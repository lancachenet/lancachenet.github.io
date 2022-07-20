---
title: Useful Commands
permalink: "/docs/useful-commands/"
---
This page will show you some useful commands when troubleshooting issues with your lancache setup. If this doesn't work out you can always [check out the support page](https://lancache.net/support/).

# Client device commands
These commands can be run from the client device you are trying to access the lancache from. 

|Command		|  Explanation											|
|-------------------------------|----------------------------------------|
|`ipconfig /flushdns`           |   This command will clear all locally stored ip adresses and other DNS records from your device                   |
|`nslookup steam.cache.lancache.net`  | This command will try to find the IP for the steam.cache.lancache.net url, this will verify if the DNS is working correctly         |
| `nslookup lancache.steamcontent.com`|  This command will try to find the IP for the lancache.steamcontent.com url, this will verify if the DNS is working correctly  |
| `ping <ip_address> `| This command can be used to see if your device can reach a certain ip address, for example the ip address of your caching server.   |
| `ipconfig /all` | This command will show your device's network adapter information, including the DNS server(s)   |
| `Test-NetConnection <lancache_ip> -port <port>` | *(Windows only)* This command tests the connection of the specified ports (Lancache runs on ports 80 and 443) to your lancache


<br><br>

# Lancache server commands
These commands can be run from the lancache server. For the docker commands it is recommended to use a privileged user. For more information on using docker with a privileged user see [this documentation](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user).

|Command		|  Explanation											|
|-------------------------------|----------------------------------------|
|`docker logs <container_id>`                        |  This command shows the logs of a specific docker container. `<container_name>` could be used instead of `<container_id>`   |
|`docker container ls -a`  | This command shows all docker containers *(including stopped ones)*|
|`docker status` | This command shows the active status of docker
| `Lsof -i tcp:<port>`| This command shows the services running on a specific port  |
| `ip -a` | This command shows all the ip information for your caching server|

