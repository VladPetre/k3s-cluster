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

Services:
 - portainer
 - pihole
 - heimdall
 - uptime-kuma

### Second Docker Host
To connect the second docker host to the portainer the following commands needs to be executed on the second host

```
 cd /etc/systemd/system
 mkdir docker.service.d
 cd docker.service.d/
 nano remote-api.conf

	[Service]
	ExecStart=
	ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock 
 
 sudo systemctl daemon-reload
 sudo systemctl restart docker
```

After the configuration is done, the second host will be available to be added to portainer