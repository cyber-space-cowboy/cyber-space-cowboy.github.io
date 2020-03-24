---
layout: post
title: WiFi Hacking
---
**This is only for learning purposes and to demo what I have done in class. By no means should this be done without clear consent from any party.**

This project was done for my TN425 class which dealt with wireless signals.
My group was tasked to investigate all generations of WiFi, data transfer rates, and security implementation. Sadly enough, we did not have 802.11 AX (WiFi 6) equipment on hand to use/test which is the newest offering to the WiFi family. In addition to only going up to 802.11 ac the security we were able to grant the wireless was only up to WPA2. The bands did not apply much towards security from the methods we were testing so we just tested the security within the AC band.

In the lab we were able to create a Router-On-A-Stick network for testing purposes to recreate a similar setup you may see in the wild.
We used several pieces of Cisco equipment to accomplish this: Cisco Router, Cisco Switch, and a Cisco 3504 Wireless Controller. The AP and Controller were not up to 802.11 AX yet, but could still support the 802.11 a/b/c/g/n/ac bands. The 802.11 n channel was unable to be tested in its own and was used in unison with other bands [see the slides and guide we made](https://drive.google.com/open?id=1sozDTa1xm9SjYfEsAZY7P4lSRtlAXzca).

What are the different bands?
A - Operates at frequencies between 5.725 GHz and 5.850 GHz, using Orthogonal Frequency-Division Multiplexing (OFDM)  modulation scheme, and supports speeds up to 54 Mbps
B- Operates around 2.4 Ghz and uses Ethernet protocol and CSMA/CA for path sharing. Its modulation method is Phase-Shift keying (PSK), and supports speeds up to 11Mbps
B/G - The specifications of 802.11b are included with 802.11g which operates at 2.4 to 2.4835 GHz, uses the OFDM modulation scheme to over speeds of 54Mbps that can fall back to 11 Mbps
AC - Introduces 256-QAM modulation, 5.0 GHz and 2.4Ghz operating frequencies, and because it can run 8 160MHz 256-QAM channels, can reach speeds almost up to 7 Gbps
AX - Uses 1024-QAM to pack signals with more data (efficiency) with a wider, 160MHz Channel (speed) - up to 10 Gbps. Also, has an 8 stream uplink/downlink, MU-MIMO and OFDMA for a capacity up to four times of WiFi 5


To measure the data transfer rates of the different bands we had two laptops running ftp services transferring 1MB, 10MB, and 50MB files. While these transfers of data were taking place, a third laptop outside of the network running ParrotOS with a ALFA AWUS036ACH USB Wireless Adapter. This card in particular is able to do monitor mode and packet injection, which is incredibly important during WiFi hacking. The majority of tools used came from the Airegeddon suite which comes pre-installed with ParrotOS and Kali (two offensive security distros). There are several pre-installed WiFi tools to use besides the Airegeddon suite, but we used those specifically to show what exactly is taking place. To show the more automated "Script-Kiddie" tools we used WiFite which is completely automated through simple command-line prompts.

What are the differences behind the encryption methods?
WEP - Wired Equivalent Privacy introduced in 1997 and uses a key of 10 to 26 hexadecimal digits (40 or 104 bits). Available on 802.11a and 802.11b  
WPA - Wi-Fi Protected Access available in 2003 introduced TKIP and CRC
WPA2 - Wi-Fi Protected Access 2 available in 2006 introduced AES encryption
WPA3 - Wi-Fi Protected Access 3 announced January 2018 AES 256 with SHA 384 as HMAC
802.1x - Binds MAC to a user radius server would be needed as an authentication authority

The first test was with a network running WEP:

![_config.yml]({{ site.baseurl }}/images/WiFi/WEP_Key.png)

This picture shows the security configuration we made within the controller. We went with the smallest key size which allowed us to place 5 characters (since 8 bits x 5 characters = 40 bits) letting the PSK be easily crackable.

![_config.yml]({{ site.baseurl }}/images/WiFi/WEP_Sniff.png)

We first booted up a terminal within the laptop running Parrot and launched the tool known as airodump-ng. Once the WLAN adapter is placed in monitor mode we are able to 802.11 traffic. Our test network was aptly names "Test 425 Network" as you can see in the encryption (ENC) column we are running WEP encryption . It is important to note when you are doing this kind of attack that the data needs to be clearly transferred. Otherwise it is going to be hard for you to obtain the key or deauth any device on the network in order for you to obtain the handshake.

![_config.yml]({{ site.baseurl }}/images/WiFi/WEP_Sniff_2.png)

Once you identify the network you are needing to attack you can further refine the airodump command by selecting the BSSID, Channel, then having it write to a file.

In this case we used the WiFite tool instead to do some automated hacking.

![_config.yml]({{ site.baseurl }}/images/WiFi/WEP_Wifite.png)

So since we know what encryption the target network is running we can identify the encryption method to search for. In this case we selected the --wep option to find all WEP networks. As you can see our "Test 425 Network" was found.

![_config.yml]({{ site.baseurl }}/images/WiFi/WEP_Wifite_2.png)

The attack that was launched against the network was a replay/fakeauth attack. As you can see the key was cracked then placed in the cracked.txt file. WEP is obsolete for a reason because the encryption algo is very weak. As I will highlight towards the end, key size and encryption is incredibly important to stay secure.

Now covering WPA and WPA2:

![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_and_WPA_2_Key.png)

This is what the controller had to offer within security. When testing we decided to go with the most common encryption standards to attack. WPA/WPA2 policies were enabled to use AES with a preshared key (PSK). The key in this case was "hellothisissecure", which was to show why key length matters. When a device tries to authenticate to a network it is prompted to present the PSK in order to get properly authenticated and obtain an IP. While authenticating the controller (in this case) and device exchange four EAPOL packets. The picture below displays the key exchange between a device and AP:

![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_Key_exchange.jpg)



Just like in the WEP demo we are using airodump on the WPA and WPA2 networks

WPA:
![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_sniff.png)

WPA2:
![_config.yml]({{ site.baseurl }}/images/WiFi/WPA2_Sniff.png)

As you can see we have some data transferring, but we need a device to authenticate to this network in order to obtain those EAPOL packets. So while running the airodump tool with the refined query to only sniff the "Test 425 Network" we will deauth a device from the network. The good thing about this is that you can see what devices are putting out data and chose your victim.

Here is me Deauthing a device on the network with aireplay-ng:
![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_Deauth.png)

Here is me runnning airodump and obtaining the WPA handshake:
![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_2_sniff.png)

This is what the EAPOL Packets look like within Wireshark:
![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_Wireshark.png)

Once the EAPOL packets are found you can now save the selected packets as a brand new PCAP file and then have Aircrack-ng run against it with any passwordlist that you have assembled.

![_config.yml]({{ site.baseurl }}/images/WiFi/WPA_Cracking.png)

I found wordlist that was on GitHub that held a million different passwords and used it when cracking the obtained EAPOL packets. It ran through the password list and was not cracked. This comes down to possibilities and entropy when coming up with a wordlist that holds all possible combinations, in this case only alphabetic symbols.
Here is a very good XKCD comic to describe the reasoning:

![_config.yml]({{ site.baseurl }}/images/WiFi/XKCD.jpg)
