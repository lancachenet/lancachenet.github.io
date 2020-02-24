---
title: General
description: Our primary container
permalink: /docs/containers/monolithic/
---

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

You can use the [lancache-dns](https://hub.docker.com/r/lancachenet/lancache-dns/) docker image to do this or you can use a DNS service already on your network see the [lancache-dns](/docs/containers/lancache-dns/) for more information.

For the cache files to persist you will need to mount a directory on the host machine into the container. You can do this using `-v <path on host>:/data/cache`. You can do the same with a logs directory as well if you want logs to be persistent as well.

Run the container using the following to allow TCP port 80 (HTTP) and to mount `/cache/data` directory into the container.

```sh
docker run \
  --restart unless-stopped \
  --name lancache \
  -v /cache/data:/data/cache \
  -v /cache/logs:/data/logs \
  -p 192.168.1.10:80:80 \
  lancachenet/monolithic:latest
```

Unlike lancachenet/generic this service will cache all cdn services defined in the [uklans cache-domains repo](https://github.com/uklans/cache-domains) so multiple instances are not required.

## SSL

Some publishers, including Origin, use the same hostnames we're replacing for HTTPS content as well as HTTP content. We can't cache HTTPS traffic, so if you're intercepting DNS, you will need to run an SNI Proxy container on port 443 to forward on any HTTPS traffic.

Please read the [sniproxy](/docs/containers/sniproxy/) container information for more information on how to do this.

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

## Monitoring

Access logs are written to /data/logs. If you don't particularly care about keeping them, you don't need to mount an external volume into the container.

You can tail them using:

```
docker exec -it lancache tail -f /data/logs/access.log
```

If you have mounted the volume externally then you can tail it on the host instead.

## Custom Forks/Branches

See [cache_domains](/docs/containers/monolithic/cache_domains/) for details on how to use a non-standard list of hostnames for this container.

## Thanks

- Based on original configs from [ansible-lanparty](https://github.com/ti-mo/ansible-lanparty).
- Everyone on [/r/lanparty](https://reddit.com/r/lanparty) who has provided feedback and helped people with this.
- UK LAN Techs for all the support.
