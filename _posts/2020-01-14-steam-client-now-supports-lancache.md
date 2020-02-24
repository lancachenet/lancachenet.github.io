---                                                 
title: 'Steam client now supports lancache!'
date: 2020-01-14 09:00:00 -0000
author: mintopia
version: steam
categories: [cdn,release]
---
Hi everyone, We have some great news that some of you may have already seen.

As of today's Steam Client update, there is now full support in Steam for a lancache. The way it works is by setting a DNS override for lancache.steamcontent.com pointing to the IP address of your cache server. The cache domains list at uklans/cache-domains has already been updated to include this and if you redeploy lancachenet/monolithic and lancachenet/lancache-dns containers, it will pick up the new hostname and work.

The Steam client will then use that cache server for all downloads. It will still make requests to the cache server using the hostname of the Valve content server to download from, but it will connect to your cache.

In addition, it will increase the maximum number of connections per IP from 4 to 32. This should solve the issue people have been seeing with slow initial downloads through lancache. It will also prevent the Steam Client from using Open Caching, which caused some downloads to not be cached correctly.

An example of it running from my local PC's Steam client (on the beta before Christmas) - the content.log file contains:

```
[2019-12-21 17:15:14] Enabling local content cache at '::ffff:c0a8:1e80' from lookup of lancache.steamcontent.com.
[2019-12-21 17:15:14] Adding cache type 'LANCache' on host '::ffff:c0a8:1e80'
[2019-12-21 17:15:14] Got 30 download sources and 0 caching proxies via ContentServerDirectoryService::BYieldingGetServersForSteamPipe (4/0)
[2019-12-21 17:15:14] Created download interface of type 'SteamCache' (7) to host cache12-lhr1.steamcontent.com (cache12-lhr1.steamcontent.com)
[2019-12-21 17:15:14] Created download interface of type 'SteamCache' (7) to host cache34-lhr1.steamcontent.com (cache34-lhr1.steamcontent.com)
[2019-12-21 17:15:14] Created download interface of type 'SteamCache' (7) to host cache26-lhr1.steamcontent.com (cache26-lhr1.steamcontent.com)
[2019-12-21 17:15:14] Created ipv4-only http client
[2019-12-21 17:15:14] HTTP (SteamCache,238) - cache26-lhr1.steamcontent.com (0.0.0.0:0 / 0.0.0.0:0, host: cache26-lhr1.steamcontent.com): AuthenticateDepotID (1521) - Success!
[2019-12-21 17:15:14] HTTP (SteamCache,238) - cache26-lhr1.steamcontent.com (192.168.30.128:80 / 192.168.30.128:80, host: cache26-lhr1.steamcontent.com): ::ffff:c0a8:1e80/depot/1521/manifest/6898858218219951821/5 - received 200 (OK) HTTP response
[2019-12-21 17:15:14] HTTP (SteamCache,43) - cache34-lhr1.steamcontent.com (192.168.30.128:80 / 192.168.30.128:80, host: cache34-lhr1.steamcontent.com): CDepotHTTPDownloadInterface::BYldTestConnection - Server response 'OK' (200)
```

I can't thank Valve enough for all their help with this, they've been amazingly supportive of getting these changes in to Steam. They're really interested in all your feedback about it, so if you have any feedback or comments on how well it's working for you - stories, case studies, problems, statistics, etc. then please post them here or in our issue on Github

Please also spread the word about this to anyone you think might be interested in this.
