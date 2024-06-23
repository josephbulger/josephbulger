WIth our [OIDC provider configured](https://www.josephbulger.com/blog/from-scratch-oidc-providers), we can start setting up our devops build process. This will ensure that everyone on the team follows the same process when deploying infrastructure for anything built in our account.

# Configuring Backends

Up until now all the work we've done has been provisioned into the our cloud account by running CDTKF locally. However, when we run this using Github Actions, we're going to need those runners to be aware of the same terraform state that we have locally on our machine. Before we can do that, we need to define the remote backends we want to use.

## S3 Backend

I'm going to use AWS S3 for my remote backend. It's pretty straightforward to set up. The first thing I need to do is provision an S3 bucket to hold all my state. I'm going to write a `Backend` class to hold the entire setup.

```typescript
export class Backend extends Construct {
  arn: string;
  name: string;
  constructor(scope: Construct, name: string) {
    super(scope, name);

    const bucket = new S3Bucket(scope, `${name}-dbb`, {
      bucketPrefix: "devops-backend-bucket",
      lifecycle: {
        preventDestroy: true,
      },
    });

    new S3BucketVersioningA(scope, `${name}-dbb-v`, {
      bucket: bucket.id,
      versioningConfiguration: {
        status: "Enabled",
      },
    });

    this.arn = bucket.arn;
    this.name = bucket.bucket;
  }
}
```

It's worth mentioning that you should change the `lifecycle` on the bucket by setting `preventDestroy` to true, which will prevent anyone from inadvertently deleting this bucket when they don't mean to. Also, I highly recommend turning versioning on, this can save you if you accidentally lose track of your state, or get a conflict, and need to roll back.

With the backend defined, I need to choose where to provision it. I will eventually have three stacks in total during this series: `root`, `account`, and `devops`. I don't want to put any more infrastructure in the `root` stack, because we only wanted to use that stack to get thing started. Continuing to use that stack would mean only users with the root account information would be able to provision this backend bucket. That's definitely not a good idea. The `devops` stack is going to act as a concept for what one typical team would build for an app or system. The issue with putting the backend in the devops stack is then every team would have their own buckets, and while that's ok, it can be **really** cumbersome and a pain to track. The `account` stack offers the best balance I've found of keeping things simple but not elevating the infrastructure too high, permissions wise, where you can run into security bottlenecks or concerns.

With that being said, we'll provision the bucket inside the account stack like this:

```typescript
export class AccountStack extends TerraformStack {
  constructor(
    scope: Construct,
    id: string,
    config: { identityCenter: IdentityCenter; githubOrg: string }
  ) {
    super(scope, id);

    const provider = new AwsProvider(this, `${id}-provider`);

    this.devOpsBackend = new Backend(this, `${id}-backend`);
  }
}
```

Before we can use the backend, we need to deploy this change to our account one final time locally so that the bucket can get created: `cdktf deploy 'widget-factory-account'`. Once that is provisioned, we need to grab the bucket name so we can use that to set up the backends. You can do this by adding a terraform output to the account stack, or just go find it in the AWS console. I just grabbed the values from my console since I wouldn't need the output again once I had it. Now we're ready to change the backends of all our stacks to S3. I'll add the backends to the `main.ts` file after the stacks are defined so all the state can be captured in the same place.

```typescript
const backend = "devops-backend-bucket-[junk-prefix-nums-autogenned-by-AWS]";

new S3Backend(accountStack, {
  bucket: backend,
  key: "account",
});

new S3Backend(rootStack, {
  bucket: backend,
  key: "root",
});

new S3Backend(devOpsStack, {
  bucket: backend,
  key: "devops",
});
```

The bucket is set to the backend we just provisioned. The key is the name of the state file that will be allocated for that stack. I'm going to go ahead and make a simple devops stack now so I can create it's backend here as well. Before we can deploy this we need to [migrate the state](https://developer.hashicorp.com/terraform/cdktf/concepts/remote-backends#migrate-local-state-storage-to-remote) from the `root` and `account` stacks into the S3 backend.

# Migrating State

In order to migrate your state you'll need to open up a terminal and run a couple `cdktf` commands. First, let's migrate the `root` stack.

```bash
cdktf diff widget-factory-root --migrate-state
```

If it works correctly, you should see a message asking you if you want to proceed with migrating from your local state into S3. Once we proceed with that, then you should get a success message saying that your state has been properly intialized.

Now let's do the `account` stack.

```bash
cdktf diff widget-factory-account --migrate-state
```

Same thing here. Once you have migrated both of these stacks, you should be able to see both state files in the S3 bucket in your AWS console. I would do a quick check here before proceeding further.

# Setting up the Deployment in Github

Now that the backend is all set up, we're ready to actually deploy the new devops stack we made. In my case, I'm just making a simple stack that has an ECR in it because I know I'll need one later.

## Devops Stack

It looks something like this:

```typescript
import { EcrRepository } from "@cdktf/provider-aws/lib/ecr-repository";
import { AwsProvider } from "@cdktf/provider-aws/lib/provider";
import { TerraformStack } from "cdktf";
import { Construct } from "constructs";

export class DevOpsStack extends TerraformStack {
  constructor(scope: Construct, id: string) {
    super(scope, id);

    new AwsProvider(this, `${id}-provider`);

    new EcrRepository(this, `${id}-test`, {
      name: "testing",
    });
  }
}
```

## Github Actions

Ok now we're ready to wrap this up. Let's change the iac workflow we made when we set up our [OIDC providers](https://www.josephbulger.com/blog/from-scratch-oidc-providers) so that it actually deploys our stacks now.

We're going to change this:

```yaml
- name: Proof of Life
    run: |
        aws iam list-roles
```

to this:

```yaml
- name: Run Terraform CDK
    uses: hashicorp/terraform-cdk-action@v4
    with:
        cdktfVersion: 0.20.7
        terraformVersion: 1.8.3
        mode: auto-approve-apply
        stackName: widget-factory-devops
        githubToken: ${{ secrets.GITHUB_TOKEN }}
        workingDirectory: ./aws
```

This will deploy the new `devops` stack we just made. We can also add more steps to deploy all the `account` stack as well doing the same thing but just changing the `stackName` to match. I'm not going to add the root stack in the workflow, though, because making modifications to the root stack should only be done by someone with the root credentials and we will never be sharing that anywhere in github (even as a secret).

## Another Worflow: Planning

It's also a really good idea to set up a workflow that let's your team see the terraform plan before a deployment is actually kicked off. Luckily, this is really easy to do since the `hashicorp/terraform-cdk-action@v4` action provides us with a mode to do that in.

```yaml
- name: Plan
    uses: hashicorp/terraform-cdk-action@v4
    with:
        cdktfVersion: 0.20.7
        terraformVersion: 1.8.3
        mode: plan-only
        stackName: widget-factory-devops
        githubToken: ${{ secrets.GITHUB_TOKEN }}
        workingDirectory: ./aws
```

# Up Next

Now that we have our build process in place, we're ready to start building some apps.
