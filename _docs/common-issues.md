---
title: Common Issues
permalink: "/docs/common-issues/"
---

There are several issues that frequently crop up when deploying lancache. Here is the information on how to handle them if they happen to you.

{% include toc.html %}

## Disabling systemd-resolved DNSStubListener

If your cache host is running a recent Linux distribution, it is likely running `systemd-resolved`, which listens on port `53`. If you want your cache host to bind to `0.0.0.0:53` (INADDR_ANY, all IPv4 addresses), you will first need to disable `systemd-resolved`'s stub listener.

1. Run `sudo -e /etc/systemd/resolved.conf` and change the line starting with `#DNSStubListener=` to `DNSStubListener=no` - _removing the `#` is important_
1. Run `sudo rm /etc/resolv.conf`
1. Run `sudo ln -s /var/run/systemd/resolve/resolv.conf /etc/resolv.conf`
1. Run `sudo service systemd-resolved restart`
1. Check that you can still resolve DNS on the cache server by running `nslookup` for a domain of your choice (e.g. `lancache.net`)

## Unraid Port Bindings

Unraid provides network storage, virtual machines and docker functionality and on the face of it seems a great platform for running up your lancache. However people often run into problems with ports. To use lancache you need to have unchallenged access to:
* Port 80: where HTTP content is requested.
* Port 443: so that HTTPS is handled.
* Port 53: so that DNS requests can be directed.

These are usually in heavy rotation by the unraid UI. While some users have managed to add another IP and move some services arround there is an easier way. Simply run up an unbuntu VM on unraid. You can assign an uncontested IP to your VM with ease and now you are on the "happy path" for install. Use the Separation of Concerns principle and put your caching all inside one easy to operate wrapped up VM of its own.

## Slow Download Speeds

Its quite common to see different or odd behaviour at the download client with a lancache in your network. Sometimes this is as simple as the way the client does the maths on showing your current speed. An example would be:
1. Client requests 20MB file.
1. Lancache intercepts the request and makes the same request from the internet
1. The client is now waiting and getting no data as the cache fetches the file. Speed is zero at the client.
1. Lancache finishes downloading and informs the client.
1. Client downloads 20MB over a 10Gbit nic to an M2 harddrive at speeds the 1990s wouldn't even dare dream of and gets the file almost instantly. The average speed in the client blips upto 100Kbps and returns to zero.

It worth remembering that your cache is aiming to provide for many people and that using one client to assess speed can be misleading. That said there are some games that have odd behaviour that can be improved. Some CDNs use back off mechanics that don't play well with the slicing module in nginx. Others have "interesting" behavior with range requests. For the most part we have found the standard behaviour to be satifactory but if you want to try and tune it, try out the [advanced section](/docs/advanced/tuning-cache).