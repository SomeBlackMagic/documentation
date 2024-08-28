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
