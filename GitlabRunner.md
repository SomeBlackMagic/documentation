## Setup gitlab runner

```bash
apt install -y ncdu jq mc pv htop iftop iotop nmon 
```


docker: 
https://docs.docker.com/engine/install/ubuntu/
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

kubectl 

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl completion bash > /etc/bash_completion.d/kubectl
kubectl version --client
```


helm: 
https://helm.sh/docs/intro/install/#from-script
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm completion bash > /etc/bash_completion.d/helm
helm version
helm plugin install https://github.com/helm-unittest/helm-unittest.git

```

https://github.com/wagoodman/dive#installation
```bash
export DIVE_VERSION=$(curl -sL "https://api.github.com/repos/wagoodman/dive/releases/latest" | grep '"tag_name":' | sed -E 's/.*"v([^"]+)".*/\1/')
curl -OL https://github.com/wagoodman/dive/releases/download/v${DIVE_VERSION}/dive_${DIVE_VERSION}_linux_amd64.deb
sudo apt install ./dive_${DIVE_VERSION}_linux_amd64.deb
```
https://github.com/SomeBlackMagic/helm-assistant
```bash
wget https://github.com/SomeBlackMagic/helm-assistant/releases/latest/download/helm-assistant-linux-amd64
chmod +x helm-assistant-linux-amd64
mv helm-assistant-linux-amd64 /usr/local/bin/helm-assistant
```

https://github.com/SomeBlackMagic/kube-resource-cleanup
```bash
wget https://github.com/SomeBlackMagic/kube-resource-cleanup/releases/latest/download/kube-resource-cleanup-linux-amd64
chmod +x kube-resource-cleanup-linux-amd64
mv kube-resource-cleanup-linux-amd64 /usr/local/bin/kube-resource-cleanup
```

gitlab-runner
```bash
curl -LJO "https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb"
dpkg -i gitlab-runner_amd64.deb
usermod -aG docker gitlab-runner
su gitlab-runner
helm repo add someblackmagic https://someblackmagic.github.io/helm-charts/
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
exit
```

```text
root@gitlab-runner1:~# gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=1181 revision=dcfb4b66 version=15.10.1
Running in system-mode.                            

https://gitlab.com/
Enter the registration token:
[**************************************]
Enter a description for the runner:
[gitlab-runner1.dev]: 
Enter tags for the runner (comma-separated):
kubectl,docker,helm,dev
Enter optional maintenance note for the runner:


Enter an executor: instance, kubernetes, virtualbox, docker, docker-ssh, parallels, shell, ssh, docker+machine, docker-ssh+machine, custom:
shell  
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
 
Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml" 
```

```yaml
  tags:
    - dev
```


Crontab
```bash
0 0 * * * /root/clean_docker_cache.sh
```

clean_docker_cache.sh
```bash
#!/bin/bash
set -eu
echo "[$(date)] Start image prune"
/usr/bin/docker image prune --all --force > /dev/null 2>&1
echo "[$(date)] Start docker system prune"
/usr/bin/docker system prune --force > /dev/null 2>&1
echo "[$(date)] Remove layers2"
systemctl stop docker.socket
service docker stop
rm -rf /var/lib/docker
systemctl start docker.socket
service docker start
echo "[$(date)] done"
```
