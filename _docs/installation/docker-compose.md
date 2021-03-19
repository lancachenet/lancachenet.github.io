---
title: Docker-Compose
description: Official guide to install lancache.net
permalink: /docs/installation/docker-compose/
---

The best and quickest way is to get started with lancache.net is to use the [quickstart](/docs/home/). The below guides give more information on what is available under the hood and provide more information on the specific setup options


## Requirements

* A modern Linux distribution supporting Docker, eg [Ubuntu](https://www.ubuntu.com) or [CentOS](https://www.centos.org/)
* [Docker](https://www.docker.com/)
* [docker-compose](https://docs.docker.com/compose/install/)

## Checkout Docker-compose

Our docker-compose repo provides everything you need to get up and running with a basic setup. Start by pulling the docker-compose as follows
```sh
git clone https://github.com/lancachenet/docker-compose.git
```

## Environment Setup

Before starting the containers it's important to configure the basics by editing the `.env` file. Work through the file and read the comments before updating each setting. 

<div class="note info">
  <h5>Getting stuck?</h5>
  <p>There are a few <a href="/docs/installation/docker-compose/env/">additional notes</a> for each variable to help you set it to something "sensible"
  </p>
</div>

## Ready to go
We are now ready to start the stack. You can bring the lancache.net stack up typing
```sh
docker-compose up -d
```
If you wish to stop it later simply type
```sh
docker-compose down
```


## Configuring your firewall
Once lancache-dns and monolithic are up and running you need to configure your router to hand out the IP address of the lancache-dns instance instead of your usual default. This is called "DNS poisoning" and is the primary intercept method for our cachable traffic. 

The ideal solution when deploying lancache.net is to distribute the IP of your lancache-dns server via dhcp. Many commercial routers will have an option under __LAN settings__ (or similar) to change the _DNS Server IP_. Unfortunately not all consumer brand routers are so versatile so if you cannot find a LAN DNS setting you can use the WAN settings instead. There are many different makes and models of router and each is configured differently. We have put together a guide for some common makes and models which can be found [here](/docs/installation/routers/)

<div class="note error">
	<h5>It's always DNS</h5>
    <p>
    	It's very important the lancache-dns is the ONLY dns server given out to your clients. Any other dns servers should be configured using the UPSTREAM_DNS option in the .env file.
    </p>
</div>

## Testing your cache
Now you've configured your dns, started that stack and are ready to test things are operating. Here's some very basic diagnostics and tests we would suggest to run to see if things are working.

1. Check your dns is being intercepted
* Run a few queries using command prompt to check that your dns is pointing at your cache, the following should all result in your cache ip if everything is working
```bat
ipconfig /flushdns
nslookup steam.cache.lancache.net
nslookup lancache.steamcontent.com
```

2. Download a game through steam
* If everything is working you should find the first time you download a game speeds which are equivalent to your internet speed. You  may find that initial downloads through the cache are slightly slower, but thats ok.

    <div class="note info">      
    <h5>Why do we cache</h5>   
    <p>
    We cache traffic in order to provide benefit to many users in a large environment. LanCache is optimised for hundreds to thousands of gamers downloading at once not one or two users on a small scale. The emphasis is always on superior cached performance over uncached.
    </p>
    </div>                          


3. Uninstall the first game and download again
* This time you should find your game downloads much faster (depending on your limiting factor: Server Disk, Client Disk, Network, see [hardware](/docs/hardware/).

4. Try a few other cdns

5. Precache some of the more popular games you might expect to happen at your event
* Although precaching isn't necessary, it certainly helps the initial demand on a cache when everyone turns up at the same time.

6. Get your friends round and enjoy the entire lan being able to download a game for only 1x external traffic.

<div class="note">
  <h5>Stay Up to Date</h5>
  <p>We recommend you update your lancache.net containers regularly to benefit from
  the latest bug fixes. Check out our guide on <a href="/docs/upgrading">upgrading &rarr;</a>
  </p>
</div>

