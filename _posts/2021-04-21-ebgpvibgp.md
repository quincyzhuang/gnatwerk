---
layout: post
title: eBGP vs iBGP
---

BGP seems rather straightforward at first. The best path selection algorithm makes sense and the next-hop obviously will be the device that gets you one step closer to the destination. However, I've come to learn that the main differences between eBGP and iBGP are pretty important to keep in mind. Let's review them:

## eBGP

* Peering is setup across physical interfaces (connectivity is provided over this physical link)
* Participants have different AS numbers
* AS number is pre-pended to the AS Path of routes
* Peer TTL is 1
* Routes received from an eBGP peer can be advertised to both eBGP and iBGP peers
* BGP attributes like local preference are not sent to eBGP peers
* Next-hop is the IP of the physical interface

## iBGP

* Peering is setup between lo0 interfaces (connectivity is provided by an underlying IGP)
* Operates within an AS
* Requires full mesh connectivity with all over iBGP peers
* Peer TTL is 255
* Routes received from an iBGP peer are not advertised to other iBGP peers. These can only be advertised to eBGP peers
* Local preference is advertised to iBGP peers
* Next-hop is unchanged as the route is passed along

## Insights

It's a lot to take in. I think it's clear to most that eBGP is intended for external peers while iBGP is intended for internal networks. Why the differences though?

In eBGP, routes pass through multiple ASNs en-route to the destination. It's important to update attributes like next-hop and the AS path as the route moves between ASNs so that the packet knows the full forward path. Routes will automatically be withdrawn if an eBGP peers goes down. After all, we don't want to continue routing packets to a dead peer!

In iBGP, the goal is to basically figure out how to get to any external address from any other point within the internal network. As the route moves around, it's important that the attributes on the route remain static so that the destination is clear no matter which packet the router is in. Since peering is setup between loopbacks, there is some redundancy because so long as the IGP is there, one downed iBGP peer is not the end of the world.