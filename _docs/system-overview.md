---
title: System Overview
permalink: /docs/system-overview/
draft: true
---

"LAN" is the environment where many people are sharing an internet connection. Many people might be 2 or 10,000, however the same problems and opportunities exist. On small bandwidth connections (most LANs tend to have ~1.5Mbps per person) there is a high contention for the chance to download from the internet. With many hundreds of people the chance you are downloading the same content many times increases radically. The situation provides the opportunity for caching to help.

Let's look at where caching fits into your network.

## The Basic Network

The basic anatomy of a LAN network consists of the WAN side where you keep your internet access (this might be one or many circuits) and the LAN side where you keep "client" PCs. In order to sensibly discuss caching you also need to define a few more components of interest. 
* A router/firewall: Something in the network is connected to the internet (WAN) and controls the access to that for everything else (LAN)
* A core switch: While you many have many switches chained together for your "many" clients, here we will regard the core as the part that connects clients and the internet router. The core is also where the cache will join in.
* DNS: There are many schemes for having a local name server but conceptually something chooses how things LAN side find the address of anything that has a named address. It is this component that we will intercept to allow caching.
 
 If you consider a basic home setup all of these components are in the thing they shipped you and is now plugged into the phone line (where we used to keep bakelite rotary dial telephones). For a huge LAN party then we might have a dedicated firewall routing traffic, a stacked set of industry level switches controlling the core and a server farm that hosts the DNS service for the network. The concepts are the same in both instances.

## Adding a Cache

In the basic network above, every time we want to download a game we have to fetch it from the internet on the WAN side and put it on a client PC on the LAN side. If many people want to do this at once they all have to get separate copies. This means it gets downloaded several times, once per PC wanting the game. If you are running an event with 10 identical PCs to show off a game or have hundreds of eager LAN gamers wanting the lastest shiny game of the moment, it can be a lot of strain on your internet. 

The purpose of a cache is to allow you to download the game once, crossing from WAN to LAN and then to allow as many clients as you like to grab a copy within your network. This wants to be transparent to the clients, by this we mean they should not need to know that caching is happening in the network and can just use their machine as they normally would. To enable this we must satisfy a few main conditions:
* DNS Redirection: In order to convince clients to grab the LAN copy, we will have to tell them that the content they are requesting is kept on the cache machine.
* Proxy Requests: When the cache doesn't have a LAN copy of a game already, it will need to act as the clients proxy and pass the request on through to the WAN side. To keep the transparent appearance that the network has internet there will also be a need to pass requests that are not to be cached onwards. 
* Cache Storage: The copy of the game must go somewhere and so we will need a machine with plenty of disk space to put it on. Generally the aim is to support many concurrent clients pulling a copy off the cache so we will want those disks to be fast. We will also want the machine to be able to keep up with the requests. ([See the hardware section]({% link _docs/hardware.md %}))

These are the steps that the lancache solution helps provide. Understanding these terms will hopefully allow you to integrate it into your network. Lets explore a basic scheme that should be applicable for most networks.

## Lancache Example

Having followed the installation guide we now have a linux server with plenty of memory, cpu cores and fast disk storage. On it we have three services running in docker.
* [Lancache-DNS]({% link _docs/containers/dns/index.md%}): this service is aware of which requested domains should be directed to the cache and which should be passed on the internet.
* [Lancache]({% link _docs/containers/monolithic/index.md %}): This component does the heavy lifting. It fetches content from the internet, slices it up and stores it to disk. Then it passes a copy of the content to the requester and will hand out additional copies when other clients also request the same data.
* [SNI Proxy]({% link _docs/containers/sniproxy/index.md %}): In some cases the location in the internet that stores the game and provides HTTP access, also has some HTTPS content that is required under the same named address. This component listens for HTTPS content requests and securely forwards them to the original server.

The next installation step is physical. Our server needs to be connected to the network. We will do this at the core switch. The reason it goes there is that it provides access to clients and the router in a central location. It also gives the best options for optimising traffic within our network in more advanced cases.

Following a physical install the next step is for us to change how clients look up addresses. In our imaginary network we provide addresses to clients by DHCP. We will alter our routers settings to now hand out the ip address of our lancache-dns service as their DNS server rather than using the router itself. The two most basic configurations we could now adopt are:
* 8.8.8.8 : Use a well known public DNS service as the defined upstream in the container config.
* Router : If there are other things in the network that require local named addresses (other services or internally hosted items) then it might make sense to use the router as the upstream DNS. This will still return internet addresses for us but will also know about the LAN addresses we have.

There are many more complex schemes for DNS, advanced topics may discuss some however the goal is to ensure that when a client request a content address they are directed to the cache container and not the internet.

#### Notes:

- More complex networks may have the servers and clients in different subnets. In these cases take care to consider the route traffic takes. You might have a 10Gbit fibre connection from your server but if the route between the subnets requires traffic to go up a 100Mbit connection to a standard home router and back again your clients will not be get their game very quickly. Either have your cache in the same subnet or for more advanced users home your gateway on your core switch and ensure routing can go directly.   
- You can get a basic check of your DNS setup using [http://diagnostics.lancache.net](http://diagnostics.lancache.net/). NSLookup <address> is a good indication that a redirection is working but is not always conclusive.
