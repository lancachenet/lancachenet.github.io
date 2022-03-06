---                                                 
title: 'Important LanCache Update'
date: 2022-03-08 09:00:00 -0000
author: vibroaxe
#version: monolithic
categories: [release]
---

We have identified an issue with the way lancache handles the CACHE\_MEM\_SIZE/CACHE\_INDEX\_SIZE environment variables. If you are running a cache with more than 2tb of CACHE\_DISK\_SIZE we suggest you immediately do the following

* Check the value of CACHE\_MEM\_SIZE/CACHE\_INDEX\_SIZE in your .env. We now recommend this should be \~250m per 1TB of cache space
* Update Lan Cache (./update-containers.sh if you are using the compose)

&#x200B;

***More Detail:***

Over the last 24 hours we have been diagnosing an issue where some caches were not filling their allocated CACHE\_DISK\_SIZE and started purging data out after \~2.5TB. This led to the discovery of an issues within our documentation and regression handling.

**Issue 1: CACHE\_MEM\_SIZE vs CACHE\_INDEX\_SIZE**

In October we updated the slightly confusing environment variable CACHE\_MEM\_SIZE to the more accurate CACHE\_INDEX\_SIZE. In order to prevent this breaking auto update scripts we have a deprecation check which is supposed to copy the value of CACHE\_MEM\_SIZE into CACHE\_INDEX\_SIZE on boot. Unfortunately in one of the two places this check is performed the script was erroring so all caches using the legacy CACHE\_MEM\_SIZE were being overwritten with the default CACHE\_INDEX\_SIZE of 500m.

This issue has now been resolved and we are looking at how to improve the deprecation handling and avoid these duplication errors in the future

&#x200B;

**Issue 2: How much CACHE\_INDEX\_SIZE do you actually need**

CACHE\_INDEX\_SIZE is the amount of memory allocated to nginx to store the index of cache data. Our original recommendation in the documentation allowed 1000mb which is enough to store 8 million files (or 8TB of cache data). At some point we reduced this recommendation to 500mb which we felt should still have been sufficient to handle an average 4tb cache. 

This recommendation is based on the assumption that the cache is full of 1mb slices. What we realised as part of our investigations into the cache not filling sufficiently is that the distribution of file sizes within the cache is actually a reverse bell curve, with much larger than expected numbers of \~<1kb files in addition to the expected large numbers of 1mb slices. 

<center><img src="/img/Reverse-Bell-Curve.png"></center>

Based on analysis of real world usage across all CDN's, we have now adjusted the recommendation to 250m per 1TB of cache data which allows 2 million files (at an average file size of 500kb). This should cover the variance with some headroom to spare.
