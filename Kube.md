# Kubectl helm and other commands

## Install microk8s in ubuntu with snap
```bash 
#!/usr/bin/env bash

set -e
set -u
set -o pipefail

apt update
apt install -y snapd
sudo snap install microk8s --classic
microk8s status --wait-ready
microk8s.enable dns
microk8s stop
microk8s start

microk8s config > /root/.kube/config
snap install kubectl --classic
snap install helm3

```

## Setup rancher on microk8s
```
snap install microk8s --classic --channel=1.29.0
microk8s status --wait-ready
microk8s.enable dns
microk8s enable rbac
microk8s config > /root/.kube/config
```

## Hetzner vps dns
```
      dhcp4-overrides:
        use-dns: false
      nameservers:
        search: [company.com]
        addresses:
          - 8.8.8.8
          - 8.8.4.4
          - 1.1.1.1
```

## Install rancher on microk8s
https://nready.net/deploying-a-kubernetes-environment-with-microk8s-and-rancher/


## Graceful Node Shutdown
Add to /etc/kubernetes/kubelet-config.yaml
```
featureGates:
  GracefulNodeShutdown: true
shutdownGracePeriod: 60s
shutdownGracePeriodCriticalPods: 20s
```
Test time limit in 



```
systemd-inhibit --list
busctl get-property org.freedesktop.login1 /org/freedesktop/login1 org.freedesktop.login1.Manager InhibitDelayMaxUSec

grep -r "InhibitDelayMaxSec" /etc/systemd/ /usr/lib/systemd/

sudo nano /usr/lib/systemd/logind.conf.d/unattended-upgrades-logind-maxdelay.conf

InhibitDelayMaxSec=60

sudo systemctl daemon-reload
sudo systemctl restart systemd-logind

```
