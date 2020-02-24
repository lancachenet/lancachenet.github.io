---
title:  Stored cache settings
menu_name: Stored Cache settings
permalink: /docs/advanced/config-hash/
---

## Cache invalidation based on configuration 

There are various settings in the Lancache cache containers which will invalidate existing data if they are altered.  In an effort to try to catch these, as of February 2020, the containers will now store the current values of these settings alongside the cache data itself.

* For new users installing for the first time, this will happen automatically.
* For existing users that have data in their cache but no settings file, the container will automatically generate a settings file based upon the containers current configuration, and will note that it has done so during startup.
```
    Executing hook /hooks/entrypoint-pre.d/05_config_check.sh
    Checking cache configuration
	 Could not find CONFIGHASH for existing cachedata
	  This is either an upgrade from an older instance of Lancache
	  or CONFIGHASH has been deleted intentionally
	
	 Creating CONFIGHASH from current live configuration
       Current:  GENERICCACHE_VERSION=2;CACHE_SLICE_SIZE=1m;CACHE_KEY=$uri$slice_range;
	
	  See: https://lancache.net/docs/advanced/config-hash/ for more details
```
* For existing containers that already created a settings file, if environment variables are subsequently changed that will invalidate your cache, you will get a message similar to the following when you try to run up the cache
``` 
    Executing hook /hooks/entrypoint-pre.d/05_config_check.sh
    Checking cache configuration
     Detected existing cache, checking config hash for consistency

    ERROR: Detected CONFIGHASH does not match current CONFIGHASH
     Detected: GENERICCACHE_VERSION=2;CACHE_SLICE_SIZE=1m;CACHE_KEY=$uri$slice_range;
     Current:  GENERICCACHE_VERSION=2;CACHE_SLICE_SIZE=2m;CACHE_KEY=$uri$slice_range;
    
    ABORTING STARTUP TO AVOID POTENTIALLY INVALIDATING THE CACHE
    
    If you are happy that this cache is valid with the current config changes
    please delete `/<cache_mount>/CONFIGHASH`
    
    See: https://lancache.net/docs/advanced/config-hash/ for more details
```

This will indicate the existing setting, and the new settings and __will require you to manually delete the settings file before the container will fully start__.

