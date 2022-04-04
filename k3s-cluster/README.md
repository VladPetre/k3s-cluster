# K3S cluster

## Overview 
Homelab main component is actually a k3s cluster running on multiple Raspberry Pis (1 master and 3 worker nodes).

## Default apps

- **traefik ingress controller** - default in k3s
- **kube-prometheus-stack** *(prometheus, grafana)* - for monitoring the cluster and also the management RPIs
- **loki-stack** *(promtail, loki)* - for collecting logs
- **rancher2** - managing the cluster (installed on a VM)

## Install

### K3S and rancher2
I have installed k3s on Raspberry Pis following [this video](https://www.youtube.com/watch?v=X9fSMGkjtug&ab_channel=NetworkChuck) and just taking care of the updates that were in place in the meantime.

#### Rancher 
#curl -sfL https://get.rancher.io | INSTALL_RANCHERD_CHANNEL=latest sh -
on docker desktop using rancher-docker-compose.yaml

Once installed rancher, connect the k3s cluster (on RPIs) following the steps suggested by rancher using insecure option


### kube-prometheus-stack

#### Manual Install
Prerequisites: helm and kubectl should be installed on a machine that can access the cluster

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

<!-- helm install prom-stack prometheus-community/kube-prometheus-stack -n monitoring --values values.yaml --version 34.5.0 -->
helm install prom-stack prometheus-community/kube-prometheus-stack -n monitoring --values values.yaml
helm upgrade prom-stack prometheus-community/kube-prometheus-stack -n monitoring --values values.yaml
```
Here, values.yaml is the file where to put only the custom values

#### Manual Uninstall

```
helm uninstall prom-stack

kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
kubectl delete crd alertmanagers.monitoring.coreos.com
kubectl delete crd podmonitors.monitoring.coreos.com
kubectl delete crd probes.monitoring.coreos.com
kubectl delete crd prometheuses.monitoring.coreos.com
kubectl delete crd prometheusrules.monitoring.coreos.com
kubectl delete crd servicemonitors.monitoring.coreos.com
kubectl delete crd thanosrulers.monitoring.coreos.com
```

#### Install kube-node-exporter on external machine
```
sudo mkdir /opt/node-exporter &&

cd /opt/node-exporter &&

sudo wget -O node-exporter.tar.gz https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-armv7.tar.gz &&

sudo tar -xvf node-exporter.tar.gz --strip-components=1

sudo nano /etc/systemd/system/nodeexporter.service

## copy the content from nodeexporter.service

sudo systemctl enable nodeexporter && sudo systemctl start nodeexporter

```

### loki-stack
I need only loki and promtail to gather the logs

```
helm show values loki grafana/loki-stack > loki-values.yaml

helm upgrade --install loki grafana/loki-stack -n monitoring  --set grafana.enabled=false,prometheus.enabled=false,prometheus.alertmanager.persistentVolume.enabled=false,prometheus.server.persistentVolume.enabled=false,loki.persistence.enabled=true,loki.persistence.storageClassName=local-path,loki.persistence.size=15Gi

```

### registries
Create custom registry on docker using management/charlie-docker-compose.yaml
Copy registries/yaml to master node /etc/rancher/k3s/registries.yaml
Copy/create docker daemon and set the private registry - on master node in /etc/docker/daemon.json with the content

```
{
  "insecure-registries" : ["http://charlie.lan:5000"]
}
```



### copy image for jdk

docker buildx create --use --name adubuilder --config .\buildx-config.toml

docker buildx build -t charlie.lan:5000/openjdk:11-jre -f .\Dockerfile --push --platform=linux/arm64,linux/amd64 .

docker run --name r-caller -p 8070:8080 charlie.lan:5000/ropt/ropt-reactive-caller:latest