# Pre-Requisites

## Redhat

* **Disable SELinux**

```bash
sudo setenforce 0
sudo sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

* **Enable the br_netfilter module for cluster communication**

```bash
sudo modprobe br_netfilter
sudo echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

* **Disable swap to prevent memory allocation issues**

```bash
swapoff -a
sudo vi /etc/fstab -> Comment out the swap line
```

# Docker

## Redhat

* **Install the Docker prerequisites**

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

* **Add the Docker repo and install Docker**

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce
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
sudo amazon-linux-extras install -y docker
sudo cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -a -G docker ec2-user

# Exit e login na sessao

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

sudo yum install -y kubelet kubeadm kubectl
sudo systemctl enable kubelet
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

# Autocomplete

## Redhat

```bash
# Enable Epel Repo
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum-config-manager --enable epel

# Instal bash completion
yum install bash-completion bash-completion-extras

vi /.bashrc # Add Following lines
# alias k=kubectl
# source <(kubectl completion bash | sed s/kubectl/k/g)
```

# Geral Commands

```bash
kubectl api-resources -o name
```

# Kubectl

## Install

```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

## Set Remote

```bash
kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority=ca.pem \
  --embed-certs=true \
  --server=https://localhost:6443

kubectl config set-credentials admin \
  --client-certificate=admin.pem \
  --client-key=admin-key.pem

kubectl config set-context kubernetes-the-hard-way \
  --cluster=kubernetes-the-hard-way \
  --user=admin

kubectl config use-context kubernetes-the-hard-way
```

## Insecure

* **File:** $HOME/.kube/config

```yaml
- cluster:
    insecure-skip-tls-verify: true
```

# Kube Configs

## Generate Kube Configs

[See How Generate TLS Certificates](/containers/kubernetes/admin/security/#tls-certficates)

```bash
# Create an environment variable to store the address of the Kubernetes API, and set it to the private IP of your load balancer cloud server:
KUBERNETES_ADDRESS=<load balancer private ip>

# Generate a kubelet kubeconfig for each worker node:
for instance in <worker 1 hostname> <worker 2 hostname>; do
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_ADDRESS}:6443 \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-credentials system:node:${instance} \
    --client-certificate=${instance}.pem \
    --client-key=${instance}-key.pem \
    --embed-certs=true \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${instance} \
    --kubeconfig=${instance}.kubeconfig

  kubectl config use-context default --kubeconfig=${instance}.kubeconfig
done

# Generate a kube-proxy kubeconfig
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_ADDRESS}:6443 \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-credentials system:kube-proxy \
    --client-certificate=kube-proxy.pem \
    --client-key=kube-proxy-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-proxy \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
}

# Generate a kube-controller-manager kubeconfig
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=kube-controller-manager.pem \
    --client-key=kube-controller-manager-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-controller-manager \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config use-context default --kubeconfig=kube-controller-manager.kubeconfig
}

# Generate a kube-scheduler kubeconfig
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-credentials system:kube-scheduler \
    --client-certificate=kube-scheduler.pem \
    --client-key=kube-scheduler-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-scheduler \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config use-context default --kubeconfig=kube-scheduler.kubeconfig
}

# Generate an admin kubeconfig
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=admin.kubeconfig

  kubectl config set-credentials admin \
    --client-certificate=admin.pem \
    --client-key=admin-key.pem \
    --embed-certs=true \
    --kubeconfig=admin.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=admin \
    --kubeconfig=admin.kubeconfig

  kubectl config use-context default --kubeconfig=admin.kubeconfig
}

```

##  Distributing the Kubeconfig Files

### Move kubeconfig files to the worker nodes:

```bash
scp <worker 1 hostname>.kubeconfig kube-proxy.kubeconfig user@<worker 1 public IP>:~/
scp <worker 2 hostname>.kubeconfig kube-proxy.kubeconfig user@<worker 2 public IP>:~/
```

### Move kubeconfig files to the master nodes:

```bash
scp admin.kubeconfig kube-controller-manager.kubeconfig kube-scheduler.kubeconfig user@<master 1 public IP>:~/
scp admin.kubeconfig kube-controller-manager.kubeconfig kube-scheduler.kubeconfig user@<master 2 public IP>:~/
```

# Data Encryption Config

```bash
# Generate the Kubernetes Data encrpytion config file containing the encrpytion key:
export ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)

cat > encryption-config.yaml << EOF
kind: EncryptionConfig
apiVersion: v1
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: ${ENCRYPTION_KEY}
      - identity: {}
EOF

# Copy the file to both master servers:
scp encryption-config.yaml user@<master 1 public ip>:~/
scp encryption-config.yaml user@<master 2 public ip>:~/

```

# LoadBalancer

## Setting UP

```bash
# Here are the commands you can use to set up the nginx load balancer. Run these on the server that you have designated as your load balancer server:
sudo apt-get install -y nginx
sudo systemctl enable nginx
sudo mkdir -p /etc/nginx/tcpconf.d
sudo vi /etc/nginx/nginx.conf

# Add the following to the end of nginx.conf:
include /etc/nginx/tcpconf.d/*;

# Set up some environment variables for the lead balancer config file:
export CONTROLLER0_IP=<controller 0 private ip>
export CONTROLLER1_IP=<controller 1 private ip>

# Create the load balancer nginx config file:
cat << EOF | sudo tee /etc/nginx/tcpconf.d/kubernetes.conf
stream {
    upstream kubernetes {
        server $CONTROLLER0_IP:6443;
        server $CONTROLLER1_IP:6443;
    }

    server {
        listen 6443;
        listen 443;
        proxy_pass kubernetes;
    }
}
EOF

# Reload the nginx configuration:
sudo nginx -s reload

# You can verify that the load balancer is working like so:
curl -k https://localhost:6443/version
```