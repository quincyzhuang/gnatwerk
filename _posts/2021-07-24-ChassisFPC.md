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
ge-0/0/0.0            up    up       		 inet     1.1.65.1/24
ge-0/0/1              up    up         
ge-0/0/2              up    up         
ge-0/0/4              up    up          
```

Using the [Juniper interface naming conventions](https://www.juniper.net/documentation/us/en/software/junos/interfaces-ethernet-switches/topics/topic-map/switches-interface-understanding.html#id-understanding-interface-naming-conventions), we can see that these are physical gigabit-ethernet ports. 

But my child brain asked: "Where do these interfaces come from?" Good question, you silly kid. Are those interfaces manually configured? How does JunOS "know" about the existence of a physical port on the device? What do those numbers and slashes mean?

If you actually thought that I was playing around with Juniper devices as a child, then I have you fooled. Sadly, I was a fully grown adult when I had these questions.

## Why is everything broken?

Recently, I was fumbling around on a Juniper device. I had just completely [zeroized](https://www.juniper.net/documentation/us/en/software/junos/junos-install-upgrade/topics/ref/command/request-system-zeroize.html) the device and was trying to restore external connectivity so I could proceed with my task.

A few changes later, I was at a point where I _should_ have had external connectivity. However, the device was still unreachable. I ran the `show interfaces descriptions` command and noticed the complete absence of the `up` Admin and Link statuses. Usually, you'd expect to see at least a `down` or something indicating that something was broken. So what was happening?

If you're wondering, it looked similar
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

## What is an FPC?

The **Flexible PIC Concentrator**, or FPC, is integral for packet-forwarding. It's a sort of house for the PICs (**Physical Interface Cards**), which are slotted into the FPC and provide the ports available on the routing chassis. FPCs have multiple **slots**, so one can attach multiple PICs into a single FPC. On a similar note, PICs have multiple multiple **ports**.

Now, we should revisit interface labeling conventions so we can de-mystify WTH those numbers are for. 

```
<medium>-<FPC slot>/<PIC slot>/<Port number>
```

This means that an interface named `ge-0/0/0` is a `gigabit ethernet` port residing on `FPC slot 0`, `PIC 0`, `port 0`.

Going back to my predicament above - it appears that FPC slot 0 was completely offline. This means that every single port residing on that FPC is also dead. Issuing a command such as `request chassis fpc restart slot 0` is often the right thing to to force the FPC to reboot and come back online, which worked out great for me!

## Port Redundancy

It would be quite bad to have all your important networks plugged into an FPC that suddenly decides to crap out. Remember - FPCs/PICs/ports are all **physical** devices. They will eventually break! For the port-provisioning projects I've been involved in, it's been very important to make sure that ports are evenly spread out as possible. This means allocating ports so that you have an even spread among all available FPC slots, PICs, and ports. 

Take the two following (overly simplifed) examples.

Ex: bad. You will be sad.

```
user@host> show interfaces descriptions  
 Interface       Admin Link Description 
 et-0/0/0        up    up   ISP #1
 et-0/0/1        up    up   ISP #2
 et-0/0/2        up    up   Campus NW Core
 et-0/0/3        up    up   Campus NE Core
```

Ex: good! You will be less sad

```
user@host> show intefaces descriptions  
 Interface       Admin Link Description 
 et-0/0/0        up    up   ISP #1
 et-1/0/1        up    up   ISP #2
 et-0/1/0        up    up   Campus NW Core
 et-1/1/3        up    up   Campus NE Core
```

Well, that's all for today. Thanks for reading!