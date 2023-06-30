# K3S cluster

## Overview 
Homelab main component is actually a k3s cluster running on multiple Raspberry Pis (1 master and 3 worker nodes).

## Default apps

- **traefik ingress controller** - default in k3s
- **kube-prometheus-stack** *(prometheus, grafana)* - for monitoring the cluster and also the management RPIs
- **loki-stack** *(promtail, loki)* - for collecting logs
- **rancher2** - managing the cluster (installed on Docker Desktop)

## Install

### K3S
To install k3s use their official repo - https://github.com/k3s-io/k3s-ansible
sudo ansible-playbook -i inventory/rpik3s/hosts.ini site.yml --ask-pass --ask-become-pass
My hosts are in inventory/rpik3s/hosts.ini

### Rancher 
#curl -sfL https://get.rancher.io | INSTALL_RANCHERD_CHANNEL=latest sh -
on docker desktop using rancher-docker-compose.yaml

Once installed rancher, connect the k3s cluster (on RPIs) following the steps suggested by rancher using insecure option

!!! if Stuck in pending, check if the register file is with localhost or the IP, or check logs for cattle agent in cattle-system ns


### kube-prometheus-stack

#### Manual Install
Prerequisites: helm and kubectl should be installed on a machine that can access the cluster

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm install prom-stack prometheus-community/kube-prometheus-stack -n monitoring --values values.yaml
helm upgrade prom-stack prometheus-community/kube-prometheus-stack -n monitoring --values values.yaml
```
Here, values.yaml is the file where to put only the custom values

#### Manual Uninstall

```
helm uninstall prom-stack -n monitoring

kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
kubectl delete crd alertmanagers.monitoring.coreos.com
kubectl delete crd podmonitors.monitoring.coreos.com
kubectl delete crd probes.monitoring.coreos.com
kubectl delete crd prometheuses.monitoring.coreos.com
kubectl delete crd prometheusrules.monitoring.coreos.com
kubectl delete crd servicemonitors.monitoring.coreos.com
kubectl delete crd thanosrulers.monitoring.coreos.com
```


### loki-stack
I need only loki and promtail to gather the logs

```
helm repo add grafana https://grafana.github.io/helm-charts

helm show values loki grafana/loki-stack > loki-values.yaml

helm upgrade --install loki grafana/loki-stack -n monitoring  --set grafana.enabled=false,prometheus.enabled=false,prometheus.alertmanager.persistentVolume.enabled=false,prometheus.server.persistentVolume.enabled=false,loki.persistence.enabled=true,loki.persistence.storageClassName=longhorn,loki.persistence.size=25Gi

```

### registries
Create custom registry on docker using management/docker-compose.yaml - see registry image inside
Copy registries.yaml to master node /etc/rancher/k3s/registries.yaml
Copy/create docker daemon and set the private registry - on all nodes in /etc/docker/daemon.json with the content
This can be achieved using ansible playbooks

```
{
  "insecure-registries" : ["http://foxtrot.lan:5000"]
}
```


### longhorn
https://longhorn.io/docs/1.4.0/deploy/install/install-with-helm/

on each node run:
apt-get install open-iscsi

helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.4.0



### copy image for jdk

docker buildx create --use --name adubuilder --config .\buildx-config.toml

docker buildx build -t foxtrot.lan:5000/openjdk:11-jre -f .\docker-registry\Dockerfile --push --platform=linux/arm64,linux/amd64 .

docker run --name r-caller -p 8070:8080 foxtrot.lan:5000/ropt/ropt-reactive-caller:latest