# My Homelab
A learning experience. 
## Table of Contents
[1)](https://github.com/cabmatthew/Homelab/blob/main/README.md#devices-used) Devices Used

[2)](https://github.com/cabmatthew/Homelab/blob/main/README.md#pi-recommissioning) Pi Recommissioning

[3)](https://github.com/cabmatthew/Homelab/blob/main/README.md#pihole) Pihole

[4)](https://github.com/cabmatthew/Homelab/blob/main/README.md#samba-setup) Samba Setup

[5)](https://github.com/cabmatthew/Homelab/blob/main/README.md#router-addition) Router Addition

[6)](https://github.com/cabmatthew/Homelab/blob/main/README.md#pivpn-wireguard) PiVPN WireGuard

[7)](https://github.com/cabmatthew/Homelab/blob/main/README.md#dockerizing-pihole) Dockerizing Pihole

[8)](https://github.com/cabmatthew/Homelab/blob/main/README.md#Proxmox) Proxmox

## Devices Used

[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)

- Raspberry Pi OS Trixie (Debian-based) on a Raspberry Pi 4
- Windows 11 Home on a Lenovo Laptop AMD Ryzen 7 5800H 16 GB RAM 2 TB over 2 drives
- MacOS Sequoia on a Macbook Pro M1
- Xfinity Gateway bridged to TP-Link Archer AX4400 Router

## Pi Recommissioning 

[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)

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
[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)


## Samba Setup
[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)


## Router Addition
[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)


## PiVPN WireGuard
[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)


## Dockerizing Pihole
[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)


## Proxmox
[To top](https://github.com/cabmatthew/Homelab/blob/main/README.md#table-of-contents)

