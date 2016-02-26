# SteamCache.Net

SteamCache.Net provides the ability to reduce your Internet bandwidth consumption in scenarios where you have multiple PC gamers sharing the same Internet connection. Through the use of easy to deploy docker containers we aim to make it easy for you to get a local cache of steam's content servers up and running quickly.

The primary use case is gaming events, such as LAN parties, which need to be able to cope with hundreds or thousands of computers receiving an unannounced patch - without spending a fortune on internet connectivity. Other uses include smaller networks, such as Internet Cafes and home networks, where new games are regularly installed on multiple computers; or multiple independent operating systems on the same computer.

## Prerequisites

 * Some local dedicated server hardware

	Lots of RAM and a reasonably fast SSD or HDD is recommended. Regular commodity hardware (a single 2TB WD Black on an HP Microserver) can achieve peak throughputs of 30MB/s+ using this setup (depending on the specific content being served).

 * Linux with Docker

 	We suggest [Ubuntu server](http://www.ubuntu.com/server) because it is really easy, but any Linux is ok. Running on Windows or Mac OS X is not recommended for performance reasons. Please see the [Docker installation instructions](https://docs.docker.com/engine/installation/).

## Usage

1. Run the steamcache reverse proxy container

	```
	docker run --name steamcache -p 80:80 steamcache/steamcache
	```

	Note that this container requires the use of port 80 and so may not be run on the same machine as some other website. A dedicated caching server is recommended.

2. Run the steamcache DNS server container

	When starting this container you need to pass in the IP address which the reverse proxy container is running on. Assuming that you're running both containers on the same machine then this will just be the IP address of that machine.  This command looks up that IP and then starts the container using it.

	```shell
	export STEAMCACHE_IP=`ip -4 addr show docker0 | grep -Po "inet \K[\d.]+"`;
	docker run --name steamcache-dns -p 53:53/udp -e STEAMCACHE_IP=$STEAMCACHE_IP steamcache/steamcache-dns
	```

	If you want to specify  the IP then just place it in the run command like

	```shell
	docker run --name steamcache-dns -p 53:53/udp -e STEAMCACHE_IP=111.111.111.111 steamcache/steamcache-dns
	```

	Now anyone using this DNS server will have their steam traffic severed by the cache.

3. Change your DHCP server to give out the IP address of the steamcache DNS container as primary DNS

Now, as people join your network they will automatically be configured to use the steam cache (unless they've chosen to override DNS on their local PC).

## Running on Startup

Please follow the [instructions in the Docker documentation](https://docs.docker.com/articles/host_integration/) to run the container at startup.
