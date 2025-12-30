# My Homelab

A learning experience 😁

## Table of Contents
1) Devices Used

2) Pi Recommissioning

3) Pi-hole

4) Samba Setup

5) Router Addition

6) PiVPN WireGuard

7) Dockerizing Pihole

8) Proxmox

## Devices Used

- Raspberry Pi OS Trixie (Debian-based) on a Raspberry Pi 4
- Windows 11 Home on a Lenovo Laptop AMD Ryzen 7 5800H 16 GB RAM 2 TB over 2 drives
- MacOS Sequoia on a Macbook Pro M1
- Xfinity Gateway bridged to TP-Link Archer AX4400 Router

## Pi Recommissioning 

```sudo apt update```

This command updated the package index files on my Raspberry Pi 4 which hadn’t been used for 3 years, but there were errors. My first attempt at resolution involved editing /etc/apt/sources.list. It originally tried updating using Buster, which is an old version of the Raspberry Pi OS. After switching it to Bullseye, “sudo apt update” ran successfully.
[Guide](https://dev.blues.io/blog/guide-upgrade-raspberry-pi-buster-bullseye/)

```sudo apt full-upgrade -y```

Moving on, this command installed the available updates found when looking through the package index files. The parameter “full-upgrade” allowed the Pi to remove any packages when necessary to resolve dependency issues while installing the updates. “-y” told the Pi to automatically agree to any prompts. The error from this command mentioned “Breaks: libgcc-8-dev” and running “sudo apt install ggc-8-base” resolved this issue.
[Guide](https://unix.stackexchange.com/questions/592657/full-upgrade-to-debian-testing-fails-due-to-libc6-dev-breaks-libgcc-8-dev)

```Error: No wireless lan interfaces found```

Once all the packages were updated, I then ran into another issue. This time it involved Wi-Fi connectivity, as the wireless LAN interface could not be found. 
[Fix?](https://gist.github.com/basperheim/d419491bc7a6889d99fb5cb72d58d0b6)
After following the article for a fix, I kept seeing the same issue after restarting with the fix in place. I ended up flashing the OS again, and it ran like new.

## Pihole 

Pi-hole is an open-source software serving as a DNS sinkhole. Setting your computer’s DNS to the Pi-hole server causes the computer’s DNS requests to first route to Pi-hole. Then, Pi-hole uses its domain blacklist to see which requests are associated with common advertisement domains. Pi-hole tells your computer that nothing is at that domain by responding with 0.0.0.0, effectively stopping the ads from loading.

```curl -sSL https://install.pi-hole.net | bash```

CLI Installation Wizard

<img src="/images/Pi-hole1.png" height=250px />

I had to reserve the Pi’s IP through the network’s admin tool to avoid temporary leases and IP changes. I could have set my router to advertise Pihole as the network’s DNS server, but I did not want to invade the privacy of my roommates’ browsing history. After setting it up, I just used it on one device for testing as it will have more utilization later with PiVPN.

Below is Pi-hole’s comprehensive dashboard. It provides query logging, real-time statistics, and management.

<img src="/images/Pi-hole2.png" height=250px />

## Samba Setup

Samba is an open-source suite of programs that provide secure and stable file sharing services across operating systems using Server Message Block (SMB). 

```smb.conf```

This file defines the runtime configuration for Samba services. I added extra configurations in addition to the [regular setup](https://wiki.samba.org/index.php/Setting_up_Samba_as_a_Standalone_Server) to improve compatibility and security. 
- ```client min protocol = SMB2``` to enforce use of SMBv2 over SMBv1 for improved performance and security.
- ```vfs objects = streams_xattr``` provides integrity and authenticity with Message Authentication Codes, using a secret key to show it’s from a trusted source and a hash to show the data did not change.
- ```server signing = mandatory``` provides integrity and authenticity with Message Authentication Codes, using a secret key to show it’s from a trusted source and a hash to show the data did not change.
- ```smb encrypt = mandatory``` provides confidentiality through end-to-end Advanced Encryption Standard (AES) encryption.

```sudo useradd -M -s /sbin/nologin matt-user```

Adds a new user account strictly for use with Samba and not logging into the Pi. Use ‘-M’ so that this account does not get a home directory. ‘-s /sbin/nologin’ means this user does not have the ability to log in. I set the password as well. I ended up making the mistake of trying to SSH to my Pi using ‘matt-user’ instead of ‘matt-pi’ before learning about no-login accounts.

```sudo groupadd first-samba-group``` and ```sudo usermod -aG first-samba-group matt-user```

Creates a group and adds ‘matt-user’ to it for local group management.

```mkdir -p /srv/samba/matt-samba```

Creates the directory to be shared across the network.

```chown```, ```chgrp```, ```chmod```

Associates the appropriate groups to the new share and provides them with the correct permissions.

```smbclient //localhost/matt-samba -U matt-user```

Opens the smb client terminal to access the share from the Pi. Accessing the share from my Mac and Windows computer was simple. Mac used Finder to connect to ‘smb://XXX.XXX.X.XXX/matt-samba’, while Windows mapped a network drive to ‘\\XXX.XXX.X.XXX\matt-samba’ with each prompting for my username and password for Samba.

File share contents in SMB Client on Mac Terminal SSHed to Pi running Samba:

<img src="/images/SambaSetup1.png" height=225px />

File share contents in Windows Explorer:

<img src="/images/SambaSetup2.png" height=225px />

File share contents in macOS Finder:

<img src="/images/SambaSetup3.png" height=225px />

File share contents in iOS Files:

<img src="/images/SambaSetup4.jpg" height=225px />

## Router Addition

My apartment’s new router enabled easier management and gave insights into live bandwidth usage, which the Xfinity admin tool did not provide. Adding the router required enabling bridge mode on my Xfinity Gateway. This changed it from a modem + router combo to just a modem, providing internet access to the new external router. Adding IP reservation for Pi-hole through the TP-Link admin page was straightforward. 

## PiVPN WireGuard

**No-IP**

- No-IP was necessary as it provides Dynamic DNS services, which would map a static human-readable domain name to the IP address of my home router which my ISP occasionally changes. After setting up my free DDNS domain, I set up PiVPN to use WireGuard, my domain, and my Pi-hole, then ran it on my Pi. I had to download a Dynamic Update Client and run it on my Pi as well, which informs No-IP of any router IP changes. 

<img src="/images/PiVPN1.png" height=225px />

With my new VPN set up, I was able to add clients to the VPN server running on my Pi. Adding clients required using a client-device-specific .conf file generated by PiVPN, which I was able to easily transfer to new client devices using my Samba share. After activating the VPN, my devices would be able to remotely connect to my home network when outside of my apartment, e.g. securely accessing my home NAS while on cellular data, or SSHing to my Pi while on school Wi-Fi. In addition to remote access to my home network, the VPN uses the Pi-hole server as its primary DNS, meaning there is ad-blocking built into the VPN. 

<img src="/images/PiVPN2.png" height=225px />

## Dockerizing Pihole

First, I set up the Docker repository and installed Docker Compose on my Pi using [their instructions](https://docs.docker.com/compose/install/linux/#install-using-the-repository). Then, I used Pi-hole’s [Docker example](https://hub.docker.com/r/pihole/pihole) and customized the compose.yaml file to my preferences. I first uninstalled the native Pi-hole from the Pi, then ran the Pi-hole container, setting it to run on boot using ```systemctl```. Running Pi-hole provides isolation and ease of management. 

<img src="/images/Dockerized1.png" height=225px />

## Proxmox

