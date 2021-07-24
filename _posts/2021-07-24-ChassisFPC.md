---
layout: post
title: Chassis and FPCs and Interfaces, oh my!
published: false
---

Before I start this post, I had no idea that the singular and plural forms of the word _chassis_ were spelled identically, but pronounced differently. The singular is pronounced **CHASS-ee** while the plural is pronounced **CHASS-eez**? English is dumb.

## Interfaces

As a wee lad, I typed the `show interfaces` command on a Juniper device and saw output similar to the following:

```
user@host> show interfaces terse
Interface            Admin Link Proto    Local                 Remote
ge-0/0/0              up    up          
ge-0/0/0.0            up    up       		inet     1.1.65.1/24
ge-0/0/1              up    up         
ge-0/0/2              up    up         
ge-0/0/4              up    up          
```

Using the [Juniper interface naming conventions](https://www.juniper.net/documentation/us/en/software/junos/interfaces-ethernet-switches/topics/topic-map/switches-interface-understanding.html#id-understanding-interface-naming-conventions), we can see that these are physical gigabit-ethernet ports. 

But my child brain asked: "Where do these interfaces come from?" Good question, you silly boy. Are those interfaces manually configured? How does JunOS "know" about the existence of a physical port on the device? What do those numbers and slashes mean?

If you actually thought that I was playing around with Juniper devices as a child, then I have you fooled. Sadly, I was a fully grown adult when I had these questions.

## Why is everything broken?

Recently, I was fumbling around on a Juniper device. I had just completely [zeroized](https://www.juniper.net/documentation/us/en/software/junos/junos-install-upgrade/topics/ref/command/request-system-zeroize.html) the device and was trying to restore external connectivity into the device so I could proceed with my task.

A few changes later, I was at a point where I _should_ have had external connectivity. However, the device was still unreachable. I ran the `show interfaces descriptions` command and noticed the complete absence of the `up` Admin and Link statuses. Usually, you'd expect to see at least a `down` or something indicating that something was broken. So what was happening?

If you're wondeirng, it looked similar
```
user@host> show interfaces descriptions  
 Interface       Admin Link Description 
 ge-0/0/0                            
```

Google told me to check the chassis, so I ran `show chassis fpc` and saw a foreboding `Offline` status:

```
user@host> show chassis fpc 0
                     Temp  CPU Utilization (%)   Memory    Utilization (%)
Slot State            (C)  Total  Interrupt      DRAM (MB) Heap     Buffer
  0  Offline
```

This got me wondering - WTH is a chassis? What is an fpc?

