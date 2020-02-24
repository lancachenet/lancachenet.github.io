---
title: General
description: Our sni proxy container
permalink: /docs/containers/sniproxy/
---

## Introduction

When running a LAN Cache and overriding DNS entries, there are some services (including the Origin launcher) which will try and use HTTPS to talk to one of the hostnames that are being overridden. This breaks updates to the Origin client.

The solution is to run SNI Proxy on all IP addresses on the LAN Cache server. This accepts the HTTPS requests, looks at the host being requested and sends the request on to the correct server.

## Why do I need an SNI Proxy

SNI Proxy allows hostnames that serve BOTH http and https content to be overridden and pointed to your cache server. Traffic going to that hostname on port 80 (http) will hit the cache container and be cached, whilst traffic on port 443 (https) is passed straight through to the internet by the SNI Proxy container.

### Can SNI Proxy cache encrypted traffic

It does NOT allow https / encrypted content to be inspected or cached - merely ensure that it is passed straight through to the internet to avoid breaking anything whilst allow us to cache the HTTP traffic from that same hostname.

## Usage

Run the proxy container using the following to allow TCP port 443 (HTTPS) through the host machine:

```sh
docker run --name sniproxy -p 443:443 lancachenet/sniproxy:latest
```

## Changing Upstream DNS

If you need to change the upstream DNS server the cache uses, these are defined by the `UPSTREAM_DNS` environment variable. The defaults are Google DNS (8.8.8.8 and 8.8.4.4).

```
 UPSTREAM_DNS 8.8.8.8 8.8.4.4
```

You can override these using the `-e` argument to docker run and specifying your upstream DNS servers. Multiple upstream dns servers are allowed,  separated by whitespace.

```
-e UPSTREAM_DNS="1.1.1.1 1.0.0.1"
```

## Running on Startup

Follow the instructions in the Docker documentation to run the container at startup.
[Documentation](https://docs.docker.com/config/containers/start-containers-automatically/)

## Thanks

The SNI Proxy config and Dockerfile are from the [OpenSourceLAN origin-docker](https://github.com/OpenSourceLAN/origin-docker) project on GitHub.
