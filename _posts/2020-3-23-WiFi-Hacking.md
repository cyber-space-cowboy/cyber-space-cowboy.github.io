---
layout: post
title: WiFi Hacking
---

This project was done for my TN425 class which dealt with wireless signals.
My group was tasked to investigate all generations of WiFi, data transfer rates, and security implementation. Sadly enough, we did not have 802.11 AX (WiFi 6) equipment on hand to use/test which is the newest offering to the WiFi family. In addition to only going up to 802.11 ac the security we were able to grant the wireless was only up to WPA2. The bands did not apply much towards security from the methods we were testing so we just tested the security within the AC band.

In the lab we were able to create a Router-On-A-Stick network for testing purposes to recreate a similar setup you may see in the wild.
We used several pieces of Cisco equipment to accomplish this: Cisco Router, Cisco Switch, and a Cisco 3504 Wireless Controller. The AP and Controller were not up to 802.11 AX yet, but could still support the 802.11 a/b/c/g/n/ac bands.

To measure the data transfer rates of the different bands we had two laptops running ftp services transferring 1MB, 10MB, and 50MB files. While these transfers of data were taking place, a third laptop outside of the network running ParrotOS with a ALFA AWUS036ACH USB Wireless Adapter. This card in particular is able to do monitor mode and packet injection, which is incredibly important during WiFi hacking. The majority of tools used came from the Airegeddon sweet which comes pre-installed with ParrotOS and Kali (Two offensive security distros). There are several pre-installed WiFi tools to use besides the Airegeddon sweet, but we used those specifically to show what exactly is taking place. To show the more automated "Script-Kiddie" tools we used WiFite which is completely automated through simple command-line prompts.

The first test was with a network running WEP:
