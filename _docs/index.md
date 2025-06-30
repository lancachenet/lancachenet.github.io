---
title: Quickstart
permalink: /docs/
redirect_from:
- /docs/home/
- /docs/extras/
---


## Prerequisites

The best and quickest way is to get started with lancache.net is to use the quickstart. Before you begin make sure you have: 

* A modern Linux distribution supporting Docker, eg [Ubuntu](https://www.ubuntu.com) or [CentOS](https://www.centos.org/)
* [Docker](https://www.docker.com/)

<div class="note">
    <p>
    	Installing Docker via your package manager may install an unsupported out of date version.  It is recommended to install it via Docker's official install instructions, which can be found <a href="https://docs.docker.com/engine/install/">here</a>
    </p>
</div>

## The Simplest Method

If you have a Linux machine that already has Docker pre-installed, please just run the following commands:

<div class="unit code code-terminal">
<span class="title">Terminal</span>
<div class="shell">
<p class="line">
<span class="path">~</span>
<span class="prompt">$</span>
<span class="command">git clone https://github.com/lancachenet/docker-compose/ lancache --depth=1</span>
</p>
<p class="line">
<span class="path">~</span>
<span class="prompt">$</span>
<span class="command">cd lancache</span>
</p>
<p class="line">
<span class="path">~/lancache</span>
<span class="prompt">$</span>
<span class="command">nano .env</span>
</p>
<p class="line">
<span class="path">~/lancache</span>
<span class="prompt">$</span>
<span class="command">docker compose up -d</span>
</p>
<p class="line">
<span class="output"># => Configure your router to serve ONLY lancache-dns</span>
</p>
</div>
</div>


This should bring up a fully functional LanCache and DNS container, and an SNI Proxy to handle passthrough of https traffic.

<div class="note error">
	<h5>It's always DNS</h5>
    <p>
    	It's very important the lancache-dns is the ONLY dns server given out to your clients. Any other dns servers should be configured using the UPSTREAM_DNS option in the .env file.
    </p>
</div>

## More Detail

When you edit the .env file most users are going to need to make the following changes:
* `LANCACHE_IP` should be set to the ip address that you wish your DNS to hand out for your cache container. In normal operation this would be the ip of the box running your cache.
* `DNS_BIND_IP` can be commented out in a simple setup or you can choose the ip address of your dns container (which could be the same as your lancache ip)
* `CACHE_ROOT` is where you want to store the cached data. For best practice we recommend a mount point on a separate volume from your OS.
* `CACHE_DISK_SIZE` ensure this matches the size of the volume you have the cache root on. The cache will try to keep around 100g free space, but you may want to pick an appropriate size that leaves some space available for other data. The size should be in gigabytes (suffix `g`) or terabytes (suffix `t`). (For very large sizes you should understand what `CACHE_INDEX_SIZE` does)

<div class="note info">
	<h5>Common Issues</h5>
    <p>
    	Be sure to check the <a href="/docs/common-issues/">common issues</a> page if you have any problems setting up your server.
    </p>
</div>

## More Advanced Information
If you want to play with advanced installation and further configuration options we recommend starting with one of the guides below. For detailed install instructions have a look at one of the following guides.

* [docker-compose](/docs/installation/docker-compose/)
* [Manual docker](/docs/installation/docker/)
