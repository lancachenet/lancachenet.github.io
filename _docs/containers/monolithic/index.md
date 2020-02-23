---
title: Monolithic
description: Our primary container
permalink: /docs/containers/monolithic/
---

![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/monolithic?label=Monolithic) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/lancache-dns?label=Lancache-dns) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/sniproxy?label=Sniproxy) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/generic?label=Generic)



## Introduction

This docker container provides a caching proxy server for game download content. For any network with more than one PC gamer in connected this will drastically reduce internet bandwidth consumption. 

The primary use case is gaming events, such as LAN parties, which need to be able to cope with hundreds or thousands of computers receiving an unannounced patch - without spending a fortune on internet connectivity. Other uses include smaller networks, such as Internet Cafes and home networks, where the new games are regularly installed on multiple computers; or multiple independent operating systems on the same computer.

This container is designed to support any game that uses HTTP and also supports HTTP range requests (used by Origin). This should make it suitable for:

 - Steam (Valve)
 - Origin (EA Games)
 - Riot Games (League of Legends)
 - Battle.net (Hearthstone, Starcraft 2, Overwatch)
 - Frontier Launchpad (Elite Dangerous, Planet Coaster)
 - Uplay (Ubisoft)
 - Windows Updates

This is the best container to use for all game caching and should be used for Steam in preference to the lancachenet/steamcache and lancachenet/generic containers.

## Usage

You need to be able to redirect HTTP traffic to this container. The easiest way to do this is to replace the DNS entries for the various game services with your cache server.

You can use the [lancache-dns](https://hub.docker.com/r/lancachenet/lancache-dns/) docker image to do this or you can use a DNS service already on your network see the [lancache-dns github page](https://github.com/lancachenet/lancache-dns) for more information.

For the cache files to persist you will need to mount a directory on the host machine into the container. You can do this using `-v <path on host>:/data/cache`. You can do the same with a logs directory as well if you want logs to be persistent as well.

Run the container using the following to allow TCP port 80 (HTTP) and to mount `/cache/data` directory into the container.

```
docker run \
  --restart unless-stopped \
  --name lancache \
  -v /cache/data:/data/cache \
  -v /cache/logs:/data/logs \
  -p 192.168.1.10:80:80 \
  lancachenet/monolithic:latest
```

Unlike lancachenet/generic this service will cache all cdn services defined in the [uklans cache-domains repo](https://github.com/uklans/cache-domains) so multiple instances are not required.

## Simple Full Stack startup

To initialise a full caching setup with dns and sni proxy you can use the following script as a starting point:
```
export HOST_IP=`hostname -I | cut -d' ' -f1`
docker run --restart unless-stopped --name lancache-dns --detach -p 53:53/udp -e USE_GENERIC_CACHE=true -e LANCACHE_IP=$HOST_IP lancachenet/lancache-dns:latest
docker run --restart unless-stopped --name lancache --detach -v /cache/data:/data/cache -v /cache/logs:/data/logs -p 80:80  lancachenet/monolithic:latest
docker run --restart unless-stopped --name sniproxy --detach -p 443:443 lancachenet/sniproxy:latest
echo Please configure your router/dhcp server to serve dns as $HOST_IP
```
Please check that `hostname -I` returns the correct IP before running this snippet


## Changing from lancachenet/steamcache and lancachenet/generic

This new container is designed to replace an array of steamcache or generic containers with a single monolithic instance. However if you currently run a steamcache or generic setup then there a few things to note.

1) Your existing cache files are NOT compatible with lancachenet/monolithic, unfortunately your cache will need repriming
2) You do not need multiple containers, a single monolithic container will cache ALL cdns without collision
3) lancachenet/monolithic should be compatible with your existing container's env vars so you can use the same run command you currently use, just change to lancachenet/monolithic


## Origin and SSL

Some publishers, including Origin, use the same hostnames we're replacing for HTTPS content as well as HTTP content. We can't cache HTTPS traffic, so if you're intercepting DNS, you will need to run an SNI Proxy container on port 443 to forward on any HTTPS traffic.

```
docker run \
  --restart unless-stopped \
  --name sniproxy \
  -p 443:443 \
  lancachenet/sniproxy:latest
```

