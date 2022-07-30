---
title: Common Issues
permalink: "/docs/common-issues/"
---

There are several issues that frequently crop up when deploying Lancache. Here is the information on how to handle them if they happen to you.

{% include toc.html %}

## It's not DNS ...

The most common cache issue we see is incorrectly configured DNS. It is essential that gaming machines only have one dns server assigned, that being Lancache. If you have two different ones then the client will use them both and bypass the cache for about half of the requests. While the mechanism operating systems use to choose which dns server to use is complicated it is a suitable summary to say its "at random".


## Disabling systemd-resolved DNSStubListener

If your cache host is running a recent Linux distribution, it is likely running `systemd-resolved`, which listens on port `53`. If you want your cache host to bind to `0.0.0.0:53` (INADDR_ANY, all IPv4 addresses), you will first need to disable `systemd-resolved`'s stub listener.

1. Run `sudo -e /etc/systemd/resolved.conf` and change the line starting with `#DNSStubListener=` to `DNSStubListener=no` - _removing the `#` is important_
1. Run `sudo rm /etc/resolv.conf`
1. Run `sudo ln -s /var/run/systemd/resolve/resolv.conf /etc/resolv.conf`
1. Run `sudo service systemd-resolved restart`
1. Check that you can still resolve DNS on the cache server by running `nslookup` for a domain of your choice (e.g. `lancache.net`)

## Battle.net Slow Initial Download

The Battle.net client is known to suffer from incredibly slow downloads, 1mbit/s or less, when downloading for the first time with an empty cache.
These slow downloads are due to how Battle.net poorly interacts with Lancache, and are unfortunately unable to be resolved in Lancache itself.

As a workaround, [battlenet-lancache-prefill](https://github.com/tpill90/battlenet-lancache-prefill) can be used to prime Battle.net games at full speed, , enabling subsequent downloads to be retrieved from the Lancache.

## Unraid Port Bindings

Unraid provides network storage, virtual machines and docker functionality and on the face of it seems a great platform for running up your Lancache. However people often run into problems with ports. To use Lancache you need to have unchallenged access to:
* Port 80: where HTTP content is requested.
* Port 443: so that HTTPS is handled.
* Port 53: so that DNS requests can be directed.

These are usually in heavy rotation by the Unraid UI. While some users have managed to add another IP and move some services around there is an easier way. Simply run up an Ubuntu VM on Unraid. You can assign an uncontested IP to your VM with ease and now you are on the "happy path" for install. Use the Separation of Concerns principle and put your caching all inside one easy to operate wrapped up VM of its own.

## Slow Download Speeds

Its quite common to see different or odd behaviour at the download client with a lancache in your network. Sometimes this is as simple as the way the client does the maths on showing your current speed. An example would be:
1. Client requests 20MB file.
1. Lancache intercepts the request and makes the same request from the internet
1. The client is now waiting and getting no data as the cache fetches the file. Speed is zero at the client.
1. Lancache finishes downloading and informs the client.
1. Client downloads 20MB over a 10Gbit nic to an M2 harddrive at speeds the 1990s wouldn't even dare dream of and gets the file almost instantly. The average speed in the client blips upto 100Kbps and returns to zero.

It worth remembering that your cache is aiming to provide for many people and that using one client to assess speed can be misleading. That said there are some games that have odd behaviour that can be improved. Some CDNs use back off mechanics that don't play well with the slicing module in nginx. Others have "interesting" behavior with range requests. For the most part we have found the standard behaviour to be satisfactory but if you want to try and tune it, try out the [advanced section](/docs/advanced/tuning-cache).

## IPv6

Lancache, and in particular lancache-dns, is not currently enabled for IPv6. Therefore we recommend checking your LAN Party network and ensuring that if you are providing or allowing IPv6 DNS servers, that clients are not able to get the external v6 address for the major CDNs. If they do get a "none poisoned" IP via IPv6 then they will go direct to the internet for the files and bypass the cache totally.

Future support for IPv6 is being investigated but is at an early stage.

## Non-RFC1918 IP Ranges

Several of the most popular game clients/launchers, including Steam Origin and Riot, only work properly if they detect the cache is on an RFC1918 private IP address (i.e 10.0.0.0/8, 172.16.0.0/12 or 192.168.0.0/16). 

The clients have built in logic that will ONLY enable their built in TLS downgrade / HTTP fallback modes if the hostnames they are connecting to resolve to an RFC1918 private address. If the hostnames resolve to a public IP range (even if that range sits on your local network) the client will use encryption and thus lancache will not be able to cache the traffic.

As this is built in logic from the games companies, there is no work around for this situation. However even if your local network is setup to give client machines a public ip address, you can still setup lancache on a private IP and configure your router to perform the appropriate routing or NAT locally - it is only the hostnames pointing to the lancache itself that must resolve to a private IP and it does not matter what IP space the clients use.

The exact reason for this logic is known only to the game companies in question, but it is likely a failsafe measure to ensure that they are only downgrading to unencrypted communication when they can be 100% sure they are talking to a local cache and not sending unencrypted traffic over the internet to a public ip that could be anywhere.

## Can't resolve DNS with a Centos 8 server

There appears to be a fault between the inbuilt firewall and docker on some of the linux variants. Centos 8 has this issue and we have also seen it on fedora. As primary advice we suggest that ubuntu is a easy to use linux choice. If you have chosen Centos 8 or similar on purpose then there are work arounds for the problem. [As described here](https://serverfault.com/questions/987686/no-network-connectivity-to-from-docker-ce-container-on-centos-8) you can use masquerading to combat the problem between nftables, iptables and docker.