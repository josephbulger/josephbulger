It occurred to me recently that it might not be obvious when someone should start using IaC. This can be daunting because there are things you can't do, and some things you shouldn't do with IaC. There are also situations where you could use it, but it doesn't lead to any benefit.

## Sign up for AWS Cloud Account

There will some some things you have to do that are probably common sense you can't leverage IaC for. [Signing up for your AWS cloud account](https://aws.amazon.com/free) is one of them. In my case I'm completely starting from scratch, so let's go through everythign involved in that. During this series, when we actually use IaC, I will be very explicit about it, so you'll know the difference between something I had to do by hand / manually vs something I scripted.

## Root Access

So you've signed up for your account, and you have your root account. For the majority of the work you'll be doing, you will **not** be using your root account. It's dangerous to rely too heavily on your root account, but there are a couple things you have to use it for, especially at the beginning.

### MFA your Root Account

I've had the privelege of working with some really talented security engineers around cloud, like [Chris Farris](https://www.chrisfarris.com/about/) for example. When it comes to cloud governance you really can't do wrong by just [doing what they recommend](https://www.primeharbor.com/blog/multicloud/). So it should come to no surprise the next few steps I'll be doing are going to essentially be doing that.

Your Root Account will be the only account that can be logged in outside your identity provider, and the account that can do the most damage if compromised, so it's critical you keep it as secure as possible.

## Setup External Identity Provider

You **really** need to connect your organization with an identity provider. Before you can do this you have to [enable Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-set-up-for-idc.html). While you go through this process AWS is going to ask you to enable Organizations as well, which you'll want to do. In my case I'm going to be doing this with google workspaces, so I just followed [this documentation](https://docs.aws.amazon.com/singlesignon/latest/userguide/gs-gwp.html) and got up and running.

### Customize the access portal url

This isn't necessary but it's a nice to have. Once you have your identity provider active, the portal to sign in can be a little obscure, but you can [change it](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtochangeURL.html) to use a subdomain of your liking, assuming it's available.

## Groups

When working with Google Workspaces, one of the limitations with the IDP integration is that workspace groups don't automatically come over like users do. To make matters even more complicated, you can't add groups using the AWS console, you have to add them through the CLI or the API. That being said, it's worth going through the trouble to make some groups anyway, because they will be useful later when we want to start giving permissions to our teams.

### Enter IaC

Now, most of the time folks will just make the groups using the CLI and call it a day, but I think this is actually a good starting point to creating your IaC footprint. This does come with some _caveats_ though, because this isn't going to follow the normal flow of scripting we will get into later after we're done using the root user.

### Scripting against the Root Account

We are going to be creating identity groups using terraform, but in order to do that we need some access keys, and they have to come from root (because we don't have any other users yet). This is something that is usually recommended **never** to do, and while I largely do agree with this practice, in this situation I argue it's actually a better practice to do it this way than to do it manually. Why? Scripting something makes it repeatable, which is less prone to error. Additionally, there are some safeguards we'll be putting in place so that the root user doesn't get compromised. I can't tell you how many times I've set something up without IaC, and then a month later I forgot what I did, which caused me or my team a **lot** of headaches. It also creates a less secure situation because you now have to circle back on what you did and rethink everything, which can introduce errors and misteps.

So we're going to make some access keys, but we're going to immediately **deactivate** them. Why? Because we haven't written our scripts yet, and we're not going to leave anything to chance when it comes to the root account.

## Show me the code

I'm going to be building a solution around this using [CDKTF](https://developer.hashicorp.com/terraform/cdktf) but you could solve this with other scripting tools as well.

To remedy this group situation is actually pretty easy and doesn't require a lot of code. We're going to define a construct to hold onto all our org's groups. We'll deploy this once and shouldn't need to revisit this again, which will be a common pattern for anything we script using the root user.

```typescript
export class IdentityCenterGroups extends Construct {
  constructor(
    scope: Construct,
    name: string,
    config: {
      provider: AwsProvider;
      org: string;
    }
  ) {
    super(scope, name);

    const stores = new DataAwsSsoadminInstances(
      scope,
      `${name}-stores`,
      config
    );
    const storeId = Fn.element(stores.identityStoreIds, 0);

    new IdentitystoreGroup(scope, `${name}-admins`, {
      identityStoreId: storeId,
      displayName: `${config.org}-admins`,
      provider: config.provider,
    });
  }
}
```

Here I'm just making an admins group, but you would also add others like `developers` or `security` based on whatever needs you have. This is important because later we can give these groups specific permissions.

Finally we need to add this construct to our stack and deploy it.

```typescript
class RootStack extends TerraformStack {
  constructor(scope: Construct, id: string) {
    super(scope, id);

    const providerAsRoot = new AwsProvider(this, `${id}-provider`, {
      profile: "root",
    });

    new IdentityCenterGroups(this, `${id}-identity-center`, {
      provider: providerAsRoot,
      org: organization,
    });
  }
}

const app = new App();
new RootStack(app, `${organization}`);
app.synth();
```

Before we can deploy it, we need to go back into our AWS console and activate the access keys for our root account. Now we can deploy this with `cdktfy deploy` and we should immediately see the groups show up in Identity Center. After we're verified the group was created, immediately go back to the access keys for root and deactivate them.

## What's Next

We have our AWS account created and connected to our identity provider of choice. That Identity Provider isn't configured to provide access into our AWS account yet, however, because we haven't configured permissions yet. That's what we'll get into next. It's also worth pointing out that, even though we've been doing everything using the root user up until this point, our goal is to do **as little as possible with this user**, so once we've finished the bare minimum with root, we'll be switching over to an identity user for everything else. We're just not to that point yet.

Also one last thing for reference because I get asked this a lot: it took me about 3 hours to do everything in this article **and** write the blog article. If you are wondering how long it takes to do IaC properly, it really doesn't take that long. If you're curious exactly what I built for this set up, you can check it out in my [how to cloud](https://github.com/josephbulger/how-to-cloud) repository in the aws section.
