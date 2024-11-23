I tried to think of the best way to describe building an app from scratch in the cloud, but there are so many different ways you can easily get lost in all the options. So I decided I would start by showcasing what is arguably the easiest app you can make from a computing perspective. It offers the least amount of operational overhead, but at the same time, it's the most expensive option _on paper_.

# AWS API Gateway

When I think of simple I think API. That might just be because I'm so used to them at this point, but there are also some direct comparisons to other compute solutions which we'll get into later in other posts as we get into more complex cloud compute ideas. Right now, though, we're going to use AWS API Gateway to deploy the first version of the API we're going to make for the rest of this series. This is a great choice if you want to spend as little time maintaining your gateway as possible. There are no load balancers to deal with. You can optionally put an edge cache (CloudFront) in front of it easily, and it connects to other serverless technology in AWS seamlessly.

## Setting up the gateway

For the example I'm building, I'm going to actually use a terraform module that already has all the pieces we need. The first thing we need to do is add the module to our project by editing the `cdktf.json` file to include the module.

```typescript
"terraformModules": [
 {
    "name": "apigateway",
    "source": "terraform-aws-modules/apigateway-v2/aws",
    "version": "~> 5.2.0"
 }
],
```

Later on, we're going to add another module but for now, that's all we need to build the gateway.

## Defining the gateway

I'm going to make a `serverless` folder and put an `api.ts` to hold all the work we'll need to get the app up and working. Inside our class, we have to import the module we made and then create the gateway.

```typescript
import { Apigateway } from "../.gen/modules/apigateway";
```

```typescript
let gw = new Apigateway(scope, `${name}-gw`, {
  name: `${name}-gw`,
  protocolType: "HTTP",
  corsConfiguration: {
    allowHeaders: [
      "content-type",
      "x-amz-date",
      "authorization",
      "x-api-key",
      "x-amz-security-token",
      "x-amz-user-agent",
    ],
    allowMethods: ["*"],
    allowOrigins: ["*"],
  },

  createDomainName: true,
  createDomainRecords: true,
  createCertificate: true,

  domainName: config.domain,
  hostedZoneName: config.hostedZone,

  routes: {
    "ANY /": {
      integration: {
        uri: lambdaArn,
        payload_format_version: "2.0",
        timeout_milliseconds: 3000,
      },
    },
    $default: {
      integration: {
        uri: lambdaArn,
        payload_format_version: "2.0",
        timeout_milliseconds: 3000,
      },
    },
  },
});
```

In my case, I already had a hosted zone set up in my AWS account for a subdomain `aws.josephbulger.com`. I decided to go ahead and add this gateway to that so the hosted zone in this example is `aws.josephbulger.com` and the API's domain is [apigw.examples.how2cloud.aws.josephbulger.com](apigw.examples.how2cloud.aws.josephbulger.com).

You don't have to set this up with a domain. If you don't AWS will generate a random URL to host your gateway instead, and you can use that for making your API calls.

### CORS

I have the API's cors configuration set up to allow traffic from anywhere because, with this kind of setup, I'm assuming the front end that would potentially use this is not hosted by the same app. This is a common approach I use for my systems. I don't like to have the API backend be served by the same system that serves the front end. I like to keep them separate.

### Routes

Finally, you need to define the routes that the gateway will serve. In my case, I'm going to send all my traffic to a single lambda, which we'll talk about in a second. For now, we just need to know to send all the traffic over to the lambda, and let it figure out what to do with it.

#### Stages

Additionally, we have to define the default stage. Stages in API Gateway are kind of a weird concept. It's beyond the scope of what I want to cover in this post, but for this purpose let's just say you have to define a default one, so the gateway knows which stage gets the traffic. In our case, we're just going to set it up the same way we set up the route we made.

# Lambda

