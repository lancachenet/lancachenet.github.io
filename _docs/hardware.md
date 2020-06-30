---
title: Hardware
permalink: "/docs/hardware/"
---

## Introduction

This project is heavily reliant on the hardware that you run it on.  The most
common complaint is "The cache is slow" and in nearly every case this can be
attributed to the hardware that the cache is being run on.

The following hardware specifications are real-world examples of hardware that
has been used to run this project, so you can get an idea of the type of
performance that you can expect from varying hardware setups.

## High Capacity

"My LAN is in a national expo centre and has 1000s of gamers in a space that trade shows normally used to sell motorhomes or show off cooking utensils. Let's cache like we mean it."

* 24 CPU core, 
* 140GB memory, 
* 8 x Samsung 850 Pro SSD in RAID0
* Served ~2,800 uniques, 
* peaking at 10Gbps

## Medium capacity

"I run the LAN. All my mates come along, in fact i don't even know some of customers by name. My living room doesn't hold these people so we found hire a good size venue"
* 24 CPU core, 
* 96GB memory, 
* 6 x 1TB 15K SAS drives in RAID5, 
* plus 2TB SSD (lvmcache)
* Served ~250 uniques, 
* peaking at 1.8Gbps

## Low capacity

"We have 20 ppl and somewhere to put them we need a name for our LAN. Also known as my housemates all game, lets level up."

* 8 CPU core
* 16GB DDR3
* 2TB HDD + 512GB SSD LVM Cache.
* Without the LVM Cache the system will push around 300-400Mbps, with the SSD Cache pushing around 800-900Mbps

## Home Hardware

"Me and my < Wife/Husband/Bother/Sister/Extremely well trained cat> both play < insert game >""

Regular commodity hardware (a single 2TB WD Black on an HP Microserver) can achieve peak throughputs of ~240Mbps using this setup (depending on the specific content being served).  This would also be suitable for very small LANs (<10 people).  For any sort of hosting for LAN sizes above this, it is thoroughly recommended that your storage backend is entirely SSD or NVMe.


## One more thing ...

"I can see Disney from here, or at least I could if there weren't so many gamers here that the LAN isn't long enough to look at each of them individually. I'm not busy while running a LAN this size so I have decided to try a thing ..."

* 32 CPU core
* 128GB memory
* 8TB of nvme
* quad 40Gbit NIC
* peaking at 53.9Gbps

see [here](https://dreamhack.com/anaheim/top-news/anaheim-you-were-awesome/) for details
