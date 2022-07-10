# Homelab Management utils installation

## Overview
Homelab Management is done using 2 Raspberry Pis to have redundancy. it consist of some applications to ease the access on the local network and monitor machines' state

## Docker and docker compose
```
sudo apt-get update && sudo apt-get upgrade
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker ${USER}
sudo apt-get install libffi-dev libssl-dev
sudo apt install python3-dev
sudo apt-get install -y python3 python3-pip
sudo pip3 install docker-compose
sudo systemctl enable docker
```

## Docker Stack

The utilities stack runs on a Raspberry Pi

Build the stack from docker-compose.yaml file
#docker-compose down && docker-compose build --pull && docker-compose up -d
docker-compose down --rmi all && docker-compose up -d --remove-orphans

Services:
 - portainer
 - pihole
 - heimdall
 - uptime-kuma
 - registry and registry ui



