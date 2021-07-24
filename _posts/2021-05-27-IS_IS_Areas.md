---
layout: post
title: IS-IS Areas
---

[IS-IS](https://en.wikipedia.org/wiki/IS-IS) is an IGP that provides routing and reachability within an AS. It's pretty cool (though has an unfortunate name). I've been studying the protocol a bit and intend to set up some labs, so stay tuned for that... In any case, let's talk about IS-IS areas.

I also apologize for not posting in a while. It's been a busy few weeks.

## What is an IS-IS Area

Areas are used to subdivide a network into logical groupings. How simple are areas? They're as simple as 1,2,3! Don't believe me? It's actually even easier than that. IS-IS contains Level 1 and Level 2 areas. The takeaway is that Level 1 areas define a single "cluster", while Level 2 areas connect Level 1 areas to each other. An area can also occupy both levels at the same time. More on that later.

Here's an example. There area two areas: Area A and B. There is a Level 2 connection linking A and B together, while Level 1 links connect each trio together.               

![IS-IS Areas Simple Diagram]({{ site.baseurl }}/images/05_27_21_pic1.png "IS-IS_Area")

## IS-IS Backbone

I often find the term "backbone" to be a loaded term. You often hear it used a lot to describe the infrastructure of huge network providers and carriers. There's no need to be confused though when it comes to IS-IS. All you have to do is trace the Level 2 links and voila! You have your backbone. 

Here's a slightly more complex topology.

![IS-IS Areas Backbone Diagram]({{ site.baseurl }}/images/05_27_21_pic2.png "IS-IS_BB")

The backbone here is the rectangle-thing in the center that connects together Areas A,B, and C. You'll also notice that an Level 2 and 1 area exists in area C, which is kinda neat. This means you can have two "border" routers in a single IS-IS area.

## But why?

Areas aren't just a fancy way of dividing up an IS-IS topology.

* Participants within a Level 1 area can share routes with each other
* Level 1 areas can export routes to a Level 2 area
* Level 2 areas **cannot** export routes to a Level 1 area

That sounds really swell. IS-IS has an inherent trait which control how many routes are swimming around a network. Use this fact to limit the complexity of a network.

## Next Up

[Juniper IS-IS set-up guide](https://www.juniper.net/documentation/us/en/software/junos/is-is/topics/example/isis-multi-level.html)