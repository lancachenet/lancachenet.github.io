---
title: Useful Commands
permalink: "/docs/useful-commands/"
---
This page will show you some useful commands when troubleshooting issues with your lancache setup. If this doesn't work out you can always [check out the support page](https://lancache.net/support/).

# Client device commands
These commands can be run from the client device you are trying to access the lancache from. 


|Command		|  Explanation											|
|-------------------------------|----------------------------------------|
|`ipconfig /flushdns`           | *(Windows only)*  This command will clear all locally stored ip adresses and other DNS records from your device                   |
|`ipconfig /renew`           | *(Windows only)*  This command will renew your DHCP assigned IP and DNS settings from the DHCP server            |
|`nslookup steam.cache.lancache.net`  | This command will try to find the IP for the steam.cache.lancache.net url and should return the IP of your LanCache server. This will verify if the DNS is working correctly         |
| `nslookup lancache.steamcontent.com`|  This command will try to find the IP for the lancache.steamcontent.com url and should return the IP of your LanCache server. This will verify if the DNS is working correctly  |
| `ping <ip_address> `| This command can be used to see if your device can reach a certain ip address, for example the ip address of your caching server   |
| `ipconfig /all` | *(Windows only)* This command will show your device's network adapter information, including the DNS server(s)   |
| `ip a` | *(Linux equivalent of the command above)*    |
| `Test-NetConnection <lancache_ip> -port <port>` | *(Windows only)* This command tests the connection of the specified ports (Lancache runs on ports 80 and 443) to your lancache |


<br><br>

# Lancache server commands
These commands can be run from the lancache server. For the docker commands it is recommended to use a privileged user. 
For more information on using docker with a privileged user see [this documentation](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user).
Adding `sudo` in front of the command also works.

|Command		|  Explanation											|
|-------------------------------|----------------------------------------|
|`docker logs <container_id>`                        |  This command shows the logs of a specific docker container. `<container_name>` could be used instead of `<container_id>`. The container names *(normally)* are `lancache_monolithic_1` and `lancache_dns_1`   |
|`docker container ls -a`  | This command shows all docker containers *(including stopped ones)*|
|`docker status` | This command shows the active status of docker |
|`docker-compose up -d` | This command will create the docker container(s) for the LanCache and start running it |
|`docker-compose down` | This command will stop all the running docker containers |
|`docker-compose down && docker-compose up -d` | This command stops all running docker containers and restarts them afterwards |
| `lsof -i tcp:<port>`| This command shows the services running on a specific port  |
| `ip -a` | This command shows all the ip information for your caching server|
| `find /your/cache/folder -type f -exec awk 'FNR>2 {nextfile} ``/origin\/eamaster\/s\/shift\``/mass_effect\/mass_effect_andromeda\/patchww_ww\``/mass_effect_andromedapcpatchww_wwconcept_670739583_``pc_retail_patch_fe0ac79761d8c4feba5831a96cfbc7fc6.zip/ ``{ print FILENAME ; nextfile }' '{}' +` | This command allows you to remove a corrupt file from the LanCache. **This process takes very long** |

