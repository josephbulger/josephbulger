I have kind of a standard approach on building out a cloud presence that I’ve developed over the last decade or so. I’ve used it extensively with multiple teams to create a highly mature DevOps culture within groups again and again. Today I’m beginning a series that explains some of the basics. Hopefully it can be of some use to those trying to understand good ways to leverage cloud technologies.

## Any Cloud Will Work

I’m going to start this series off with examples of how to deploy the concepts I’m talking about in AWS, but the approach I use works with a lot of cloud solutions out there, including Azure and GCP. I will do my best to call out how the approach would work in the Big 3 where it’s appropriate, but the topic here is not about showing you a detailed solution of a tech stack, but rather the strategy behind the build out and how to make the best decisions when it comes to rolling things out. And speaking of decisions, when it comes to adopting a cloud approach, the very first thing your teams need to understand is containerization. The need to learn Docker.

## Learn Docker

Docker is the gateway to understanding what containerization is all about. Not only is it a containerization solution itself, but the concepts your team learns from understanding it will help them navigate the massive forest of different compute options they will have across any cloud vendor. Without starting here you will never be able to see the forest through all the trees being thrown at you. Options range from AWS ECS, to Fargate, or EKS, or Lambdas, and that’s just one cloud vendor, so trust me and start here. I’m not going to get into the specifics of all things Docker, but I do want to highlight some of the learnings I’ve made over the years.

### Organize your images

When you are just beginning to learn what containers are this won’t make a lot of sense, but after you have your first image working and you actually have a system working inside a container, you should stop and think about organizing your image builds. What this means is not only keeping them logically organized so your team can understand how to use them efficiently, but also layering them together so you can reduce your build times significantly where you have a lot of commonality across systems. Let me show you a simple example.

#### The Starting Point

Once the team knows how to build docker images and can reliably run them, I start introducing a pattern that allows them to create layered images that use each other instead of creating separate duplicate builds that make your CI/CD process take forever to finish. This begins by identifying your base starting point and branching off from there. Let’s say your team needs to build a web app and a background service, but they plan to use nodejs for both. Start by making a docker compose yml that will simply define the common base both of them will use. For example:

##### docker-compose.yml

```yaml
version: "2"
services:
  how-to-cloud-base:
    build: .
    image: how-to-cloud:base
```

##### Dockerfile

```Docker
FROM node:alpine
```

The base image right now is mind numbingly simple. The docker compose yml just builds the Dockerfile below and then tags the image with a base tag. The Dockerfile doesn’t really do anything other than grab the alpine distro of node. That’s how it starts. Later your team will realize there are commonalities that both systems share and they can “bubble up” those things into the base image when they realize it. Now that you have the base the team can use it to create the web app and the background service.

Let’s build out the web version first…

##### docker-compose.web.yml

```yaml
version: "2"
services:
  how-to-cloud-web:
    build:
      context: ../
      dockerfile: ./docker/Dockerfile.web
    image: how-to-cloud-web:latest
```

##### Dockerfile.web

```Docker
FROM how-to-cloud:base

COPY ./web /app

WORKDIR /app

CMD npm start
```

and now the background service, which for now will look very similar to the web…

###### docker-compose.background.yml

```yaml
version: "2"
services:
  how-to-cloud-background:
    build:
      context: ../
      dockerfile: ./docker/Dockerfile.background
    image: how-to-cloud-background:latest
```

##### Dockerfile.background

```Docker
FROM how-to-cloud:base

COPY ./background /app

WORKDIR /app

CMD npm start
```

#### Clarifying Points

One thing that’s worth pointing out here is that the web app and the background service function completely independently. Meaning one system does not in any way depend on the other from a deployment perspective. If they did you there are other ways to deal with that.

Another call out I want to make is that when I use this organizational method I really like to make an LTS build that has kind of a default set of what my team normally uses in the cases where they just wanna use “the normal” build and not have to have something specialized. It looks something like this.

##### docker-compose.lts.yml

```yaml
version: "2"
services:
  how-to-cloud-lts:
    build:
      context: ../
      dockerfile: ./docker/Dockerfile.lts
    image: how-to-cloud:lts
```

##### Dockerfile.lts

```Docker
FROM how-to-cloud:base

COPY ./lts /app

WORKDIR /app

CMD npm start
```

### Deployment

So with all this set up we can talk about how this all ties together in a CI/CD pipeline and why you can get some pretty significant performance gains for your build when you follow this approach. The process is separated into three main stages: build, deploy, clean. It looks something like this.

#### Build

```sh
#!/bin/bash
set -e

# build image
docker compose build
docker compose -f docker-compose.web.yml build
docker compose -f docker-compose.background.yml build
docker compose -f docker-compose.lts.yml build
```

The important thing here is that you build the base docker image first, and then build the images that depend on that base image. You can get much more elaborate with the layering of the images if you want to, but the main point here is that the more commonalities that you bring up into higher layers the faster you’ll make your build, and the easier it will be to maintain your systems across all your teams because they’ll be sharing and collaborating together instead of duplicating the same concepts in silos.

#### Deploy

```sh
#!/bin/bash
set -e

# login to ECR
version=$(aws --version | awk -F'[/.]' '{print $2}')
if [ $version -eq "1" ]; then
  login=$(aws ecr get-login --no-include-email) && eval "$login"
else
  aws ecr get-login-password | docker login --username AWS --password-stdin $ecr
fi

# push image to ECR repo
docker compose -f docker-compose.lts.yml push
docker compose -f docker-compose.web.yml push
docker compose -f docker-compose.background.yml push
```

After the images are built you can deploy them to your container registry. In AWS it looks basically like this. The AWS CLI has two versions, and both of them are pretty widely used from what I’ve seen, so I still have a check for which version the team has installed so it uses the right command, but if you know your team is going to only use version 2, you can clean that up a little bit and only use the second command to log into the ECR.

Aside from that once you have logged into your ECR it’s a simple matter of pushing all the images up. You don’t need to publish the base image because you should be building that every time your CI/CD pipeline runs, unless you have a really good reason to need some kind of available redundancy for it. From my experience that is usually over kill.

Also, in the example above we’re deploying to AWS ECR, but every cloud vendor has their own container registry. The point here is how you design the process, not the specifics of how you get the image into the registry. The process works for most if not all cloud vendors.

#### Clean

At the end of your build process you might need to do some cleaning up, so we have a third stage to handle that, but a lot of the time it’s empty because the CI/CD process is ephemeral anyway. However, if you are using external dependencies during your build and you need to clean them up after the fact, then you can do that here, or if your CI/CD process is not ephemeral then you’ll probably want to do some clean up before you call the build done.

In this set up our clean up process is empty because there’s nothing really to clean, but I’ve used this in the past under certain situations. For example, we’ve built github self hosted runner farms that required us to pre-emptively download and extract binaries because the docker build was actually failing to sign certificates, so the host had binaries we needed to clean up after we were finished with the build.

## Show me the Code

I went ahead and made a repository to showcase all the lessons I talked about here, as well as any future topics surrounding How to Cloud. If you want to see the full set up I described here head over to https://github.com/josephbulger/how-to-cloud
