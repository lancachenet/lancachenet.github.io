---
title: Setting up your router
description: Official guide to install lancache.net
permalink: /docs/installation/routers/
---

The ideal solution when deploying lancache.net is to distribute the IP of your lancache-dns server via dhcp. Many commercial routers will have an option under __LAN settings__ (or similar) to change the _DNS Server IP_. Unfortunately not all consumer brand routers are so versatile so if you cannot find a LAN DNS setting you can use the WAN settings instead.

<div class="note warning">
  <h5>Take Care</h5>
  <p>If you are overriding the WAN DNS settings on your router it is extremly important to set <code>UPSTREAM_DNS</code> on all containers to a valid external IP (such as Google or Cloudflare)
  </p>
</div>

Changing the DNS server settings on a router isn't tricky, but every manufacturer uses a custom interface, meaning the process can differ widely depending on the router you own. Here are the steps needed to change the WAN DNS servers on the most popular router brands.

## Linksys
![Photo of a Linksys EA8500 Router](https://www.lifewire.com/thmb/ufbA5SBHQyJ2-GQYUGaL3q3pxIc=/1000x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/linksys-ea8500-router-56a6fac35f9b58b7d0e5d140.jpg)

Change the DNS servers on your Linksys router from the _Setup_ menu:

1. Sign in to your Linksys router's web-based administration, usually at [http://192.168.1.1](http://192.168.1.1).

2. Tap or click __Setup__ in the top menu.

3. Tap or click __Basic Setup__ in the _Setup_ submenu.

4. In the _Static DNS 1_ field, enter the ip address of your lancache-dns server.

5. Leave the Static DNS 3 field blank or add a primary DNS server from another provider.

6. Tap or click the __Save Settings__ button at the bottom of the screen.

7. Tap or click the __Continue__ button on the next screen.

Most Linksys routers don't require a restart for these DNS server changes to take effect, but if the router admin page asks you to, do it.

## NETGEAR
![Photo of a NETGEAR R8000 Router](https://www.lifewire.com/thmb/tqpvvpPi2vvSUTAnbKIEk5TnK6Y=/1350x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/netgear-r8000-router-56a6fac63df78cf772913f71.jpg)

Change the DNS servers on your NETGEAR router from the _Basic Settings_ or _Internet_ menu, depending on your model:

1. Sign in to your NETGEAR router manager page, most often by entering [http://192.168.1.1](http://192.168.1.1) or [http://192.168.0.1](http://192.168.0.1) in your web browser.

2. NETGEAR has two primary interfaces with different ways of performing the next step: If you see a _BASIC_ and _ADVANCED_ tab along the top, choose __Basic__ followed by the __Internet__ option on the left. If you don't have those two tabs along the top, choose __Basic Settings__.

3. Choose the __Use These DNS Servers__ option in the Domain Name Server (DNS) Address section.

4. In the Primary DNS field, enter the ip addresss of your lancache-dns server.

5. If your NETGEAR router has a _Second_ or _Third_ DNS field, leave them blank.

6. Tap or click __Apply__ to save the DNS server changes you just entered.

7. Follow any additional prompts about restarting your router. If you don't get any, your changes should now be live.

## D-Link
![Photo of a D-Link DIR-890L/R Router](https://www.lifewire.com/thmb/ZRGH20wyeaRzkb7HwsFB27_SgeQ=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/d-link-dir-890lr-router-56a6fac35f9b58b7d0e5d143.jpg)

Change the DNS servers on your D-Link router in the Setup menu:

1. Sign in to your D-Link router with a web browser using [http://192.168.0.1](http://192.168.0.1).

2. Choose the __Internet__ option on the left side of the page.

3. Select the __Setup__ menu at the top of the page.

4. Find the _Dynamic IP (DHCP) Internet Connection Type_ section and enter the ip address of your lancache-dns server into the Primary DNS Address field.

5. Choose the __Save Settings__ button at the top of the page.

6. The DNS server settings should have changed instantly, but you might be told to reboot the router to complete the changes.

## ASUS
![Photo of an ASUS RT-AC3200 Router](https://www.lifewire.com/thmb/Mh06WzOrpB1O7VsNbah9og64aGc=/1398x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/asus-rt-ac3200-router-56a6fb053df78cf7729140aa.jpg)

Change the DNS servers on your ASUS router through the LAN menu:

1. Sign in to your ASUS router's admin page with this address: [http://192.168.1.1](http://192.168.1.1).

2. From the menu on the left, click or tap __WAN__.

3. Choose the __Internet Connection__ tab at the top of the page.

4. In the WAN DNS Setting section, enter the ip address of your lancache-dns server in the _DNS Server1_ text box.

5. Save the changes with the __Apply__ button at the bottom of the page.

You may need to restart the router after applying the changes.

## TP-LINK
![Photo of a TP-LINK AC1200 Archer C5 Router](https://www.lifewire.com/thmb/7-EBd6Y2iDeYAnk8ZF8Cvo7Hn6I=/795x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/tp-link-ac1200-router-56a6fb055f9b58b7d0e5d283.png)

Change the DNS servers on your TP-LINK router via the DHCP menu:

1. Sign in to your TP-LINK router's configuration page, usually via the [http://192.168.1.1](http://192.168.1.1) address but sometimes via [http://192.168.0.1](http://192.168.0.1).

2. Choose the __DHCP__ option from the menu on the left.

3. Tap or click the _DHCP submenu_ option called __DHCP Settings__.

4. In the _Primary DNS_ field, enter the ip address of your lancache-dns server..

5. Choose the __Save__ button at the bottom of the page to save the changes.

You probably don't have to restart your router to apply these DNS settings, but some TP-LINK routers may require it.

## Cisco
![Picture of a black Cisco RV110W router](https://www.lifewire.com/thmb/x2W3MGVuC3BTjiwNyV6bpU-4mpU=/1002x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/cisco-rv110w-router-5a09af7113f12900375c2f71.PNG)

Change the DNS servers on your Cisco router from the LAN Setup menu:

1. Sign in to your Cisco router using either [http://192.168.1.1](http://192.168.1.1) or [http://192.168.1.254](http://192.168.1.254), depending on your router model.

2. Click or tap the __Setup__ option on the menu at the top of the page.

3. Choose the __Lan Setup__ tab from the menu that's just below the _Setup_ option.

4. In the _LAN 1 Static DNS 1_ field, enter the ip address of your lancache-dns server.

5. Save the changes using the __Save Settings__ button at the bottom of the page.

Some Cisco routers have you restart the router to apply the changes. If not, all the changes are applied right after you choose Save Settings.

These steps are different for your router if you have a co-branded Cisco-Linksys router. If your router has the word Linksys on it anywhere, follow the steps at the top of this article for changing the DNS servers on a Linksys router.

## TRENDnet
![Picture of a TRENDnet TEW-818DRU AC1900 Router](https://www.lifewire.com/thmb/DrE7p4Wze1RNoRTsNlIW_1mzcvI=/786x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/trendnet-ac1750-router-5a09b12d89eacc0037461589.PNG)

Change the DNS servers on your TRENDnet router via the Advanced menu:

1. Sign in to your TRENDnet router at [http://192.168.10.1](http://192.168.10.1).

2. Choose __Advanced__ at the top of the page.

3. Select the __Setup__ menu to the left.

4. Click or tap the __Internet settings__ submenu under the _Setup_ menu.

5. Choose the Enable option next to _Manually configure DNS_.

6. Next to the _Primary DNS_ box, enter the ip of your lancache-dns server.

7. Save the settings with the __Apply__ button.

If you're told to reboot the router, follow the instructions on the screen. Not all TRENDnet models require this.

## Belkin
![Picture of a Belkin AC 1200 DB Wi-Fi Dual-Band AC+ Router](https://www.lifewire.com/thmb/ehBvqGG7p55VepFnCA6WobK8L1k=/2250x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/belkin-ac-1200-db-wifi-dual-band-router-5a09b1b54e4f7d00366c799e.PNG)

Change the DNS servers on your Belkin router by opening the DNS menu:

1. Sign in to your Belkin router through the address [http://192.168.2.1](192.168.2.1).

2. Select _DNS_ under the __Internet WAN__ section from the menu on the left.

3. In the _DNS Address_ field, enter the ip of your lancache-dns server.

4. Click or tap the __Apply Changes__ button to save the changes.

You may be told to restart your router for the changes to take effect. If so, follow the on-screen prompts.

## Buffalo
![Picture of a black Buffalo AirStation Extreme AC1750 router](https://www.lifewire.com/thmb/drDQOsq3uGO0RoEX88buLlBl5EA=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/buffalo-airstation-extreme-ac1750-router-5a09b000da271500379aba9b.PNG)

Change the DNS servers on your Buffalo router from the Advanced menu:

1. Sign in to your Buffalo router at [http://192.168.11.1](192.168.11.1).

2. Click or tap the __Advanced__ tab at the top of the page.

3. Choose __WAN Config__ on the left side of the page.

4. Next to the _Primary_ field in the _Advanced Settings_ section, enter the ip of your lancache-dns server.

5. Near the bottom of the page, choose __Apply__ to save the changes.

