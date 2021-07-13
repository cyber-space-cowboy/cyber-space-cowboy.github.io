---
layout: post
title: Honeypots and Hand Grenades
---
### What is a Honeypot?

Summarizing multiple definitions a "honeypot" is a system designed to be attacked. However, you can refine that definition into three main things that make up a "honeypot". In this blog, I will highlight the main three types of "honeypots". But first, we need to address one of the opposing arguments for the use of "honeypots".

# Legality

**Disclaimer: I am not a lawyer and none of this should be used in a court because someone did something stupid.**

Honeypots, honey systems, honey services, and honey tokens are NOT illegal to use in any circumstance. However, it comes down to intent and execution. Like any cybersecurity-related topic it resides in a grey area and can be used in many good circumstances, but also be used in just as many bad circumstances. Logging an attacker’s attempts, using a system for threat intel/research, and for detection, purposes are not illegal. Setting it up to DDoS or break an attacker’s system (anything "Hacking Back") is where it becomes illegal.

# Honey Systems vs Honey Tokens vs Honey Services

### Honey Systems

Honey systems- Emulate or are actual operating systems. Within the Honey systems, you can categorize these further into High Interaction and Low Interaction honeypots. High Interaction honeypots are where the danger resides due to the honeypot being the full OS. You are risking the instance being compromised and the attacker pivoting from the honeypot further into the network. However, you are able to obtain more information on the attackers’ tactics, techniques, and procedures (TTPs). Low Interaction honeypots are used for detection and these usually apply to Honey services and Honey tokens to where they emulate a specific service or system. Since this isn't designed to be a whole OS there is less chance of compromise.

Example: a Windows server that is exposed but is not directly running any services that are used for a purpose (High Interaction). [Tpotce](https://github.com/telekom-security/tpotce): Prebuilt, insanely cool with a lot of options, and ELK stack with numerous tools (Low - Mid Interaction).

tpotce:

![_config.yml]({{ site.baseurl }}/images/honeypots/tpot.jpg)

This is a honey system with so many honey service options from [cowrie](https://github.com/cowrie/cowrie), [conpot](http://conpot.org/), [dionaea](https://github.com/DinoTools/dionaea), [mailhoney](https://github.com/awhitehatter/mailoney), [rdpy](https://github.com/citronneur/rdpy), and so many more operating all within Docker containers. Within the installation you can choose the types of services you would like to deploy and they even have industry-specific configs like Industrial and Medical.

I happened to use the Standard configuration when I set mine up within an Azure. Within a week I had over a million attacks on multiple services, the biggest attack was within the [dionaea](https://github.com/DinoTools/dionaea) honey service which emulates multiple protocols.

![_config.yml]({{ site.baseurl }}/images/honeypots/tpot_Result.jpg)

The ELK stack is incredibly nice to utilize as a SIEM, examining the logs within the stack is very easy and intuitive. Tpotce also runs an instance of [Cyberchef](https://gchq.github.io/CyberChef/) which is one of my favorite Cybersecurity tools, this makes decoding and data analysis so easy. After decoding and analyzing data you can then take it to the best OSINT tool out there, [Spiderfoot](https://github.com/smicallef/spiderfoot) for automated OSINT investigation.

### Honey Services

Honey Services- Emulates a service or protocol and multiple honey services can be run on a honey system or a legitimate system. Running a service like these with the proper account restrictions and basic security steps can provide an amazing detection tool for the user.

Example: Cowrie

Cowrie is an SSH emulation honey service that allows SSH attacks by malicious actors. I was able to get it installed fairly easily within an Ubuntu box within my homelab. There are several ways to run it by utilizing a [docker container](https://hub.docker.com/r/cowrie/cowrie/dockerfile) on [a host or installing it on a host natively](https://github.com/cowrie/cowrie). I HIGHLY recommend following the instructions on how to set it up on this [page](https://cowrie.readthedocs.io/en/latest/INSTALL.html#).

In its base configuration it operates on port 2222 and the credentials to access (connecting to it as an "attacker") are:

```
Username: root
Password: toor
```

![_config.yml]({{ site.baseurl }}/images/honeypots/nmap_results.png)

![_config.yml]({{ site.baseurl }}/images/honeypots/cowrie_logon.png)

All other attempts that are not those provided above do not establish an SSH session. Despite successful or unsuccessful logins are logged in:

```
/home/cowrie/cowrie/var/log/cowrie
/var/log/cowrie - if you do not follow the recommendations within the docs
```

![_config.yml]({{ site.baseurl }}/images/honeypots/cowrie_logs.jpg)

Within the logs you are able to see the IP of the attacker, username/password attempted, successful or not, and the commands ran. Cowrie also does emulate other systems to make it appear that the whole network is accessible to the attacker as well as an internet connection. I pinged Googles IP and as well tried to install Nmap on the system, but you were not able to execute the command.

![_config.yml]({{ site.baseurl }}/images/honeypots/cowrie_internet.png)

![_config.yml]({{ site.baseurl }}/images/honeypots/install_nmap.png)

When I attempted to try to then pivot to another Linux instance in my home lab (goat) from cowrie it kinda broke. The username reverted to root and the host was localhost. It also displayed incorrect hardware when trying to find out what I was in.

![_config.yml]({{ site.baseurl }}/images/honeypots/cowrie_emulation.png)

The results of the filesystem and hardware are the exact same for the emulated systems cowrie produces

![_config.yml]({{ site.baseurl }}/images/honeypots/sys_info.png)

You can also explore the file system and also take a look at fake users

![_config.yml]({{ site.baseurl }}/images/honeypots/cowrie_explore1.png)

![_config.yml]({{ site.baseurl }}/images/honeypots/cowrie_passwdcontents.jpg)

### Honey Tokens

Honey Tokens- emulates legitimate data. This can be an email, word doc, excel doc, pdf, custom exe or bin, and all the way to if the website you imbued a token with to detect if it is cloned in a phishing attempt. Honey tokens are by far the safest option when it comes to these three. Though it is probably the last way to detect an attacker within your network. However, if you are wanting to track insider threats and basic DLP this is a fantastic tool.

You can make your own Honey token yourself, but I recommend utilizing [CanaryTokens](https://www.canarytokens.org/generate) by Thinkist. CanaryTokens is incredibly easy to use and everything is managed by Thinkist.

![_config.yml]({{ site.baseurl }}/images/honeypots/canary_options.png)

In this case, I chose to use a URL token and within this option, you can provide an email or webhook URL to receive the alert with the reminder you wrote

![_config.yml]({{ site.baseurl }}/images/honeypots/web_token.png)

After you provide the information needed to create a token, you will receive the URL that will allow you to manage and observe the token.

![_config.yml]({{ site.baseurl }}/images/honeypots/token.png)

I clicked on the canary link and received an email shortly after. The information that is displayed within the message is minimal but you can see more within the actual management panel

![_config.yml]({{ site.baseurl }}/images/honeypots/email.png)

Once in the management portal, you can view the event in much more detail. The incident map portion didn't render properly, but when it was first loading it did come through and was accurate. It displays Country, City, Region, Organization, and if the IP was a Tor node.

![_config.yml]({{ site.baseurl }}/images/honeypots/token_info.png)

Further down you can view more info

![_config.yml]({{ site.baseurl }}/images/honeypots/other_info.png)

### In Closing

A good honeypot may include all three of these variants: honey system, honey service, and honey token. However, all three can be independently used on their own or with some combination. A great honeypot will any of those three in combination with good cyber deception techniques to entice attackers. In my next post, I will go through my steps on creating a honeypot and cyber deception tactics.
