![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/monolithic?label=Monolithic) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/lancache-dns?label=Lancache-dns) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/sniproxy?label=Sniproxy) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/generic?label=Generic)


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

## Advice to Publishers

If you're a games publisher and you'd like LAN parties, gaming centers and other places to be able to easily cache your game updates, we reccomend the following:

 - If your content downloads are on HTTPS, you can do what Riot have done - try and resolve a specific hostname. If it resolves to a RFC1918 private address, switch your downloads to use HTTP instead.
 - Try to use hostnames specific for your HTTP download traffic.
 - Tell us the hostnames that you're using for your game traffic. We're maintaining a list at [uklans/cache-domains](https://github.com/uklans/cache-domains) and we'll accept pull requests!
 - Have your client verify the files and ensure the file they've downloaded matches the file they **should** have downloaded. This cache server acts as a man-in-the-middle so it would be good to ensure the files are correct.

 If you need any further advice, please contact us and we'll be glad to help!
 
 ## How to help out
To build just run `docker build --tag lancachenet/monolithic:testing .`. To test you can run `./run_tests.sh`

If you want to test a new build with a forked uklans repo you can specify a combination of `-e CACHE_DOMAINS_REPO="https://github.com/vibroaxe/cache-domains.git" -e CACHE_DOMAINS_BRANCH="testing"` or even `-v <your repo here>:/data/cachedomains -e NOFETCH=true` if using a locally bind mounted git repo.

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