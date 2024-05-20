It's time to divert our attention to what will soon become our infrastructure deployment process. This will feel a lot like application deployments, but there will be some stark differences we'll be going over. Our applications will incorporate infrastructure in the future, but not everything we do with our IaC will be related to an application.

# Github Actions

I'll be deploying my infrastructure through Github Actions because it's where I keep my repositories anyway, and keeping the deployment scripts there is really convenient and makes tracking things really easy too.

## The Github and AWS Dance

If we're going to be deploying infra from within github actions, it stands to reason we'll have to give our workflows permission to deploy infrastructure in our AWS account. That's where OIDC comes in. AWS allows you to build a trust relationship with an OpenID connected provider (in our case github), and then define a permissions policy to a role in your account. Once the role is defined, you then have complete control over what permissions you let that role have within your AWS account.

Github has some [documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) on how the whole process works.

# Creating the Provider

We'll start off by essentially following [AWS guidance on connecting with Github](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp_oidc.html#idp_oidc_Create_GitHub). In order to do that, we're going to need to grab a certificate from github to put into our provider definition.

## Certificate

Grab the certificate from github by using

`openssl s_client -servername token.actions.githubusercontent.com -showcerts -connect token.actions.githubusercontent.com:443`

The cert you want is the last one you see in the terminal. You want to create a file named `certificate.crt` and put all the cert information from the terminal which includes the `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----` parts too.

## Thumbprint

Now with the certificate, we'll need to run through a process to [grab the thumbprint](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc_verify-thumbprint.html). Open up a terminal in whatever path you made put the cert, and run the following command:

`openssl x509 -in certificate.crt -fingerprint -sha1 -noout`

The output should look something like this:

`SHA1 Fingerprint=99:0F:41:93:97:2F:2B:EC:F1:2D:DE:DA:52:37:F9:C9:52:F2:0D:9E`

You only care about the alphanumerics in the fingerprint itself, so remove the colons and you're left with something like this:

`990F4193972F2BECF12DDEDA5237F9C952F20D9E`

Depending on where your provider is, you're fingerprint will be different.

## Provider Definition

Now we're finally ready to script the provider itself.

```typescript
const oidcProvider = new IamOpenidConnectProvider(scope, `${name}-provider`, {
  url: "https://token.actions.githubusercontent.com",
  clientIdList: ["sts.amazonaws.com"],
  thumbprintList: ["1b511abead59c6ce207077c0bf0e0043b1382612"],
});
```

In the thumbprint list you'll put the fingerprint you got from earlier. You need to make sure your thumbprint is in all lowercase when you do this, because if you don't then terraform will always detect drift from the AWS provider that is provisioned since AWS will tell it it's in all lowercase.

# Trust Relationship

After we've defined the provider we have to describe the trust relationship between it and our AWS Account. This basicaly boils down to defining a policy that allows role assumption with a web identity.

The policy looks like this:

```typescript
const assumePolicy = new DataAwsIamPolicyDocument(
  scope,
  `${name}-assume-policy`,
  {
    statement: [
      {
        actions: ["sts:AssumeRoleWithWebIdentity"],
        principals: [
          {
            type: "Federated",
            identifiers: [oidcProvider.arn],
          },
        ],
        condition: [
          {
            test: "StringEquals",
            variable: "token.actions.githubusercontent.com:aud",
            values: ["sts.amazonaws.com"],
          },
          {
            test: "StringLike",
            variable: "token.actions.githubusercontent.com:sub",
            values: [`${config.githubOrg}/*`],
          },
        ],
      },
    ],
  }
);
```

The provider ARN is the provider we made earlier, and the value in `token.actions.githubusercontent.com:sub` is your github org you want to allow to provision in your AWS account. You can limit this in various ways, but in my case I just want to give the whole org the same set of permissions. Alternatively you can limit this down to specific repos, or github envs, etc etc.

# Permissions

Now that we have the provider scripted and the trust relationship defined we can turn our attention to what permissions we want to give the role. To keep things simple, I'm going to give the deployment process `PowerUserAccess`. This is a managed policy from Amazon, and I have no security concerns around the permissions it allows. If you work in a space where the security concerns need to be more limiting, this is where you could make a more restrictive policy yourself, which is also fine.

For this situation, I just need to grab the power user policy like this:

```typescript
const powerUserPolicy = new DataAwsIamPolicy(scope, `${name}-trust-policy`, {
  name: "PowerUserAccess",
});
```

# Role

None of this matters if we don't create a role that is going to use all this stuff:

```typescript
const role = new IamRole(scope, `${name}-trust-role`, {
  namePrefix: `${name}-trust-role`,
  assumeRolePolicy: assumePolicy.json,
});
```

The assume role policy here is the trust relationship we defined. Now we need to give the role power user permissions.

```typescript
new IamRolePolicyAttachment(scope, `${name}-rpa`, {
  role: role.name,
  policyArn: powerUserPolicy.arn,
});
```

# Try is Out

After we've `cdktf deploy '*'` the stack we should try this out and see if it's working. I made a really simple workflow that just configures the aws cli and then prints out all the roles in the account to make sure it has the permissions we gave it earlier and everything is wired up properly.

The workflow file looks like this:

```yaml
name: Deploy Infrastructure
on: push
permissions:
  id-token: write
  contents: read
jobs:
  Deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Git clone the repository
        uses: actions/checkout@v4
      - name: configure aws credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: [arn:aws:iam::therolearn-i-just-made]
          role-session-name: githubsession
          aws-region: us-east-2
      - name: Proof of Life
        run: |
          aws iam list-roles
```

After that it's just a matter of cleaning things up a little bit and push main up to github, which triggers the workflow to run, and I'm able to see the github action produce a list of the roles in my account.

# Next Steps

We're now ready to start letting Github Actions handle our infrastructure deployments. That's going to involve changing a few things we already did and then making some more workflows in our repo, but that's for next time.
