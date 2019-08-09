# Pre-Requisites

## Redhat

* **Disable SELinux**

```bash
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

* **Enable the br_netfilter module for cluster communication**

```bash
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

* **Disable swap to prevent memory allocation issues**

```bash
swapoff -a
vi /etc/fstab -> Comment out the swap line
```

# Docker

## Redhat

* **Install the Docker prerequisites**

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```

* **Add the Docker repo and install Docker**

```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce
```

* **Configure the Docker Cgroup Driver to systemd, enable and start Docker**

```bash
sed -i '/^ExecStart/ s/$/ --exec-opt native.cgroupdriver=systemd/' /usr/lib/systemd/system/docker.service 
systemctl daemon-reload
systemctl enable docker --now
```

## Ubuntu

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu

sudo apt-mark hold docker-ce
```

## Amazon AMI Linux 2

```bash
amazon-linux-extras install -y docker
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
systemctl daemon-reload
systemctl enable docker
systemctl start docker
usermod -a -G docker ec2-user
```

# kubernetes

## Redhat

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
   https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

yum install -y kubelet kubeadm kubectl
systemctl enable kubelet
kubeadm init --pod-network-cidr=10.244.0.0/16

# Exit sudo user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


## Ubuntu

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update

sudo apt-get install -y kubelet=1.12.7-00 kubeadm=1.12.7-00 kubectl=1.12.7-00

sudo apt-mark hold kubelet kubeadm kubectl

echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf

sudo sysctl -p

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

# Upgrade

## Ubuntu

```bash
kubectl version --short

# Release the hold on versions of kubeadm and kubelet
sudo apt-mark unhold kubeadm kubelet

sudo apt install -y kubeadm=1.14.1-00
sudo apt-mark hold kubeadm
kubeadm version

sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v1.14.1

sudo apt-mark unhold kubectl
sudo apt install -y kubectl=1.14.1-00
sudo apt-mark hold kubectl

sudo apt-mark unhold kubelet
sudo apt install -y kubelet=1.14.1-00
sudo apt-mark hold kubelet
```

# Maintenance

## Evict Pods

```bash
# Evict the pods on a node
kubectl drain [node_name] --ignore-daemonsets

kubectl get nodes -w

# Rollback - Schedule pods to the node after maintenance is complete
kubectl uncordon [node_name]
```

## Delete Node

```bash
kubectl delete node [node_name]
sudo kubeadm token generate

# Print the kubeadm join command to join a node to the cluster
sudo kubeadm token create [token_name] --ttl 2h --print-join-command
```

# Autocomple

## Redhat

```bash
# Enable Epel Repo
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum-config-manager --enable epel

# Instal bash completion
yum install bash-completion bash-completion-extras

vi /.bashrc # Add - source <(kubectl completion bash)
```