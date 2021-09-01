---
title:  Tuning your cache
permalink: /docs/advanced/tuning-cache/
---

## Introduction

Cache performance is a complicated and lengthy topic.

By far the biggest factor is the hardware of the cache box. See [hardware](/docs/hardware/) for more information. Improving the hardware is likely to have a much bigger effect than anything else listed on this page.

That being said, there are 2 things you can look at doing when dealing with a slower than expected performance issue.

{% include toc.html %}

<div class="note info">
<h5>Advanced Steps</h5>
<p>
Beware, these steps are aimed at more experienced users and could cause you more problems if not implemented carefully.
</p>
</div>

## Adding additional IPs

While it is getting rarer as better client support is now available, some client software has some inherent limitations caused by strict adherence to the HTTP spec connection pool. Because of this, download speed is highly dependent on the latency between your server and the CDN servers. In the event you find your initial download speed with the default settings is slow this may be resolved by allocating more IP's to your cache. We suggest adding one IP at a time to see how much gain can be had (4 has been seen to work for a number of people). The most likely situation for this to help is in larger installs where you have a LAG network connection and are trying to balance many requests over multiple NICs.

### Step 1: Adding IP's to your docker host

Consult your OS documentation in order to add additional IP addresses onto your docker cache host machine

### Step 2: Adding IP's to your cache container

In order for this to work you need to add the port maps onto the relevant CDN container (for example Steam).

* If you are using `lancachenet/monolithic` then using `-p 80:80` should be sufficient as per the documentation.
* If you are using `lancachenet/generic` or `lancachenet/steamcache` then add multiple `-p <IPadddress>:80:80` for each IP you have added. For example `-p 10.10.1.30:80:80 -p 10.10.1.31:80:80`

### Step 3: Informing lancache-dns of the extra IP's

Finally we need to inform lancache-dns that Steam is now available on multiple IP addresses. This can be done on the command line by adding the following to the run command: `-e STEAMCACHE_IP="10.10.1.30 10.10.1.31"`. Note the quotes surrounding the multiple IP addresses.

### Step 4: Testing

Choose a game which has not been seen by the cache before (or clear your `/data/cache` folder) and start downloading it. Check to see what the maximum speed seen by your steam client is. If necessary repeat steps 1-3 with additional IPs until you see a download equivalent to your uncached Steam client or no longer see an improvement vs the previous IP allocation.

## Tweaking slice size

Some situations can see a slower initial download rate through the cache than without (though obviously subsequent downloads are MUCH faster). Testing shows that the client does not cope as well as most others with the chunking / slicing configuration for nginx that lancache uses.

Testing has shown that it is possible that a modest increase in slice size can help downloads. Although it also can considerably reduce the speed in some cases. In the worse cases where a client makes a small range request to a large file the cache will have to fetch a full slice from the internet (for example a 256KB request could result in a 8MB download).

<div class="note warning">
  <h5>HERE BE DRAGONS!!!</h5>
   <p>
It is highly unlikely you want to change the slice size unless you REALLY know what you are doing. Please be aware that there are a number of potential downsides to increasing the slice size, that should be fully understood before making changes to it. Please note that this tuning area is still under active development and the detail is likely to change as work progresses.
   </p>
</div>

### 1. Multi-user risk

In particular in a multi-user environment (like most LAN parties) where more than one person may be trying to download the same game, and thus the same multi-gig files, the performance might be reduced. The usual behaviour is if somebody tries to download a slice that is currently being downloaded by somebody else, the second person will be forced to wait until that slice has fully downloaded before being served with the slice. If you have a relatively slow internet connection (or its very highly utilised - like at most LAN parties) then the time to download a larger slice will be high and the resulting wait time could cause problems for the game client.

Ultimately the performance will be a trade off and factors such as number of internet connections & speed, utilisation, number of users, size of game, game client behaviour and cache server performance (cpu, ram & in particular disk I/O) will all influence whether or not increasing the slice size will help you. Testing / LAB performance may be very different to "real world" event performance with the same hardware / settings.

There is no one slice value that is likely to work in all configurations.

The default of 1MB is considered the safest, but increasing to 2, 4 or even 8 might give performance improvements for single-user download scenarios, however they may also make things slower. A 256kb range request causes a full slice to be fetched on a miss so with 8mb you would be fetching 7.75mb you dont need which is considerably slower.

### 2. Invalidation of existing cache data

In addition to the above - changing the slice size will make most of the existing data in your cache unusable, and thus will have to be downloaded again. Old data will be purged off as space is required, so there is no requirement to manually remove it.

* Anything that was originally downloaded that is smaller than your original slice size will still be valid.
* Any 'end parts' of files, that would be identical with the old and new slice size will also be valid.

For example, If you had an 8.5MB file you originally downloaded with a 1m slice size, and you change the slice size to 4m, the first 8 slices will be invalidated, but the last 0.5MB slice will still be valid. If you changed the slice size to 6MB, all pieces would be invalidated.

We are adding a hash check on Monolithic startup to identify whether various cache parameters that may invalidate the cache have changed since last run and throw an error. This will then force you to run with an override environment variable to proceed. See [this page](/docs/advanced/config-hash/) for more information on the override.

### 3. Which CDNs will be impacted

We anticipate that changing the slice size will have very minimal impact on Steam and Uplay as they already use files that are mainly around 1MB or less. Blizzard and Origin are potentially the CDN's that will benefit the most, as they download large files which are sliced by LanCache. In more recent times as the game catalogues change we have found that larger slice sizes are causing more issues so beware of that if you are testing and use multiple titles to confirm.

### 4. Disabling slicing

Per the [nginx documentation](http://nginx.org/en/docs/http/ngx_http_slice_module.html), setting the slice size to 0 disables slicing.

We do NOT recommend turning off slicing (or setting the value too high) - it will have serious performance impacts on the operation of the cache given the significant size of files found in many modern games.
