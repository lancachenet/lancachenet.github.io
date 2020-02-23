---
title: Quickstart
permalink: /docs/
redirect_from:
  - /docs/home/
  - /docs/extras/
---


## The simplest method

If you have a Linux machine that already has Docker pre-installed, please just run the following commands:

    git clone https://github.com/lancachenet/docker-compose/ lancache
    cd lancache
    nano .env
    docker-compose up
    # => Configure your router to serve lancache-dns

This should bring up a fully functional LanCache and DNS container, and an SNI Proxy to handle passthrough of https traffic.
