![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/monolithic?label=Monolithic) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/lancache-dns?label=Lancache-dns) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/sniproxy?label=Sniproxy) ![Docker Pulls](https://img.shields.io/docker/pulls/lancachenet/generic?label=Generic)




## Changing from lancachenet/steamcache and lancachenet/generic

This new container is designed to replace an array of steamcache or generic containers with a single monolithic instance. However if you currently run a steamcache or generic setup then there a few things to note.

1) Your existing cache files are NOT compatible with lancachenet/monolithic, unfortunately your cache will need repriming
2) You do not need multiple containers, a single monolithic container will cache ALL cdns without collision
3) lancachenet/monolithic should be compatible with your existing container's env vars so you can use the same run command you currently use, just change to lancachenet/monolithic

## Origin and SSL

Some publishers, including Origin, use the same hostnames we're replacing for HTTPS content as well as HTTP content. We can't cache HTTPS traffic, so if you're intercepting DNS, you will need to run an SNI Proxy container on port 443 to forward on any HTTPS traffic.

```
docker run \
  --restart unless-stopped \
  --name sniproxy \
  -p 443:443 \
  lancachenet/sniproxy:latest
```

Please read the [lancachenet/sniproxy](https://github.com/lancachenet/sniproxy) project for more information.






## Advice to Publishers

If you're a games publisher and you'd like LAN parties, gaming centers and other places to be able to easily cache your game updates, we reccomend the following:

 - If your content downloads are on HTTPS, you can do what Riot have done - try and resolve a specific hostname. If it resolves to a RFC1918 private address, switch your downloads to use HTTP instead.
 - Try to use hostnames specific for your HTTP download traffic.
 - Tell us the hostnames that you're using for your game traffic. We're maintaining a list at [uklans/cache-domains](https://github.com/uklans/cache-domains) and we'll accept pull requests!
 - Have your client verify the files and ensure the file they've downloaded matches the file they **should** have downloaded. This cache server acts as a man-in-the-middle so it would be good to ensure the files are correct.

 If you need any further advice, please contact us and we'll be glad to help!
 
 ## How to help out
To build just run `docker build --tag lancachenet/monolithic:testing .`. To test you can run `./run_tests.sh`

If you want to test a new build with a forked uklans repo you can specify a combination of `-e CACHE_DOMAINS_REPO="https://github.com/vibroaxe/cache-domains.git" -e CACHE_DOMAINS_BRANCH="testing"` or even `-v <your repo here>:/data/cachedomains -e NOFETCH=true` if using a locally bind mounted git repo.

