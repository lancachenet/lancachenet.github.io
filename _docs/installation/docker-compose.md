---
title: Docker-compose
description: Official guide to install lancache.net
permalink: /docs/installation/docker-compose/
---

The best and quickest way is to get started with lancache.net is to use the [quickstart](/docs/home/). The below guides give more information on what is available under the hood and provide more information on the specific setup options


## Requirements

* A modern Linux distribution supporting Docker, eg [Ubuntu](https://www.ubuntu.com) or [CentOS](https://www.centos.org/)
* [Docker](https://www.docker.com/)

## Checkout Docker-compose

Our docker-compose repo provides everything you need to get up and running with a basic setup. Start by pulling the docker-compose as follows
```sh
git clone https://github.com/lancachenet/docker-compose.git
```

## Environment Setup

Before starting the containers it's important to configure the basics by editing the `.env` file. Work through the comments and check out the additional notes below for each variable to ensure it's set to something "sensible"

## Ready to go
We are now ready to start the stack. You can bring the lancache.net stack up typing
```sh
docker-compose up
```
If you wish to stop it later simply type
```sh
docker-compose down
```


## Configuring your firewall
Once lancache-dns and monolithic are up and running you need to configure your router to hand out the IP address of the lancache-dns instance instead of your usual default. This is called "DNS poisoning" and is the primary intercept method for our cachable traffic. 

There are many different makes and models of router and each is configured differently. We have put together a guide for some common makes and models which can be found [here](/docs/installation/routers/)

## Testing your cache
Now you've configured your dns, started that stack and are ready to test things are operating. Here's some very basic diagnostics and tests we would suggest to run to see if things are working. For more advanced tools and techniques head on over to [diagnostics](/docs/diagnostics/)

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
    We cache traffic in order to provide benefit to many users in a large environment. LanCache is optimised for hundreds to thousands of gamers downloading at once not one or two users on a small scale. The emphasis is always on superior cached performance over uncached. <a href="/docs/caching-101/">Learn more &rarr;</a>
    </p>
    </div>                          


3. Uninstall the first game and download again
* This time you should find your game downloads much faster (depending on your limiting factor: Server Disk, Client Disk, Network, see [hardware](/docs/hardware/).

4. Try a few other cdns

5. Precache some of the more popular games you might expect to happen at your event
* Although precaching isn't nessecary, it certainly helps the initial demand on a cache when everyone turns up at the same time.

6. Get your friends round and enjoy the entire lan being able to download a game for only 1x external traffic.

<div class="note">
  <h5>Stay Up to Date</h5>
  <p>We recommend you update your lancache.net containers regularly to benefit from
  the latest bug fixes. Check out our guide on [upgrading](/docs/upgrading/)
  </p>
</div>

