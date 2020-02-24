---
title: Cache Domains
description: Monolithic's relationship with UKLans/Cache_Domains
permalink: /docs/containers/monolithic/cache_domains/
---

## Introduction

The [uklans/cache-domains](https://github.com/uklans/cache-domains) repository is a community generated list of hostnames that serve game related content that is cached by Monolithic. It is broken down by service / CDN and the aim is for this to be a definitive list of all domains you might want to cache.

Whilst lancache (monolithic and lancache-dns) includes this list by default, you can use your own fork / custom branch if you wish to add or remove any particular service.

## Custom Forks/Branches

If you have your own fork (or branch) forked from [uklans/cache-domains](https://github.com/uklans/cache-domains) and would like to use it for testing purposes (before pushing it to the main branch) or cache from unofficially supported domains, then declare it with `CACHE_DOMAINS_REPO` including the full .git URL for your fork, for example:

```sh
docker run --restart unless-stopped --name lancache-dns --detach -p 53:53/udp -e USE_GENERIC_CACHE=true -e LANCACHE_IP=$HOST_IP -e CACHE_DOMAINS_REPO="https://github.com/your-username/cache-domains.git" lancachenet/lancache-dns:latest
```

and

```sh
docker run --restart unless-stopped --name lancache --detach -v /cache/data:/data/cache -v /cache/logs:/data/logs -p 80:80 -e CACHE_DOMAINS_REPO="https://github.com/your-username/cache-domains.git" lancachenet/monolithic:latest
```

which would use the cache domains from https://github.com/your-username/cache-domains.git

Should you wish to run a custom branch you can also specify `-e CACHE_DOMAINS_BRANCH="branch-name"`

You should set this environment variable against both monolithic and lancache-dns as both make use of the information.
