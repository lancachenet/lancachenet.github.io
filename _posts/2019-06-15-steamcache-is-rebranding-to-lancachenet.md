---                                                 
title: 'SteamCache is rebranding to LanCache.Net, now with added Epic Games launcher support'
date: 2019-06-15 09:00:00 -0000
author: vibroaxe
version: epic
categories: [cdn,release]
---

As many of you know I'm one of the small team of maintainers on the steamcache/* docker images (along with @mintopia @GotenXiao @Lepidopterist @JasonRivers and @Astrolox). For some time now we have felt that we have long outgrown our steamcache roots and perhaps it was time to rebrand to the generic gaming cache which we now provide. After much consideration and some discussions with Multiplay and ChurchNerd we are proud to rebrand with immediate effect to LanCache.Net



All docker images have been updated and are available at https://cloud.docker.com/lancachenet/ we will continue to keep the steamcache images updated in line with these for the moment to ease migration but it's well worth switching your images at the next opportunity.



Now, onto the REALLY big news!



The LanCache.Net and UKLans.net teams have been in discussions with the store team at Epic Games for the last few months. We are incredibly pleased to announce that with immediate effect Epic Games have migrated their CDNs and client to download over HTTP instead of HTTPS. This means those using our lancache-dns and monolithic containers will immediately be able to cache all games from the Epic Store!

This is still in beta testing right now (Pass -E DISABLE_EPIC_GAMES to the dns container if you wish to disable the EPIC store caching for the time being) but initial testing results are positive so we wanted to share this with you all as soon as possible. If you encounted any issues with epic then do raise an issue over at https://github.com/lancachenet/monolithic/issues/new



Huge thanks everyone over at Epic Games for working with us to a solution on this, the rest of the LanCache.Net team and of course everyone who has contributed issues, feedback, issues and pull requests!



Happy Gaming

@VibroAxe



Note: Most containers retain their original names so steamcache/monolithic becomes lancachenet/monolithic. The only exception to this is steamcache/steamcache-dns which has become lancachenet/lancache-dns


