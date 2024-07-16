---
title: Lancache-DNS
menu_name: General
description: Our DNS intercept container
permalink: /docs/containers/dns/
---

## Introduction

This docker container provides DNS entries for caching services to be used in conjunction with a  container.

The DNS is generated automatically at startup of the container, the list of supported services is available [here](https://github.com/uklans/cache-domains)

The primary use case is gaming events, such as LAN parties, which need to be able to cope with hundreds or thousands of computers receiving an unannounced patch - without spending a fortune on internet connectivity. Other uses include smaller networks, such as Internet Cafes and home networks, where the new games are regularly installed on multiple computers; or multiple independent operating systems on the same computer.

## Quick Explanation

For a LAN cache to function on your network you need two services.

* A depot cache service e.g [monolithic](/docs/containers/monolithic/)
* A special DNS service e.g this container

The depot cache service transparently proxies your requests for content to Steam/Origin/Etc, or serves the content to you if it already has it.

The special DNS service handles DNS queries normally (recursively), except when they're about a cached service and in that case it responds that the depot cache service should be used.

## Usage

If you are using a single Monolithic instance, you should make sure you set USE_GENERIC_CACHE=true and set LANCACHE_IP to the IP address of the caching server.

Run the lancache-dns container using the following to allow UDP port 53 (DNS) through the host machine:

```sh
docker run --name lancache-dns -p 10.0.0.2:53:53/udp -e USE_GENERIC_CACHE=true -e LANCACHE_IP=10.0.0.3 lancachenet/lancache-dns:latest
```

If you are running a dedicated Monolithic instance for a particular CDN, you can specify a different IP for each service hosted within the cache. For a full list of supported services have a look at [uklans/cache-domains](https://github.com/uklans/cache-domains). Set the IP for a service using ${SERVICE}CACHE_IP environment:

```
LANCACHE_IP (requires USE_GENERIC_CACHE to be set to true)

BLIZZARDCACHE_IP
FRONTIERCACHE_IP
ORIGINCACHE_IP
RIOTCACHE_IP
STEAMCACHE_IP
UPLAYCACHE_IP
```

If you are using `USE_GENERIC_CACHE` and `LANCACHE_IP` you can choose to disable any of the cache dns resolvers by setting the environment variable of DISABLE_${SERVICE}=true

```
DISABLE_BLIZZARD
DISABLE_RIOT
DISABLE_UPLAY

```

To use a custom upstream DNS server, use the `UPSTREAM_DNS` variable:

```sh
docker run --name lancache-dns -p 10.0.0.2:53:53/udp -e STEAMCACHE_IP=10.0.0.3 -e UPSTREAM_DNS=8.8.8.8 lancachenet/lancache-dns:latest
```

This will add a forwarder for all queries not served by steamcache to be sent to the upstream DNS server, in this case Google's DNS.  If
you have a DNS server on 1.2.3.4, the command argument would be `-e UPSTREAM_DNS=1.2.3.4`.

## Multiple IPs

Should you wish a cache server to have multiple IP addresses (for example a monolithic instance tuned for steam) you may specify them as a space delimited list within quotes for example: `-e STEAMCACHE_IP="1.2.3.4 5.6.7.8"`

## Custom DNS Records

If you would like to add custom records to your DNS server (for example additional services, ad-blocking, etc) you can do so by adding records to the `/etc/bind/cache/custom.db` file within the container. The easiest way to do this, upon container creation, is to bind a local file to a container file. 

To do that, the command argument would be: `-v /<host path here>/custom.db:/etc/bind/cache/custom.db`

Here is an example of what that `custom.db` could look like:
```
notes.<your domain>        A       10.0.0.230
games.<your domain>        A       10.0.0.230
files.<your domain>        A       10.0.0.231
```
The `custom.db` uses the RPZ zone format. For more information on the RPZ format, see the [BIND9 documentation](https://downloads.isc.org/isc/bind9/9.14.6/doc/arm/Bv9ARM.ch05.html#zone_file).

## Running on Startup

Follow the instructions in the Docker documentation to run the container at startup.
[Documentation](https://docs.docker.com/config/containers/start-containers-automatically/)

## Custom Forks/Branches

See [cache_domains](/docs/containers/monolithic/cache_domains/) for details on how to use a non-standard list of hostnames for this container.
