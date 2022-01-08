---
layout: post
title: Next-hop=self??
---

Title says it all. My last blog post touched a bit on how next-hop is treated within iBGP. When I first learned about "next-hop:self," my brain broke a bit. I'll try to break this down in this post.

## Refresher

In an iBGP fabric, each peer is connected directly with every other peer in a full mesh. Routes learned from a peer cannot be re-advertised to another adjacent peer. 

## Use Case

As I understand it, `next-hop=self` is used to distribute routes learned from an eBGP peer into an IGP. For instance, let's say that you (AS101) is learning the 1.1.1.0/24 prefix from your external peer AS100. 

![AS100 advertising 1.1.1.0/24 to AS101]({{ site.baseurl }}/images/04_22_pic1.png "Example")

On the AS101 edge router, the `1.1.1.0/24` route will have a `next-hop` address of `10.0.0.1`, which is the IP address of the eBGP peer router in AS100. The `10.0.0.1` IP is directly reachable over the interface the eBGP session is set up on.

Remember that in iBGP peering, next-hop information is not changed as the advertisement moves to the adjacent peer. This means that if I set up an iBGP full mesh within AS101 and distribute the `1.1.1.0/24` route to my iBGP peers, the `next-hop` on that route will still be the address of the eBGP peer `10.0.0.1`. This presents an issue - how do the iBGP peers within AS101 know how to reach `10.0.0.1`? Recall that iBGP peering sessions are set up between loopback addresses of the peers. The iBGP participants only know how to reach those addresses.

This is where `next-hop=self` comes in. The `next-hop` attribute on the `1.1.1.0/24` route will be changed to the loopback address of the AS101 edge router `172.168.0.1`. A-ha! That IP is reachable from all iBGP peers within AS101.