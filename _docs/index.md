---
title: Quickstart
permalink: /docs/
redirect_from:
- /docs/home/
- /docs/extras/
---


## Prerequisites

* See [requirements](/docs/installation#requirements)

## The simplest method

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
<span class="command">docker-compose up</span>
</p>
<p class="line">
<span class="output"># => Configure your router to serve lancache-dns</span>
</p>
</div>
</div>


This should bring up a fully functional LanCache and DNS container, and an SNI Proxy to handle passthrough of https traffic.

## More Detail

If you want to play with advanced installation and further configuration options we recommend starting with the [Installation](/docs/installation/) page.
