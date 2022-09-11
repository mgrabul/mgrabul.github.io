---
title: Setting up WireGardVPN at home
date: 2022-06-15
categories: [VPN, Wireguard]
tags: [TAG]     # TAG names should always be lowercase
---

## How to set-up WireGuardVPN on debian, ubuntu?

In this tutorial the following software will be used, required:
* curl
* docker, docker compose
* pivpn
* duck-dns docker image

Running vpn at home requires three general components:
* VPN server, this is a PC that is running VPN. In our case wireguard 
* Dynamic DNS service, is a service that can connect forward internet traffic from a domain to an IP address. This is required since the vpn clients will use this domain as the url to connect to the VPN. The Dynamic DNS then will forward traffic from this url to the IP address that in this case is our PC running wireguard. Important point to note here is that the IP of a home PC is changing and ones the ip changes the Dynamic DNS will need to be informed of the new IP address. 
* Port forwarding, the home router needs to forward the VPN traffic to the wireguard server PC. This process is called port forwarding.

## Setup wireguard on debian / ubuntu

### Install curl
As first step you need to install curl, that is in case it is not already installed.   
```sh
sudo apt install curl
```
Reference: https://www.cyberciti.biz/faq/how-to-install-curl-command-on-a-ubuntu-linux/

### Install piVPN
Second step, piVPN needs to be installed. PiVPN is an easy way to set up wireguard. After running the following command `curl -L https://install.pivpn.io | bash`, step by step installation process is started that will guide you thew the install process. PiVPN also offers OpenVpn so make sure you pick the correct vpn during this process. Also pls select custom-dns from the dns list as this tutorial shows how to use Duck-dns.

Reference: https://www.pivpn.io/

## Set up Dynamic DNS
To for dynamic dns DuckDNS will be used. This requires setting up account on DuckDNS and getting a domain. After you have a domain you need to periodically update the IP address associated with this domain.

### Get a domain on DuckDNS
Domains on duck dns are free but are limited per account. 
<!-- TODO register an account on duck dns -->
<!-- TODO get domain on duck dns -->

### Refresh Dynamic DNS IP address

This requires periodically calling the DUCK DNS API from the PC that runs wireguard. To do this a script can be created, but there is also already existing docker image specially designed for this task that can be used. In this tutorial I'll use docker-compose to run the image as I find it personally more convenient. 

#### Install docker

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
Reference: https://docs.docker.com/engine/install/ubuntu/

#### Install docker-compose

```sh 
sudo apt-get install docker-compose-plugin
```

#### Create docker-compose file

On the pc running wireguard, create a docker compose file 
docker-compose.yml with the following content:
```sh
version: "2.1"
services:
  duckdns:
    image: ghcr.io/linuxserver/duckdns
    container_name: duckdns
    environment:
      - PUID=1000 #optional
      - PGID=1000 #optional
      - TZ=Europe/XXXXX
      - SUBDOMAINS=oXXX-XXX
      - TOKEN=5187bc8b-XXXX-XXXX-XXXX-XXXXXXXXXXXX
      - LOG_FILE=false #optional
    restart: unless-stopped
```

#### Run docker-compose

Run the following command on the same level of the docker-compose.yml file:
```sh
docker-compose up
```
or to run in background:
```sh
docker-compose up -d
```
Reference: https://docs.docker.com/compose/gettingstarted/

## Port forwarding

Port forwarding is the last step. The concept here is that wireguard used by default 51820 port. Most probably your router is blocking this port so you have to redirect any traffic cumming to port 51820 to the IP address of PC running wireguard. Port forwarding is a unique process for every router manufacture and also perhaps every router, thats why you will need to research for your specific model. Important thing here is to assign a static IP address to the pc running wireguard. This is important since In case the IP changes the port forwarding might not work any more.

## Test

Get keys from for the wireguard clients, set them up and test the process. 
<!-- tutorial on getting keys -->

https://jamstackthemes.dev/demo/theme/jekyll-theme-chirpy/