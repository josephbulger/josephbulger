---
layout: post
title: "Some New Topics"
date: 2019-02-18 13:40:00 -0400
comments: false
categories: [aws]
---

I've been spending a lot of time lately working around Infrastructure Architecture. Not only have I been able to pick up and learn some new concepts, but in a lot of ways it's also really helped me learn new application architecture concepts.

<!-- more -->

About a year ago, I started looking at [Fargate](https://aws.amazon.com/blogs/aws/aws-fargate/) at work and began adopting it as a container platform. There are a lot of benefits to using a platform like this, and I won't be able to get into all of them here. I would, however, like to share some of the key takeaways I have had with it so far.

First thing is, I really like it as a container solution for teams that don't want, or have the capability to, operate their own infrastructure. I'm not going to say they get to have a running system for *free*, but it is **significantly** easier to operate Fargate than it is other container PaaS offerings in my experience.

Second thing is, the level of control you get is really good. I usually start teams off with 256 CPU units and 512 MB RAM. Once they get their system up and running they can use CloudWatch Metrics to rightsize their containers. Also keep in mind that with Fargate, you're allocating CPU and memory at the container level, and not the host, because you don't manage the host. One final piece of advice is make sure you keep an eye on your allocation. Not all CPU / memory [combinations](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-cpu-memory-error.html) are valid. If you need a **lot** more memory, then you may have to increase your CPU, or vice versa.

Lastly, it can ***save*** you money. Most of the time, folks think that it's going to end up costing you more, but you save time. And while it is true that you will save operational time for you team, there is also a very real possibility that you can save money on your footprint costs too. This is because you are paying for your containers, but you aren't paying for any hosts. When you pay for hosts, you have to overallocate, but with Fargate, you can just leverage the containers autoscaling and you won't need to worry about your hosts allocation. In a typical scenario that we have where I work, you could easily see a team allocate double their needed host allocation, or even triple or quadruple, depending on traffic patterns.

If you're curious how we do our IAC where I work, check out these repos:

[https://github.com/turnerlabs/fargate](https://github.com/turnerlabs/fargate)

[https://github.com/turnerlabs/fargate-create](https://github.com/turnerlabs/fargate-create)

[https://github.com/turnerlabs/terraform-ecs-fargate](https://github.com/turnerlabs/terraform-ecs-fargate)