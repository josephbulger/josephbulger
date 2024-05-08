Ok at this point we are now ready to get into deploying your app into some kind of compute solution in your cloud provider. In order to make the best decisions when it comes to choosing said compute solution, we need to start with the foundations of almost all of them: virtual machines.

## Cornerstone of Compute

Virtual machines will make up the cornerstone of all compute solutions you will build on top of, whether you have control over them or not. In fact, that’s one of the decision criteria you should consider when you get to choosing one, but that’s for another post. For now, what’s important is understanding the ins and outs of how your cloud provider allocates and operates their VMs, and how those aspects of your cloud provider effect the higher level compute solutions they all offer.

## Bare Metal

Virtual Machines (VMs) are probably the closest to the metal kind of solution that you can leverage with a cloud provider. AWS calls this offering AWS EC2, Azure just calls them Azure Virtual Machines, and GCP calls them Compute Engine. They all end up doing the same thing in the end. You request for a certain configuration of machine, and after some time (usually a few minutes), you can have a machine spun up that closely mimics an on prem hypervisor. You can almost, for all intents and purposes, do the same thing to this machine that you would do with one mounted in a data center. That means you have the highest degree of control on this machine that you can possibly have in your cloud provider. You can mount disks to them for storage, you can configure them with GPU, SSD state storage, basically set up any kind of compute or data storage solution you want.

This also means, however, that you have the least amount of operational efficiency by leveraging a virtual machine. You have to keep the OS up to date from a security perspective. You have to maintain the overall health and state of the machine. You have to connect that virtual machine to all the other offerings of your cloud provider to create an overall solution. There is a lot of extra work you have to put in to make that virtual machine do what you want it to do. The cloud provider is really only giving you one thing you didn’t have before, and that is rapid availability.

## Cost Model

This is where things might get a little tricky. On paper, VMs look to be the cheapest of all compute solutions. However, if you are using VMs as the only element in your compute solution, you are going to have significant labor costs. In fact, if you don’t add other aspects to your compute solution, like serverless functions, or some type of orchestration plane, you will end up operating the most expensive solution you could have built once you consider the total all in cost. VMs are still crucial to understand, however, because they feed into all the other compute solutions you can use. This will ultimately boil down to trades off between labor costs or operational efficiency, and operational flexibility or control. We can get into those specifics more in other posts when we talk about the specific solutions that are offered and how they effect those trade offs.

To keep things simple for now, let’s just do a basic run down of the cost structure for VMs by themselves. It will serve as a fraction of the overall cost to build your solution, and in a lot of ways it makes up the foundation of your cost model.

### Examples

#### AWS

Amazon offers a variety of ways to rent their VMs. The most straight forward (and expensive) one is just using their On Demand allocation. Prices vary depending on your configuration of CPU, Memory, GPU support, storage, etc, and I won’t be going into the details of how they effect cost, but it’s important to note that On Demand pricing effectively influences all other pricing.

There is also Reserved pricing, which allows you to actually reserve a certain level of allocation in 1 or 3 year agreements. That may sound confusing, but it’s important to note in this pricing model that you aren’t renting a specific VM for a specified time. It’s more like you are renting the time for that configuration, and that agreement nets you some cost savings. Having said that, there are some limitations. There are two ways to purchase a Reserved instance, Standard and Convertible. Standard rental allows you to modify certain aspects of the reservation like availability zone, networking type, instance size, but you can’t change other things such as the instance type. You can, however, sell that rental license on the AWS marketplace. Then there is convertible. Convertible agreements allow you to exchange one instance for another. You can do this as many times as you like but the value of the overall exchange has to be equal or greater than the agreement you put in place to begin with. Convertible rentals can not be sold on the AWS marketplace. If you choose either of these styles of pricing, you can get upwards of a 75% reduction in price from AWS for the equivalent machine with on demand pricing.

Finally we have Spot pricing. Spot pricing doesn’t require a termed agreement like reserved pricing, but it can still afford you similar savings. It achieves this through one major stipulation: AWS may choose at any time to terminate your instance with only a two minute warning. This can sound devastating if your cloud solution relies solely on VMs, but, as we will discuss in later posts, layering an orchestration platform on top of your rented VMs can make this an extremely appealing option. With this type of set up you can achieve savings up to 90% of what you would have spent on an on demand instance.

#### GCP

You’re going to start to see a lot of repetition amongst cloud providers. What GCP offers is extremely similar to AWS, and largely the only differences are in names.

GCP’s main offering is largely referred as on demand or “pay as you go” pricing. It’s akin to AWS’ on demand pricing, and the structure of offering has a lot of similarities. From there, GCP offers “commitment” pricing that is similar in principle to AWS’ reserved pricing. In addition to this offering, GCP will also give discounts for what they call “sustained use” discounts (or SUDs). SUDs can give you a 30% discount if you haven’t received some other form of discount and your compute has been running for at least 25% of the billable month. GCP also offers Spot or Preemptible VMs, and as you’ve probably guessed they function in a very similar way to what AWS Spot instances offer. With GCP spot instances you can achieve savings upwards of 90%.

#### Azure

We’re going to do the big three and then we’re going to stop. I wanted to get all three laid out so you can see the pattern. This will effect your decision process later when choosing your solutions, and it’s important to understand that all cloud providers should (most do) offer you the same cost savings models.

Azure’s VMs can be rented under a Pay as you Go model, a Savings Plan, Reserved Instances, and finally Spot models. Pay as you Go is the same thing as On Demand. Reserved is the same as before. Spot as well. Savings Plans are worth pointing out. They allow you to lock in an agreed to hourly price for a variety of services Azure offers for a period ranging from 1 to 3 years. In order to get those reduced prices, however, you have to agree to a certain level of consumption during that time period.

#### Get Containerized

Developing the right compute solution is a series of related decisions. It starts with understanding how to choose the right VM structure. We’ve already talked about containerization, so the next thing we’ll go over will be how to tie together containerized applications with the foundation of virtual machines we just went over.