The lambda is the part that makes this computing solution simple and easy. It does this because it's truly serverless. We don't have to worry about running a server, neither as a virtual machine an EC2 instance somewhere, nor as compute sitting on top of an orchestration plan like ECS or k8s. We just create the app, and give it to the lambda. The largest amount of operational overhead with the lambda boils down to dealing with how much CPU and memory to allocate to it, and these days you only specify the memory because the CPU is adjusted based on how much memory you choose. The price we pay for this ease of use is, well, the actual sticker price. All things considered, you can expect to pay anywhere from double to up to 6 times the price of other compute solutions in AWS for the equivalent _compute time_, but that's on paper. In reality, there are some very compelling reasons to use Lambda, including financially.

## Setting up your Lambda

So to get using the lambda we first need to add the module into cdktf the same way we did the gateway.

```typescript
"terraformModules": [
 {
    "name": "lambda",
    "source": "terraform-aws-modules/lambda/aws",
    "version": "~> 7.14.0"
 }
],
```

## Defining the Lambda

Now we can add the lambda to our API.

```typescript
let lambda = new Lambda(scope, `${name}-function`, {
  functionName: `${name}-function`,
  createPackage: false,
  packageType: "Image",
  architectures: ["x86_64"],
  imageUri: `${config.ecrUri}:${config.tag}`,
  publish: true,
});

const lambdaArn = lambda.lambdaFunctionArnOutput;
```

We used the `lambdaArn` earlier where we defined the gateway. This is actually where it came from, as an output from the lambda module. Now the last thing we need to do to connect the two is set up the lambda's triggers. The trick to them is that the lambda needs to know about the gateway, but we have to define the lambda first. What we do to get around this is just define the triggers **after** we make both the lambda and the gateway and then add the triggers back into the lambda.

It looks something like this:

```typescript
let apiExecArn = gw.apiExecutionArnOutput;

let triggers = {
  APIGatewayAny: {
    service: "apigateway",
    source_arn: `${apiExecArn}/*/*`,
  },
};

lambda.allowedTriggers = triggers;
```

Now that the lambda is connected to the gateway we need to create the app that the lambda is going to run.

# Docker

I created a small server in `examples` that creates a simple go web server built using docker. What's nice about Go is that you can actually deploy it from a scratch image so the overall size of the image is really small (~8 MB). Perfect for running on a lambda.

The docker file looks something like this:

```docker
# syntax=docker/dockerfile:1

FROM golang:1.23 AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY *.go ./

ARG TARGETARCH=amd64
ENV GOARCH=$TARGETARCH

ARG TARGETOS=linux
ENV GOOS=$TARGETOS

RUN CGO_ENABLED=0 go build -o /api

FROM scratch

COPY --from=builder /api /api

EXPOSE 8080

ENTRYPOINT ["/api"]
```

We'll be building it using `docker buildx bake` so we need to set up a bake file too:

```hcl
group "default" {
 targets = ["api"]
}

variable "IMAGE_URI" {
 default = "api"
}
variable "IMAGE_TAG" {
 default = "local"
}

target "api" {
 dockerfile = "Dockerfile"
 context = "."
 tags = ["${IMAGE_URI}:${IMAGE_TAG}"]
 platforms = ["linux/amd64"]
 args = {
 TARGETARCH = "amd64"
 TARGETOS = "linux"
 }
 attest = []
 provenance = false
}
```

## Provenance

One issue with deploying to lambdas, at least for now, is that they don't support multi-architecture docker images. So when we deploy our image to ECR we have to disable provenance so docker knows to only build the image in the format we specify with multi-mode turned off.

That's why in our GitHub action we turn provenance off when we do the docker build and push:

```yaml
- name: Build and push
  uses: docker/bake-action@v5
  with:
    push: true
    workdir: examples/api
    provenance: false
  env:
    IMAGE_URI: [[ECR_URL]]/api
    IMAGE_TAG: ${{ github.ref_name }}
```

# That's It

Now you have a fully functioning API. You can see the one I've deployed at http://apigw.examples.how2cloud.aws.josephbulger.com/. There's also a route you can play around with the changes in the response based on what you give it: http://apigw.examples.how2cloud.aws.josephbulger.com/some/kind/of/trick.