Please read the [lancachenet/sniproxy](https://github.com/lancachenet/sniproxy) project for more information.

## DNS Entries

You can find a list of domains you will want to use for each service over on [uklans/cache-domains](https://github.com/uklans/cache-domains). The aim is for this to be a definitive list of all domains you might want to cache.

## Suggested Hardware

Regular commodity hardware (a single 2TB WD Black on an HP Microserver) can achieve peak throughputs of ~30MB/s using this setup (depending on the specific content being served).  This would be suitable for very small LANs (<10 people).  For any sort of hosting for LAN sizes above this, it is thoroughly recommended that your storage backend is entirely SSD or NVMe.

## Changing Upstream DNS

If you need to change the upstream DNS server the cache uses, these are defined by the `UPSTREAM_DNS` environment variable. The defaults are Google DNS (8.8.8.8 and 8.8.4.4).

```
 UPSTREAM_DNS 8.8.8.8 8.8.4.4
```

You can override these using the `-e` argument to docker run and specifying your upstream DNS servers. Multiple upstream dns servers are allowed,  separated by whitespace.

```
-e UPSTREAM_DNS="1.1.1.1 1.0.0.1"
```

## Tweaking Cache sizes

Two environment variables are available to manage both the memory and disk cache for a particular container, and are set to the following defaults.
```
CACHE_MEM_SIZE 500m
CACHE_DISK_SIZE 1000000m
```

In addition, there is an environment variable to control the max cache age.

```
CACHE_MAX_AGE 3560d
```

You can override these at run time by adding the following to your docker run command.  They accept the standard nginx notation for sizes (m) and durations (m/h/d).

```
-e CACHE_MEM_SIZE=4000m -e CACHE_DISK_SIZE=1000g -e CACHE_MAX_AGE=3560d
```

CACHE_MEM_SIZE relates to the memory allocated to NGINX for the cache manager process.  1 megabyte will hold around 8000 cache entries, and for Monolithic slicing in 1MB slices, this means each 1M allocated will service around 8GB on disk.  The default size of 500MB should allow you to have a cache of up to 4TB quite comfortably.  Any other available memory in your cachebox should then be used for the Filesystem cache.

## Tuning your cache

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

## Monitoring

Access logs are written to /data/logs. If you don't particularly care about keeping them, you don't need to mount an external volume into the container.

You can tail them using:

```
docker exec -it lancache tail -f /data/logs/access.log
```

If you have mounted the volume externally then you can tail it on the host instead.

## Advice to Publishers

If you're a games publisher and you'd like LAN parties, gaming centers and other places to be able to easily cache your game updates, we reccomend the following:

 - If your content downloads are on HTTPS, you can do what Riot have done - try and resolve a specific hostname. If it resolves to a RFC1918 private address, switch your downloads to use HTTP instead.
 - Try to use hostnames specific for your HTTP download traffic.
 - Tell us the hostnames that you're using for your game traffic. We're maintaining a list at [uklans/cache-domains](https://github.com/uklans/cache-domains) and we'll accept pull requests!
 - Have your client verify the files and ensure the file they've downloaded matches the file they **should** have downloaded. This cache server acts as a man-in-the-middle so it would be good to ensure the files are correct.

 If you need any further advice, please contact us and we'll be glad to help!

## Frequently Asked Questions

If you have any questions, please check [our FAQs](faq.md). If this doesn't answer your question, please raise an issue in GitHub.

## How to help out
To build just run `docker build --tag lancachenet/monolithic:testing .`. To test you can run `./run_tests.sh`

If you want to test a new build with a forked uklans repo you can specify a combination of `-e CACHE_DOMAINS_REPO="https://github.com/vibroaxe/cache-domains.git" -e CACHE_DOMAINS_BRANCH="testing"` or even `-v <your repo here>:/data/cachedomains -e NOFETCH=true` if using a locally bind mounted git repo.

## Thanks

 - Based on original configs from [ansible-lanparty](https://github.com/ti-mo/ansible-lanparty).
 - Everyone on [/r/lanparty](https://reddit.com/r/lanparty) who has provided feedback and helped people with this.
 - UK LAN Techs for all the support.

# Frequently Asked Questions

## What is a LAN Cache or Steam Cache?

A Steam Cache or LAN Cache is a way of speeding up Steam or other content downloads on a local area network for multiple users.

## How does it work?

The Cache works as a simple caching proxy server. Most game and content updates use unsecured HTTP for downloading the content. We are able to intercept these download requests and save them, so that if another request is made for the same file, we don't need to download it again from the Internet.

## This doesn't sound very new or original, we've been using caches for years

You're correct. HTTP caches are not a new thing, many businesses and organisations such as universities and schools have been running them for a while, however they are normally intended for caching traditional web traffic with short caching lifetimes and small files.

## Why not use Squid or another server?

We chose nginx as it offers the flexibility we need to overwrite cache keys and optimise the cache performance to cope with gaming update traffic. We need to be able to cache files for a long period of time, rewrite upstream HTTP headers and to be able to exclude the hostname of the server from the cache key, to allow us to cache content served from Content Delivery Networks (CDNs)

## Should I use this then?

It depends on your situation. If you just have a single PC and you download games to it once, you probably won't see any advantage or benefit from using the cache. It won't speed up your downloads of **new** content. It would speed up downloads if you removed the game and then re-installed it.

If you have multiple computers sharing one Internet connection, you would see a benefit to it. If user A downloads Team Fortress 2 on Steam, it will download at the speed of your Internet Connection and stored on the cache server. Once it's been downloaded, if user B downloads Team Fortress 2, it will be served up from your cache server at the maximum speed it can read the files from storage.

## So can I really get 10gbps downloads for my games?

Yes, but it relies on a few things:

 1. You have already downloaded the content and it is on your cache server.
 2. Your network switch, network card in the cache server and PC network are 10gbps.
 3. You can read the files from disk at 10gbps.

Realistic performance you can expect from the cache depends on all of these things.

## You mentioned storage, how fast does my storage need to be?

As fast as possible. Linux is great in that it will cache in memory any files accessed from disk, so if someone accesses the same files multiple times, and they haven't changed, those files will be served from memory, which is very quick.

If the files aren't cached in memory, the OS will need to read it from the disk. If you have mechnical drives, this can be slow, and if you're reading from different areas of the disk, this will slow things down further. SSDs are quicker and if you use RAID, you can improve things even further.

If you can't afford a large SSD, you can also use lvmcache and set up the SSD to act as a read cache for the rotational disk. This will give you three levels of caching:

 1. RAM
 2. SSD based lvmcache
 3. Rotational Hard Disk

If you can afford large SSDs, then run your entire cache using SSDs!

## Why do you mention you support Windows Update, can't we use WSUS?

This cache is intended for a LAN party environment where you have no control over the computers on the network. To force them to use a WSUS server, you'd need to either join them to a domain and push the config out through group policy or run a script to get them to use the WSUS server. Each of which is not really possible for a LAN party

This solution is designed to support anything from a couple of people at home, to thousands of people at gaming events. This is the easiest way to achieve the caching of services like Windows Update, without modifying people's computers.

## But what about HTTPS, can I cache HTTPS?

No. HTTPS traffic is encrypted. Some of the games, Origin for instance, also serve HTTPS content on the same hostnames we're intercepting - for this you can use SNI Proxy. It listens on port 443 and just passes through any HTTPS traffic. It is unable to inspect the traffic, or cache it.

You can find more at the [lancachenet/sniproxy](https://github.com/lancachenet/sniproxy) project page.

## Can I cache Fortnite/Epic Games Launcher?

Yes, as of [2019-05-30](https://github.com/uklans/cache-domains/pull/89) - Epic Games have kindly moved their CDN servers and launcher to use HTTP for content delivery.

## Can I cache *some other service*

Yes, almost any HTTP content can be cached. We're maintaining a list of hostnames for various update services on the [uklans/cache-domains](https://github.com/uklans/cache-domains) project. There's a lot of services you can cache!

## How do you intercept the HTTP traffic?

We prefer to use DNS-based interception This is because it is easier to deploy than other methods. We have a [lancache-dns](https://github.com/lancachenet/lancache-dns) project that is a self-contained DNS server with options for setting your cache IP addresses.

If you already run a DNS server and are comfortable configuring new override DNS zones, you can also just do this from the list on [uklans/cache-domains](https://github.com/uklans/cache-domains). pfSense's DNS forwarder can easily be configured, and the project has scripts to generate config for unbound.

Other options also involve transparently intercepting HTTP traffic at a network level either using WCCP on Cisco switches or on your router if it supports it.

## Can I load some content into the cache ahead of my event?

You could look at [zeropingheroes/lancache-autofill](https://github.com/zeropingheroes/lancache-autofill) as an aide to pre-loading popular games into the cache ahead of time to help kick start the cache

### Disabling `systemd-resolved` DNSStubListener
If your cache host is running a recent Linux distribution, it is likely running `systemd-resolved`, which listens on port `53`. If you want your cache host to bind to `0.0.0.0:53` (INADDR_ANY, all IPv4 addresses), you will first need to disable `systemd-resolved`'s stub listener.

1. Run `sudo -e /etc/systemd/resolved.conf` and change the line starting with `#DNSStubListener=` to `DNSStubListener=no` - _removing the `#` is important_
1. Run `sudo rm /etc/resolv.conf`
1. Run `sudo ln -s /var/run/systemd/resolve/resolv.conf /etc/resolv.conf`
1. Run `sudo service systemd-resolved restart`
1. Check that you can still resolve DNS on the cache server by running `nslookup` for a domain of your choice (e.g. `lancache.net`)

# Hardware examples and throughput 

## Introduction

This project is heavily reliant on the hardware that you run it on.  The most 
common complaint is "The cache is slow" and in nearly every case this can be 
attributed to the hardware that the cache is being run on.


The following hardware specifications are real-world examples of hardware that
has been used to run this project, so you can get an idea of the type of 
performance that you can expect from varying hardware setups.



## High Capacity

24 CPU core, 140GB memory, 8 x Samsung 850 Pro SSD in RAID6
Served ~2,800 uniques, peaking at 10Gbps


## Medium capacity

24 CPU core, 180GB memory, 5 x 900GB 15K SAS drives, plus 180GB SSD (lvmcache)
Served ~250 uniques, peaking at 1.8Gbps

## Low capacity

CPU: Quad-core AMD Phenom X4 II 945 (old!)
MEM: 16GB DDR3
Disk: 2x2TB RAID0 + 512MB SSD LVM Cache.
Without the LVM Cache the system will push around 300-400mb/s, with the SSD Cache pushing around 800-900mb/s

## License

The MIT License (MIT)

Copyright (c) 2019 Jessica Smith, Robin Lewis, Brian Wojtczak, Jason Rivers, James Kinsman

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.