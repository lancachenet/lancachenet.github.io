---
title: Common Issues
permalink: "/docs/common-issues/"
---

## Introduction

There are several issues that frequently crop up when deploying lancache. Here is the information on how to handle them if they happen to you.

## Disabling systemd-resolved DNSStubListener

If your cache host is running a recent Linux distribution, it is likely running `systemd-resolved`, which listens on port `53`. If you want your cache host to bind to `0.0.0.0:53` (INADDR_ANY, all IPv4 addresses), you will first need to disable `systemd-resolved`'s stub listener.

1. Run `sudo -e /etc/systemd/resolved.conf` and change the line starting with `#DNSStubListener=` to `DNSStubListener=no` - _removing the `#` is important_
1. Run `sudo rm /etc/resolv.conf`
1. Run `sudo ln -s /var/run/systemd/resolve/resolv.conf /etc/resolv.conf`
1. Run `sudo service systemd-resolved restart`
1. Check that you can still resolve DNS on the cache server by running `nslookup` for a domain of your choice (e.g. `lancache.net`)

## Unraid Port Bindings

Unraid provides network storage, virtual machines and docker functionality and on the face of it seems a great platform for running up your lancache. However people often run into problems with ports. To use lancache you need to have unchalenged access to:
* Port 80: where HTTP content is requested.
* Port 443: so that HTTPS is handled.
* Port 53: so that DNS requests can be directed.

These are usually in heavy rotation by the unraid UI. While some users have managed to add another IP and move some services arround there is an easier way. Simply run up an unbuntu VM on unraid. You can assign an uncontested IP to your VM with ease and now you are on the "happy path" for install. Use the Separation of Concerns principle and put your caching all inside one easy to operate wrapped up VM of its own.