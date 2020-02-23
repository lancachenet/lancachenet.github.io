---
title:  Tuning your cache
permalink: /docs/tuning-cache/
draft: true
---

## Adding additional IPs

Steam in particular has some inherent limitations caused by strict adherence to the HTTP spec connection pool. As such Steam's download speed is highly dependent on the latency between your server and the Steam cdn servers. In the event you find your initial download speed with the default settings is slow this can be resolved by allocating more IP's to your cache. We suggest adding one IP at a time to see how much gain can be had (4 seems to work for a number of people).

### Step 1: Adding IP's to your docker host
Consult your OS documentation in order to add additional IP addresses onto your docker cache host machine

### Step 2: Adding IP's to your cache container
In order for this to work you need to add the port maps onto the relevant cdn container (for example steam). 
* If you are using `lancachenet/monolithic` then using `-p 80:80` should be sufficient as per the documentation. 
* If you are using `lancachenet/generic` or `lancachenet/steamcache` then add multiple `-p <IPadddress>:80:80` for each IP you have added. For example `-p 10.10.1.30:80:80 -p 10.10.1.31:80:80`

### Step 3: Informing lancache-dns of the extra IP's
Finally we need to inform lancache-dns that STEAM is now available on multiple IP addresses. This can be done on the command line using the following command `-e STEAMCACHE_IP="10.10.1.30 10.10.1.31"`. Note the quotes surrounding the multiple IP addresses.

### Step 4: Testing
Choose a game which has not been seen by the cache before (or clear your `/data/cache` folder) and start downloading it. Check to see what the maximum speed seen by your steam client is. If necessary repeat steps 1-3 with additional IPs until you see a download equivalent to your uncached steam client or no longer see an improvement vs the previous IP allocation.

## Tweaking slice size

Some info here