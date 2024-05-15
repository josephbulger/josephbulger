Once we have our groups and users set up from our external identity provider we can move onto defining what permissions they should have. This is mostly straight forward, but we'll have to take a few detours along the way because of how groups work with the google workspace IDP.

## Permission Sets

The first thing we have to do is script out our permission sets. We will use to effectively connect IAM policies to our IDP groups. This will all be done in CDKTF.

```typescript
const set = new SsoadminPermissionSet(scope, `${name}-admin`, {
  name: `${name}-admin`,
  instanceArn: arnId,
});
```

There's not a lot to this. The ARN we are using here for the `instanceArn` actually comes from the same `DataAwsSsoadminInstances` we used to get the `storeId` [before](https://www.josephbulger.com/blog/from-scratch-iac).

## Policies

Now that we have our admin permission set, we have to actually give it an admin policy that our groups can then use.

```typescript
new SsoadminManagedPolicyAttachment(scope, `${name}-admin-policy`, {
  instanceArn: arnId,
  managedPolicyArn: "arn:aws:iam::aws:policy/AdministratorAccess",
  permissionSetArn: set.arn,
});
```

The policy here is just the AWS managed policy that comes with every account. We're ready to deploy at this point, so we need to go back into our AWS console, and reactivate the root user's access key. Remember, the last time we used it we deactivated it as soon as we were done, and we're going to do the same thing here.

With the access key active again, we can go ahead and `cdktf deploy` our stack, and we should see two more resources get created. Once it's done we go back into the console and turn off the access keys again.

## Group Assignment

With these new permissions we should be all set now to assign our groups those permissions and be on our way, but this is where we hit our first snag. We need to go into the identity center console as root and assign the group to our AWS account along with the admin permission set we just made, which we can do, but there's a problem. Because of how we had to make the groups in identity center, we aren't able to assign users to the group in the console. It has to be scripted. This is not something we should script for the root user, though, because managing permissions at this level would mean people would have to be logging into root all the time, which is a big problem. Instead, what I'm going to do instead is assign myself to the AWS account, my user was brought over automatically through the identity provider. I can set all that up in the console easily.

### Switching Users

With that set up, I now have my user set up as an admin in the AWS account. I need to quickly set up the AWS CLI and [configure it with my sso](https://docs.aws.amazon.com/cli/latest/userguide/sso-configure-profile-token.html). With that done, we're going to make a new stack that uses the admin profile to allocate infrastructure.

### Account Assignment

Now we're ready to assign the group to the AWS account, and add any users we want along the way. Since we'll be doing this as an admin user, we'll need to make a new CDKTF stack, and deploy it using the admin profile after logging in using `aws sso login`. In my case, I made an `admin` profile and configured the aws cli with it.

The first thing we'll add to the new stack is going to be the account assignment that adds the group to the AWS account with admin permissions.

```typescript
new SsoadminAccountAssignment(scope, `${name}-ga-admin`, {
  provider: config.provider,
  principalType: "GROUP",
  targetType: "AWS_ACCOUNT",
  targetId: config.caller.accountId,
  permissionSetArn: config.identityCenter.permissions.admin.arn,
  instanceArn: config.storeArn,
  principalId: config.identityCenter.groups.admin.groupId,
});
```

At this point the group is set up and ready to use. We can now go ahead and add some users to it.

```typescript
const juser = new DataAwsIdentitystoreUser(scope, `${name}-iu-joseph`, {
  identityStoreId: config.storeId,
  alternateIdentifier: {
    uniqueAttribute: {
      attributePath: "UserName",
      attributeValue: "joseph@awsdemo.com",
    },
  },
});

new IdentitystoreGroupMembership(scope, `${name}-gm-joseph`, {
  identityStoreId: config.storeId,
  groupId: config.identityCenter.groups.admin.groupId,
  memberId: juser.userId,
});
```

Remember the users are automatically syncronized from our external identity provider, so we won't be making a user. Instead, we just need to grab the one we already have. In my case I found my own account by username. There are other options too, but this one is pretty straightforward. From this point it's just a matter of creating a membership for the user to belong to the group.

## Separate Stacks

We started off by building some resources using root and then made a separate stack for things an admin can then do. However, the interesting thing about this approach is that once you have an admin that can log into the system, they also have enough permissions to do everything in the first stack we made. We used the root user to get us just far enough in our IaC build out that our admin users could take over. From this point on, we won't be needing the root user anymore, so we can go ahead and delete the access key it had.

As always, if you are interested in how everything ties together, you can take a look at the [how to cloud](https://github.com/josephbulger/how-to-cloud) repository.
