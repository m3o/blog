---
layout: post
title:  "Why and how we built Distributed with Next.js and Micro"
date:   2021-03-16 10:00:00
---
<br>
[Distributed](https://distributed.app) is a live social chat app built as a Jamstack demo using [Next.js](https://nextjs.org/)
and [Micro](https://micro.mu). We built it to demonstrate the value proposition of [M3O](https://m3o.com) - a cloud platform for API development. 
This post explains what went into building Distributed in just a few weeks and how M3O helped rapidly build our MVP.

<center>
  <a href="https://joindistributed.com">
    <img src="{{ site.url }}/assets/images/distributed.png" style="width:100%;height: auto; border: 1px solid #eee; padding: 10px;" />
  </a>
</center>
<br>
You can find the source code for distributed on [Github](https://github.com/m3o/distributed). If you want to build and host your own 
version signup to [M3O](https://m3o.com) and start running the same services from our open source repository 
[micro/services](https://github.com/micro/services).

## Why we built Distributed

Distributed was built as a jamstack demo to show how you could leverage [M3O](https://m3o.com) as an API backend for rapid MVP 
development. M3O itself is a cloud platform for API development, built on the popular open source project [Micro](https://micro.mu). 
Micro enables you to quickly build APIs in Go on the backend and M3O provides simple free hosting of those services.

We wanted to show the Jamstack audience how you could quickly leverage those APIs to build something on the frontend. Not only that, 
we really wanted to understand and experience the frontend developers perspective through dogfooding of our own APIs rather than just 
throwing stuff over the wall and hoping it works.

Hopefully in that we we've done is demonstrate the value of our platform and how others can also make use of it with a real world 
app like Distributed to learn from. Let's talk more about Jamstack now.

## Jamstack Development

Jamstack is a new architecture pattern for frontend which offloads dynamic aspects of the stack to javascript and third party APIs. 
[Vercel](https://vercel.com/), the makers of [Next.js](https://nextjs.org/) and related companies are pioneering the way forward 
for jamstack development.

<img src="https://d33wubrfki0l68.cloudfront.net/b7d16f7f3654fb8572360301e60d76df254a323e/385ec/img/svg/architecture.svg" />
<center><i><small>Credit jamstack.org</small></i></center>
<br>

JAMstack stands for Javascript, API and Markup. The static part of the application is deployed to a CDN with javascript 
dynamically loading various pieces of dynamic content from backend APIs and rendering it.

## Why we chose Next.js

Next.js is a massively popular react based framework for Jamstack development. When we were looking at building out a demo on top of [M3O](https://m3o.com) 
we had the choice of going down a number of routes but what really appealed to us was the how deliberate a lot of the choices were in how the Vercel 
team had constructed the Next.js framework.

<center>
  <a href="https://nextjs.org">
    <img src="{{ site.url }}/assets/images/nextjs.png" style="width:100%;height: auto; border: 1px solid #eee; padding: 10px;" />
  </a>
</center>
<br>
Being framework creators ourselves with the dominant framework [Go Micro](https://go-micro.dev) for Go, we could appreciate the efforts 
required and strong opinions needed to drive such adoption and success. Vercel has done a phenomenal job in this way.

Beyond praising Vercel's efforts. The Next.js framework includes a lot of key components needed for the Jamstack including server side rendering, api routes 
and typescript support. For us these were mandatory feature requirements when building against not only our APIs but third party providers. 

## Micro for the Backend

[Micro](https://micro.mu) is an open source cloud platform for API development. With modern day complexity in writing software for the cloud, 
Micro has attempted to distill that down to a handful of primitives and a framework for building services in Go.

<center>
  <img src="https://micro.mu/images/micro-3.0.png" style="width:100%;height: auto;" />
</center>
<br>

Micro took learnings from the original Go Micro framework and focused on not just gRPC based service development but actually packaging together a runtime 
and platform which exposes those services automatically as APIs. What this means is we can write microservices on the backend using gRPC and protobuf and 
immediately provide value to consumers and clients on the frontend via HTTP/JSON.

To learn more about that check out the project at [micro.mu](https://micro.mu) or the hosted platform at [m3o.com](https://m3o.com).

## Building on Netlify

We initially chose Netlify for hosting as we saw many people adopting it for Jamstack apps. Initially this proved really great for static content. As 
our apps got more complex and we started to build out the Distributed demo we found Netlify no longer scaled with our basic needs. The first example 
we can share is Netlify Functions for Next.js API routes.

Next.js routes can be turned into Netlify Functions which are essentially hosted as AWS Lambda functions. It's a clever way of pushing certain 
requirements to the server side, like calling third party APIs with keys you don't want to expose to the client. Next.js is great in this regard 
and plugins like [netlify-plugin-nextjs](https://github.com/netlify/netlify-plugin-nextjs) and [next-on-netlify](https://github.com/netlify/next-on-netlify) 
let us do this really quickly but the performance left a lot to be desired.

Our APIs are primarily hosted in London on DigitalOcean and while Netlify has a CDN for static content, the Lambda functions are deployed in 
a single region in US-East on AWS. For those who've suffered this pain you know exactly what that means. We were making cross atlantic calls 
from JS in the client to api routes on lambda and then finally to our apis. 

Needless to say this didn't scale for us. We weren't able to reach out the Netlify team to get help and so in frustration had to go down the self 
hosted route. Note we did test out Vercel and found the experience to be faster but self hosting on DigitalOcean just made more sense for 
our demo needs.

## Switching to Self Hosted

One of the things DigitalOcean now provides is [App Platform](https://www.digitalocean.com/products/app-platform/), a container hosting solution 
which lets you pick regions, does TLS certificate management for your custom domain and automatic builds from Git. This turned out to be a 
perfect solution for self hosted Next.js apps. 

Next.js at the end of the day is a React and node.js based application. As much as you may want to separate out the static content to something 
like Netlify and functions on Lambda, it equally just makes sense to host the entire thing in one place and run many copies of it much like 
we did in the old php and rails days.

<center>
<img src="{{ site.url }}/assets/images/do.png" style="width:100%; height: auto; border: 1px solid #eee; padding: 10px;" />
</center>
<br>
Because the APIs are colocated with the frontend we find this experience fairly fast, sub 100ms for all the API calls but we know it's not 
an ideal demonstration of the Jamstack's architecture and so we'll be working towards hosting on Vercel in the future to showcase that 
experience.

## How It All Fits Together

We're running Distributed as a Next.js application on the frontend talking to Micro APIs on the backend. All of this is constructed 
as API routes in Next.JS firing requests at our M3O platform and the various APIs we need. Let's walk through a typical request. 

For example, when loading a group we need to get the group info, user profile, chats and more. We could do this as a GraphQL API
but that would require too much stitching together in terms of the schema on the backend. Instead we're using protobuf and RPC 
for rapid development there and Micro automagically exposes that as a HTTP/JSON API.

So a typical flow is like so.

1. Frontend makes a request to `/api/groups/[id]` which loads the api code in the Next.js app
2. We validate the user is logged in by calling the `/users/Validate` endpoint and ff authenticated load the group data by id using `/groups/Read`
3. Skipping ahead, we'll load group messages via `/threads/ListConversations` and private messages using `/chats/ListMessages`
4. We can check for already read messages via a "seen" API and then subscribe to the streams API for instant messaging
5. Finally we render everything based on the content loaded for the user

<style>
.gist-data { max-height: 600px; overflow: auto;}
</style>

Here's a code "snippet" for those interested. From an MVP standpoint this is just a very quick and rapid way for us to build against 
numerous separate APIs on the backend all hosted in the same place.

<script src="https://gist.github.com/asim/af104dd0e83b8c16bbb6918c8d9bab97.js"></script>

For anyone interested in the "call" function. It's simply a small function we're using to call the Micro APIs on the backend. Remember 
Micro turns any RPC based service into a HTTP/JSON API automatically via an API gateway. M3O provides hosting for all this.

<script src="https://gist.github.com/asim/8293a2cb96bd768cab0d675391cde8be.js"></script>
<br>
## Performance & Productivity

Aside from the structural benefits of a framework like Next.js we find it really unlocks significant productivity by providing an opinionated 
approach to frontend development. That coupled with Micro on the backend and our APIs hosted on M3O it's enabled us to rapidly ship this 
MVP within the space of 4-6 weeks with mostly 1 person doing the work. 

That really speaks to the power of the combination of Next.js and Micro. For this demo we built APIs for user management, group messaging, 
websocket streaming, sending invite emails and audio/video through Twilio WebRTC. One can only imagine where it would go with a dedicated 
team and full product focus.

On the performance side, Next.js is blazingly fast by all measures. Whether it be the local reload for development or the server side rendering. 
It all adds to a really snappy experience on both the development and consumption side of things. With the backend we tried to pair this 
with Go based APIs written with Micro to ensure not just speed of development but also speed of delivery.

All in all, we think Next.js and Micro are the perfect pairing for any Jamstack and API based development.

## Conclusions

Thanks for reading this post on how we built [Distributed](https://distributed.app) on the Jamstack using [Next.js](https://nextjs.org/)
and [Micro](https:/micro.mu). Find the source code for distributed on [Github](https://github.com/m3o/distributed). If you want to build and host your own 
version signup to [M3O](https://m3o.com) and start running the same services from our open source repository 
[micro/services](https://github.com/micro/services).

Reach out on [slack](https://slack.m3o.com) or [twitter](https://twitter.com/m3oservices) if you have any questions.


