<!-- TOC -->

- [General Packages](#general-packages)
- [Docker](#docker)
- [Kubeadm, Kubelet and Kubectl](#kubeadm-kubelet-and-kubectl)
- [Aliases](#aliases)

<!-- TOC -->

# General Packages

Ubuntu:

Disable local firewall and update packages.

```bash
sudo systemctl stop ufw
sudo systemctl disable ufw

sudo apt update
sudo apt upgrade -y
```

Install the follow packages.

```bash
sudo apt install -y apt-transport-https ca-certificates vim traceroute telnet tcpdump elinks curl wget openssl netcat net-tools jq
```

# Docker

Install Docker CE (Community Edition) with commands:

* Ubuntu: https://docs.docker.com/install/linux/docker-ce/ubuntu/

```bash
cd /home/vagrant

sudo curl -fsSL https://get.docker.com | bash

sudo su

cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

exit

sudo mkdir -p /etc/systemd/system/docker.service.d
sudo systemctl daemon-reload
sudo systemctl restart docker

# Start the Docker service
sudo systemctl start docker

# Configure Docker to boot up with the OS
sudo systemctl enable docker

# Add your user to the Docker group
sudo usermod -aG docker $USER
sudo setfacl -m user:$USER:rw /var/run/docker.sock

# Check whether the Cgroup driver has been set correctly
# If the output was Cgroup Driver: systemd, all right!
docker info | grep -i cgroup
```

References:

* https://docs.docker.com
* https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot
* https://github.com/badtuxx/DescomplicandoKubernetes/blob/main/pt/day_one/descomplicando_kubernetes.md#instala%C3%A7%C3%A3o-do-docker-e-do-kubernetes
* http://blog.aeciopires.com/primeiros-passos-com-docker

# Kubeadm, Kubelet and Kubectl

* Install Kubernetes with kubeadm:

```bash
sudo swapoff -a

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/k8s.list

cat /etc/apt/sources.list.d/k8s.list

sudo apt update

# Command to get version of packages
#sudo apt-cache show kubeadm kubectl kubelet | grep 1.22 | more

sudo apt install -y kubelet=1.22.6-00 kubeadm=1.22.6-00 kubectl=1.22.6-00

# List versions
kubeadm version

kubelet --version

kubectl version --client
```

More information about ``kubectl``: https://kubernetes.io/docs/reference/kubectl/overview/

References:

* https://kubernetes.io/docs/tasks/tools/install-kubectl/
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm
* https://kubernetes.io/releases/version-skew-policy/

# Aliases

Useful aliases to be registered in the ``$HOME/.bashrc`` file.

```bash
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias k='kubectl'
alias kssh='kubectl run ssh-client -it --rm --image=kroniak/ssh-client -n default -- bash'
alias nettools='kubectl run --rm -it nettools --image=travelping/nettools:latest -n default -- bash'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
```

* Apply new aliases

```bash
source ~/.bashrc
source /root/.bashrc
```
