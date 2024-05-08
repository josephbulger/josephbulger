In my last post I intentionally jumped the gun a little bit when it came to deploying containers into your cloud account. It should be your first priority to get your team to learn containerization, but deploying to the cloud requires allocating container registry infrastructure. That brings us to our next topic which is Infrastructure as Code, and it’s a very close second in priority.

## Don’t do it manually

Do not, under any circumstances, try to allocate infrastructure in your account manually through the console. I have tried to think of any situation where it makes sense to just use the console, but there really just isn’t one unless you are truly just toying around. Any product you intend to build and make money on, you can’t afford the windfall of problems you will encounter if you go down that path. Allow me to highlight a couple of these just to give you an idea.

### Your team will forget what they were doing

It may seem like it’s faster to just jump into the console and allocate something, especially like a container registry, really quickly and just get it over with. After all, it’s so easy. 6 months later, your team has forgotten how many things they allocated manually, and they don’t remember where to find them, and all of the sudden you are going to have to build it all over again because it’s actually lost in your account and there’s too many other things in your account to find the one thing you need to find. Having your infrastructure written in code completely solves this problem.

### Fear of making changes

Even if your team can find the infrastructure they made 6 months ago, they will be terrified to change it, because they won’t be able to easily tell what depends on it, and how it depends on it. Their solution will just be to make another one while keeping the original around for legacy reasons, and your cloud bill cost will suffer the consequences.

### Speed of Delivery

The reality is that, if you are using the console, you think you are going faster, but you are actually going at a minimum 4 times slower than the way I do it. I know this from experience. I’ve watched other teams that did it manually because they were convinced they could get it done quicker. They took a month and had nothing to show for it because everything kept breaking, and my team came in and got their entire account working from scratch in 1 week using scripts. Allocating an entire compute cluster stack for my teams today takes less than an hour, from start to finish. I’ve never seen a team allocate a web service on any compute cluster cloud product in the console in that time frame. You think you’re going faster in the console because of an illusion. That illusion is the self gratifying feedback loop that the console gives you from seeing a little bit of progress every 10 minutes or so. It is a complete falsehood.

## Knowing what’s necessary and what’s flavor

Before I get into the next steps for IaC, I want to take a second and point something out that’s really important for decision making as a leader. You need to be able to tell the difference between what is the right call vs what’s just your own preference or the team’s preference. What I said earlier about manual vs scripting? That’s the right call. You want to make sure that’s something you handle at your level as a leader. Which tech stack you choose to do the scripting? That’s preference. You want to help facilitate your team into making the best decision here, but I like to leave this area up to my teams, collectively. Empower them to do this for themselves and work together on this decision, not in silos for those of you that manage multiple teams. There are, of course, pros and cons to what you choose, and some situations will call for one solution more than another, but you can be successful using a lot of different tech stacks.

In the realm of IaC you have a few options. I’ll cover the main points how I see them today and explain why I choose what I choose, but that doesn’t mean it’s going to be the right choice for your team. This is the preference part. You need to choose what’s right for your situation, and you need to get good at making that choice quickly.

### AWS CLI

The OG for AWS IaC scripting. It was the best and most comprehensive solution for allocating AWS infrastructure for a long time. It isn’t hosted inside AWS, however, because it’s just a cli you run yourself, but it can be extremely versatile. It used to be that AWS would roll out commands to the cli before any other IaC solution so you had to leverage it for the newly released tech on your account.

### AWS CloudFormation

The first true IaC language developed by AWS. It’s declarative, runs inside your AWS account which is great for tracking and maintainability. CF was always lagging with features in the past because the cli would get them first, but AWS has done some work to try to close that gap so it may not be that much of an issue anymore. Being declarative has it’s drawbacks, though.

### Azure ARM

For those of you that use Azure, ARM is the equivalent tech for that cloud vendor.

### AWS CDK

