Before you can dive into deploying your application you need to create a safe environment for your application to be deployed in. This starts with understanding some basics around making good security decisions, so this next topic is about how to make a good “fence”.

First Things First

Ok, in all fairness, if you are just getting started on your cloud journey, you probably only need one vpc and one account. [Keep it simple](https://www.josephbulger.com/blog/simplicity). When you get to the point where you are going to be responsible for multiple products, then you need to potentially consider different options. Also, the “you” here is not just yourself as a leader of your team, it’s the overall landscape of your organization or company. Is your company planning on migrating multiple platforms or systems to the cloud? Then you need to read on. If they are a start up that’s literally just starting out on a single product idea, you can probably read this later, but it’s still useful to know. Either way, that’s why we need to talk about VPCs.

VPCs

In the cloud world, VPCs act as a fence. They handle a number of key technical issues related to security and network pathing that you need to be able to make quick decisions on. I’m not going to do a deep dive on all the ins and outs of VPCs, but you need to learn how they work so that you can easily choose which way to go in your organization, and with the app you are building. I plan to cover the kinds of decisions that you could make, and why you’d go one way or the other. This decision is kind of in between a hard the right call situation vs preferences. I say that because depending on decisions that your company or organization has made that is outside of your control, you will want to align your decision to the things that were already chosen for you. With that being said, since VPCs act as a fence in a lot of ways, let’s talk about the different ways that you can build that fence in your account.

One VPC per Account

This strategy will make sense for a variety of reasons. You might be leading a single team working on a single product but you own an entire account. What’s the point in having more than one VPC when you are actually only building one thing? There really isn’t a good reason to have more than one. Things get trickier when you are responsible for more than one product or platform. I say it that way because in order to build the right fence you have to start thinking about what you build as something consumed by a customer, or client. The second that your answer to that question becomes, “well I have more than one”, then I argue you have another decision to make.

Multiple Accounts or Multiple VPCs

The bottom line is that every product or platform that you own should have a fence around it. The thing is, however, that while VPCs can and do act as a good fence in a lot of situations, there are some drawbacks to having multiple products and/or platforms in the same account.

Noisy Neighbors

A really good fence isolates you from noisy neighbors. In most cases, VPCs do a pretty good job of this, especially from a networking perspective. They prevent unwanted access through the routing tables you will make coupled with the subnets that are associated to them. That’s all great. What’s not so great is what happens when one system is hogging all the allocated resources you have on your account. Oh yeah, you heard me correctly. Your cloud account has limits. AWS calls them “quotas” now, actually. You can look up the specifics of what your account has and get a report on what they currently are, too. The point, though, is that when your account has reached it’s quota on something you will be denied from allocating more of that thing for your entire account, everywhere. For example, if you have two products on your account, and one of them auto scales during a spike and uses up your quota on auto scaling groups, and then your other product sees a spike as well, guess what, that second product will bottleneck and start crashing. Nothing you can do with your VPC will prevent that from happening. If this is a significant concern for your team, and you operate in an environment, organization, or company with a lot of products and/or platforms in this manner, you will probably be better off strategically operating in a multiple account scenario.

Having said all that, there are some good reasons to own a single account and maintain good boundaries by leveraging multiple VPCs. If your company only sells one product, that’s a good sign you might be better off with one account, even if you have to deploy multiple systems. If you are in small company, odds are it’s better to have one account. What this means is you have to be aware of those service limitations, and how to deal with them correctly. You need to set up monitors to alert you when you are getting close. We have monitors set up to alert us when we get to 80% of our quota on anything. Once we get the alert we send a support ticket to our cloud vendor to increase the quotas. If you have a mature process for dealing with those situations you can deal with noisy neighbors pretty easily.

VPC for each product

If you do operate in a single account with multiple products, then you should deploy each product in their own VPC. This prevents systems from accidentally colliding with one another and becoming noisy neighbors, or teams creating security issues by not collaborating together properly. Each VPC has their own setup of networking and traffic rules, which can be managed by the team that owns the product that lives in it. Having multiple products inside one VPC probably also means you have multiple teams operating in that VPC, and that means they might step on each other’s toes.

VPC for each environment

You should also split up your environments into different VPCs. You do have multiple environments, right? We’re not going to talk through that right now, that could be it’s own blog post. For now, I’m assuming you at least have a non prod and a prod environment, which means you need to have at least one VPC for prod and another for non prod. One common pattern we’ve used it actually making an account for production and another account for all non production systems, so we’re guaranteed that no non production system could ever take down a production system for any reason. That actually segues nicely into the next thing we need to talk about.

Accounts

I guess logically it would make sense to start off talking about accounts and then get into VPCs, but I think this actually makes more sense when you are trying to think about the decisions you need to be making. It becomes a lot more clear why you need multiple accounts or not when you understand the reason why you needed the VPCs to begin with, and how their “fencing” works. The example I gave before is a good one when you want to isolate production from anything going wrong, but you still need to consider the reasons why you might want to have a single account solution vs a multi account solution. Let’s get into it.

Single Account Model

I know I just got finished talking about how important it is to isolate production from non production systems (and it is), but there are some situations where it makes sense to go single account that are worth discussing.

Innovating

If you are innovating and at the very beginning stages of whatever idea you are cooking up, there’s no reason to have multiple accounts, because you have no production ready product anyway. Don’t spend the extra money on a prod account just to prove that you can do it when you are in research mode.

That doesn’t mean you are going to be able to “flip a switch” and go from proof of concept straight to prod and making money. That’s not what I’m saying. What I’m saying is, don’t worry about tomorrow’s problems today when you have no good reason to plan that out yet, worry about that tomorrow when you have a better idea of what you are going to try to do.

Mature DevOps Culture

This is going to seem like complete ends of a spectrum, mostly because it is, but another reason why it’s feasible to only have one account is because your overall DevOps culture is extremely mature. Everyone knows how to execute well, and it’s boring. You’ll know your teams are heading down the right road towards maturity because the same teams that probably can handle operating a single account will be the same ones that beg you to isolate production. It’s a good litmus test. They understand the risks it poses having everything in one account. The decision you will have to make in this situation is largely a financial one at this point. Does it save you a significant amount of cost to merge your accounts into one? Odds are it won’t, because of the way you are billed for the services you use in the cloud. Either way, once you do a cost analysis then you’ll know which way you should go.

Multiple Accounts Model

So if your company didn’t fall into of those the situations above, then you need to go multi account. Deciding to go multi account is one thing, but knowing how to split them up is another. Let’s talk about considerations you need to make.

Separate Environments

We’ve talked at length about the production, non production split, so that should be a given at this point, but let’s also talk about some other varieties here. It’s very common to do a prod/non-prod mix, but something else to consider is how well established are your non production systems in your company? Meaning, do all of your dev systems talk to each other? All of your integration systems work well together,? Are all of your test environments playing nicely together? If you have strong synergy in your company across environments, then it makes sense to actually separate them out by accounts too. Why? because it makes establishing communications among them a lot easier. It makes the permissions model simpler, the networking easier, etc. You don’t have to worry about a dev system from one team accidentally reaching out to integration or testing and blowing something up.

Multi Tenant Platforms

When your company is large enough you are bound to have teams creating systems that are leveraged across multiple products, which invariably means they will be servicing multiple teams. If you happen to engineer as system in that manner, congratulations you’ve just become multi tenant. In this situation multi tenant platforms should have their own accounts. It creates the proper level of isolation from their “customers” so that they can properly operate in a SaaS engagement model. This is particularly important when the technology office is large enough to have multiple organizations inside it. Speaking of…

Multiple Organizations

If your technology office is large enough, you’ll have multiple organizations inside it. To keep things clean, it makes a lot of sense to have each organization own and operate their own accounts. There are exceptions to this (remember what I said about a mature devops culture), but in reality the larger the company is the more sense it makes to break out accounts to match the organizational structure of the technology office.

Allocation

As with all things cloud, once you’ve decided which way you are going to go, you need to [script it](https://www.josephbulger.com/blog/how-to-cloud-iac). So let’s talk about that.

In the example I’m doing, I’m going to build out the repository in a multi account, single vpc per account model. I’m choosing this because I wanted to do something that is slightly more difficult than a “startup situation” but not so complicated that you might get lost in the weeds.

So what does this look like? Inside our iac folder, I’ve created an account folder just for account allocation, so we’ll be making everything for the VPC in there. In the future when we need to make more than one account we can refactor this folder to accomodate. Later the systems we made will be allocated in totally different folders so we’ll have a clear separation from what we needed on the account and what we needed for each system we’re building.

```javascript
class MyStack extends TerraformStack {
  constructor(scope: Construct, name: string) {
    super(scope, name);

    const roleId = "devOps";

    new AwsProvider(this, "AWS", {
      region: "us-east-1",
    });

    new Vpc(this, "htc-vpc", {
      name: "htc-vpc",
      cidr: "10.0.0.0/16",
      azs: ["us-east-1a", "us-east-1b", "us-east-1c"],
      privateSubnets: ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"],
      publicSubnets: ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"],
      enableNatGateway: true,
    });

    const ecr = new EcrRepository(this, "htc-ecr", {
      name: "htc-ecr",
      imageTagMutability: "MUTABLE",
    });
  }
}

const app = new App();
new MyStack(app, "how-to-cloud");
app.synth();
```

I’ve left out a number of other things I’ve added to the repo for brevity. None of the other code in the account is related to allocating the VPC so it’s not relevant to this article, but if you want you can always [head over there and dive in](https://github.com/josephbulger/how-to-cloud).
