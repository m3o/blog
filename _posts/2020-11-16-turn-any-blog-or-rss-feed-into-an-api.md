---
layout:	post
title:	"Turn any blog or RSS feed into an API"
date:	2020-11-16 10:00:00
---
<br>
Last time we talked to you about how we believe Micro is [Netlify for the backend]({{ site.baseurl }}/2020/11/12/netlify-for-the-frontend-micro-for-the-backend.html). 
We got a very strong positive reaction to that, filling a pain point it looks like many people had. In that post we used headless cms aka blog posts 
as an example of the API you'd build on the backend to be consumed by a frontend. Today we're going to take it a step further.
We're going to turn any blog or RSS feed into an API.

An API on its own isn't enough. There's no content. You have to populate it yourself. So what if we made it just a little bit easier. How about we help
 you turn any blog, any content with an rss feed into an easy to consume API with just a handful of commands. That's the power of Micro. 
Exciting right, let's get to it.

## Things to know before we start

Before we start, just a little housekeeping. Here's the tools we provide and are making use of.

- [Micro](https://micro.mu) - is an open source framework for microservices development
- [M3O](https://m3o.com) - is a hosted platform for backend API development powered by Micro

These two things are fairly important to know before starting. Ok back to it.

## Content in 2020

For the most part blogging platforms haven't really evolved. There's still wordpress but interestingly everythings a bit of a silo or you have to 
run something yourself, we use a bit of jekyll + markdown, etc and that works but it's not very developer friendly (from an API standpoint). 
That's really led to firstly a bifurcation in adoption of tools and how content is stuck in walled gardens (another story for another day) and 
why we're seeing an explosion in headless CMS.

People want to write content and get paid. Most of those places don't really have APIs but do have RSS feeds. Developers want to consume and 
use this content, quite honestly it goes beyond developers. Content is king and it's used for all sorts of sentiment analysis and other 
market research. It's also actually reblogged, retweeted, and put in lots of different places. It would be really great if there was a much 
simpler way to go about providing APIs for all this content out there.

So let's do it. Micro is going to turn any blog or RSS feed into an API so it can be consumed from anywhere in any which way. And the key point 
here, it's all hosted for you, without having to think about doing that yourself.

## Deploying the blog backend

If you weren't around for the other post, we'll help you deploy the existing posts api and related services.

### Signup to M3O

You'll need to install micro and signup to M3O for this.

```sh
curl -fsSL https://install.m3o.com/micro | /bin/bash
```

For those wary of curl into bash, you can view it first [https://install.m3o.com/micro](https://install.m3o.com/micro).


Signup is purely CLI based for now so just issue the following command and follow the steps

```sh
micro signup
```

Once you're done you should have an account on M3O and be automatically logged in.

### Run the API

All the services you're going to run are open source. You can check them out at [github.com/micro/services](https://github.com/micro/services). 

Let's bootstrap the blog super quick. Because Micro is all about microservices development, we've decomposed the blog API into posts, comments and tags. 
You can find all the code on [github](https://github.com/micro/services/tree/master/blog).

Deploying these is super simple.

```sh
# run the posts service
micro run github.com/micro/services/blog/posts

# run the tags service
micro run github.com/micro/services/blog/tags

# run the comments service
micro run github.com/micro/services/blog/comments
```

Check they're running using `micro status`. You should see the status progress through starting, building and running. 
If you want to see logs or anything related just do `micro logs posts` and the same for any other service by name.

Now you've got those installed we need to move on to pulling the content from your current blog's rss feed.

### Crawl your blog or RSS feed

RSS feeds are a staple of the content industry. its a powerful standard that basically allows anything to be published as an XML based 
stream and then consumed and published in any other form. For most of us we're probably used to using RSS feed readers like 
[Feedly](https://feedly.com) or similar. Its not glamorous but it definitely gets the job done.

We've written a simple feeds service that can consume any RSS feed and populate your posts API. The code lives on 
[github](https://github.com/micro/services/tree/master/blog/feeds) alongside all the other services you just started.
Follow below to run and populate the feeds service.

Run the feeds service

```sh
micro run github.com/micro/services/blog/feeds
```

Check and wait for it to be in a running state

```sh
micro status
```

OK now the fun part. Enter your favourite rss feed to be saved and crawled.

```sh
micro feeds new --name="hackernews" --url=https://news.ycombinator.com/rss
```

Now if you give it a minute or so, what you should see is a list of posts from hacker news.

```sh
$ micro posts query
{
        "posts": [
                {
                        "id": "334ff5ad67cfd018a42ddd6095d3d51c",
                        "title": "Winners of Close-Up Photographer of the Year",
                        "slug": "winners-of-close-up-photographer-of-the-year",
                        "created": "1605277921",
                        "metadata": {
                                "domain": "news.ycombinator.com",
                                "link": "https://www.theatlantic.com/photo/2020/11/winners-close-up-photographer-year/617070"
                        }
                },
                {
                        "id": "33a807b3636f0d232f219e01ff6ee576",
                        "title": "Scientists discover two new mammals in Australia",
                        "slug": "scientists-discover-two-new-mammals-in-australia",
                        "created": "1605277920",
                        "metadata": {
                                "domain": "news.ycombinator.com",
                                "link": "https://www.cnet.com/news/scientists-discover-two-new-mammals-in-australia/#ftag=CAD-09-10aai5b"
                        }
                },
                {
                        "id": "148774c2fa4762587f84375dd5359a64",
                        "title": "The Next Decade Could Be Even Worse",
                        "slug": "the-next-decade-could-be-even-worse",
                        "created": "1605277919",
                        "metadata": {
                                "domain": "news.ycombinator.com",
                                "link": "https://www.theatlantic.com/magazine/archive/2020/12/can-history-predict-future/616993/"
                        }
                }
}
```

### Your posts as an API

And just like we did last time, here's how to directly query your posts via an api .Get your namespace using the command 
`micro user namespace` and compose the url as `$namespace.m3o.dev/posts`.

```sh
# get your namespace
$ namespace=$(micro user namespace)

# get the posts from the api
$ curl https://$namespace.m3o.dev/posts/query
```

And hey presto! Just like that we have turned your blog or RSS feed into a http API!

## The value proposition

What is the value of turning any content into an API? The world is moving towards being entirely driven by technology. It's not enough that 
we be able to consume through the web, we're now moving to mobile, voice and other platforms. APIs will power the world and to really 
have full opportunity to realise that we have to get this content out of silos and into a consumable format, that being http APIs.

Companies like Stripe, Twilio, Sendgrid and others have been API-ifying (yea I said) payments, communication and email but content 
is still stuck in the dark ages. We think the best way to make it happen is by putting power into the hands of those actually creating 
the content or those who really want to program against it. Hopefully this serves as a nice example of how to do that with Micro on M3O.
