---
layout:	post
title:	"Netlify for the frontend, Micro for the backend"
date:	2020-12-03 10:00:00
---
<br>
Today Netlify has emerged as the modern platform for rapidly building web applications without having to worry about anything beyond your code. We at [Micro](https://m3o.com)
are users of Netlify and have bought into this phenomenal experience. What's more Netlify has demonstrated to us a breakdown in the classic web architecture 
stack which previously combined web and api concerns in a single place. As we moved through a tiered architecture frontend had not been given anything more 
than hints on how to consume dynamic content from the backend. Today we're all calling this the [Jamstack](https://jamstack.org/). 

## Jamstack

<img src="https://d33wubrfki0l68.cloudfront.net/b7d16f7f3654fb8572360301e60d76df254a323e/385ec/img/svg/architecture.svg" />
<center><i><small>Credit jamstack.org</small></i></center>
<br>
The jamstack rethinks the frontend architecture by separating the concerns of static and dynamic content and pushing for the dynamic side to be consumed 
through APIs and services. Effectively, Netlify embracing this model has tried to build microservices for the frontend and moved towards a unification 
of consumption of services via APIs on the backend. It's clear this is the architecture of the future for the web and the majority of cloud services 
will be built and consumed soley as APIs.

The one question we've really been seeing a lot though is "What's Netlify for the backend?". Many of those frontend users building Jamstack apps on 
Netlify are looking to where they can find and build these APIs. It seems even Netlify's current answer has been, "go host something on heroku". I think 
in 2020 this just doesn't fly. If the frontend is being reimagined then the same has to happen on the backend to cater for that use.

## API First

<img src="https://dev-to-uploads.s3.amazonaws.com/i/0znow24kgpu2dp3zg60n.png" />

We are seeing the emergence of APIs as the dominant form factor for cloud services, from AWS all the way through to Twilio and Stripe. What's even more 
compelling is while this model has emerged in the past few years, we are only really just getting started. It's our belief that in a decade from now 
some of the most important companies will be API first yet strangely there is no platform to caters to this form of development.

Where is Netlify for the backend?

## Netlify for the Backend

<img src="{{ site.baseurl }}/assets/images/netlify.png" />

[**M3O**](https://m3o.com) is a platform for cloud services development. The fastest way to build APIs without managing the infrastructure. M3O makes use of 
[**Micro**](https://micro.mu), an open source platform for microservices development. What we get from Micro is a powerful framework for building, running 
and consuming APIs as microservices. What M3O brings to the table is Micro as a Service, a fully managed platform for building microservices. Write services 
in Go and gRPC on the backend, expose them dynamically via HTTP API to be consumed by the frontend. M3O looks to fill that gap in the market for frontend 
devs. M3O is Netlify for the backend.

Where a new development model has emerged for the frontend, we think its dictating the "headless" paradigm shift for the backend and M3O wants to be there 
to host all of those APIs as Micro services.

But don't just take our word for it. We're going to walk you through a demonstration of the value proposition so you can see for yourself just how 
powerful Micro and M3O are. You're going to be writing and deployment APIs in minutes rather than hours! No more dealing with infrastructure on the 
backend, just as Netlify empowered devs on the frontend, we're doing the same for a new generation of developers on the backend.