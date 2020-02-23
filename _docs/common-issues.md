---
title: Common Issues
permalink: "/docs/common-issues/"
---

## Disabling `systemd-resolved` DNSStubListener

If your cache host is running a recent Linux distribution, it is likely running `systemd-resolved`, which listens on port `53`. If you want your cache host to bind to `0.0.0.0:53` (INADDR_ANY, all IPv4 addresses), you will first need to disable `systemd-resolved`'s stub listener.

1. Run `sudo -e /etc/systemd/resolved.conf` and change the line starting with `#DNSStubListener=` to `DNSStubListener=no` - _removing the `#` is important_
1. Run `sudo rm /etc/resolv.conf`
1. Run `sudo ln -s /var/run/systemd/resolve/resolv.conf /etc/resolv.conf`
1. Run `sudo service systemd-resolved restart`
1. Check that you can still resolve DNS on the cache server by running `nslookup` for a domain of your choice (e.g. `lancache.net`)