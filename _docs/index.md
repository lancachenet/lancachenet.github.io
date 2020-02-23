---
title: Quickstart
permalink: /docs/
redirect_from:
- /docs/home/
- /docs/extras/
---


## Prerequisites

* See [requirements](/docs/installation#requirements)

## The Simplest Method

If you have a Linux machine that already has Docker pre-installed, please just run the following commands:

<div class="unit code code-terminal">
<p class="title">Terminal</p>
<div class="shell">
<p class="line">
<span class="path">~</span>
<span class="prompt">$</span>
<span class="command">git clone https://github.com/lancachenet/docker-compose/ lancache</span>
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
<span class="command">docker-compose up -d</span>
</p>
<p class="line">
<span class="output"># => Configure your router to serve lancache-dns</span>
</p>
</div>
</div>


This should bring up a fully functional LanCache and DNS container, and an SNI Proxy to handle passthrough of https traffic.

## More Detail

When you edit the .env file most users are going to need to make the following changes:
* LANCACHE_IP should be set to the ip address that you wish your DNS to hand out for your cache container. In normal operation this would be the ip of the box running your cache.
* DNS_BIND_IP can be commented out in a simple setup or you can choose the ip address of your dns container (which could be the same as your lancache ip)
* CACHE_ROOT is where you want to store the cached data. For best practice we recommend a mount point on a separate volume from your OS.
* CACHE_DISK_SIZE ensure this matches the size of the volume you have the cache root on. Remember to leave some space availaible. (For very large sizes you should understand what CACHE_MEM_SIZE does)

If you want to play with advanced installation and further configuration options we recommend starting with the [Installation](/docs/installation/) page.