Which is why AWS made a new language for IaC called AWS CDK. This is the next evolution in AWS’s offering for IaC. It is programmatic, getting away from being declarative, which allows for much more flexibility in defining your infrastructure. It is, however, still vendor locked into only leveraging AWS cloud technology. If you are 100% using only AWS Cloud offerings, then any of AWS’s options should be fine, including CDK, but before you make that choice let’s talk about what I mean by 100%.

### Terraform

Terraform offers two varieties of IaC, one declarative and one programmatic, but the main distinction that Terraform has over the AWS offerings above is that it’s built to be multi-cloud, multi-provider. Let’s talk about what multi-provider actually means.

Most people getting into the cloud and learning about IaC will latch onto the idea of scripting out whatever they are building in their cloud account of choice pretty quickly. It’s a pretty natural process. What will happen to a lot of teams, however, is that you will come to realize that you actually purchase or own licenses for all kinds of other services you need to build the product your team is focused on. Things like…. maybe you use fastly as a cdn provider, MongoDB Atlas for a database solution, or datadog as your observability platform. Now, you may be asking yourself, why do any of those things matter? Well, with terraform, all of those products are connected in the terraform ecosystem as providers. So you can script one IaC solution together that ties all of those things, including your cloud account infrastructure, all in one place. So when I was talking earlier about 100%, I was talking about everything your team uses.

Point of note, I didn’t really bring up GCP anywhere in this discussion, but that’s because they leverage APIs for a lot of their allocation, and those APIs feed into terraform providers so if you are in GCP land you are probably looking to adopt terraform. In a lot of ways, the same may be true for folks looking to adopt Azure, even though they have their own templating stack. Terraform is a choice my teams tend to make these days because of all the flexibility I mentioned before. It tends to be the selection of choice, although I have managed teams in the past that used AWS CloudFormation extremely successfully.

## Getting back

Ok so getting back to the example we started, let’s now talk about how you actually create that infrastructure you need for you container registry. My teams use Terraform CDK so my examples will be with that, but our approach translates well to whatever choice you make. And again, I’m not going to get into the details of how to do all things terraform, rather I want to point out some key decisions that you want to make when you start building out your IaC.

### Organizing your stack

When we start this build out you are going to want to start organizing where you put your scripts, and from my experience you want to immediately split up infrastructure you make for your account, and what you make for the product you are deploying, and their subsequent environments. Things you need to do in your account would be things like allocating your VPC, networking, domain management, stuff that you don’t normally handle on a product by product basis. For this example I’m going to actually put my ECR allocation at the account level because I’m going into this with the mindset that the overall stack here is going to be rather small, so allocating an ECR for each product is probably overkill. Think the difference between a start-up vs an enterprise corporate team. This stack is more of a start-up scene right now, whereas in a more enterprise environment you might allocate an ECR for each product, or maybe each team.

This is what allocating the ECR looks like right now in my repo, it’s nothing fancy:

```javascript
import { Construct } from "constructs";
import { App, TerraformStack, TerraformOutput } from "cdktf";
import { AwsProvider } from "@cdktf/provider-aws/lib/provider";
import { EcrRepository } from "@cdktf/provider-aws/lib/ecr-repository";

class MyStack extends TerraformStack {
  constructor(scope: Construct, name: string) {
    super(scope, name);

    new AwsProvider(this, "AWS", {
      region: "us-east-1",
    });

    new EcrRepository(this, "how-to-cloud", {
      name: "how-to-cloud-ecr",
    });

    new TerraformOutput(this, "testing", {
      value: "hello world",
    });
  }
}

const app = new App();
new MyStack(app, "how-to-cloud");
app.synth();
```

The code is actually way less important here than the folder structure I’ve started. We started with setting up docker and we have that over in a docker folder, and now that we’re allocating infrastructure we put that over in an iac folder, with an account folder inside that just for the account stuff. Later we’ll make more scripts in product folders that are separate from the account, so building out new infrastructure will be really easy.

But that’s for a later post.
