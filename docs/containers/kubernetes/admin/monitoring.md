# Monitoring

## Cluster Components

### Metrics Server

```bash
git clone https://github.com/linuxacademy/metrics-server
kubectl apply -f ~/metrics-server/deploy/1.8+/

# Get a response from the metrics server API:
kubectl get --raw /apis/metrics.k8s.io/

kubectl top node
kubectl top pods
kubectl top pods --all-namespaces
kubectl top pods -n kube-system
kubectl top pod -l run=pod-with-defaults
kubectl top pod pod-with-defaults

# Get the CPU and memory of the containers inside the pod
kubectl top pods group-context --containers
```

## Applications

### Liveness Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness
spec:
  containers:
  - image: linuxacademycontent/kubeserve
    name: kubeserve
    livenessProbe:
      httpGet:
        path: /
        port: 80
```

### Readiness Probe

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
---
apiVersion: v1
kind: Pod
metadata:
  name: nginxpd
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:191
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5

```

# Logs

## Cluster

### Dirs

* **The directory where the continainer logs reside**

```bash
ls /var/log/containers
```


* **The directory where kubelet stores its logs**

```bash
ls /var/log
```

### SideCar Container

* **The YAML for a sidecar container that will tail the logs for each type**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/1.log;
        echo "$(date) INFO $i" >> /var/log/2.log;
        i=$((i+1));
        sleep 1;
      done
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  - name: count-log-1
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/1.log']
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  - name: count-log-2
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/2.log']
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  volumes:
  - name: varlog
    emptyDir: {}

```

```bash
kubectl logs counter count-log-1
kubectl logs counter count-log-2
```

## Application

```bash
# Get the logs from a pod:
kubectl logs nginx

# Get the logs from a specific container on a pod:
kubectl logs counter -c count-log-1

# Get the logs from all containers on the pod:
kubectl logs counter --all-containers=true

# Get the logs from containers with a certain label:
kubectl logs -lapp=nginx

# Get the logs from a previously terminated container within a pod:
kubectl logs -p -c nginx nginx

# Stream the logs from a container in a pod:
kubectl logs -f -c count-log-1 counter

# Tail the logs to only view a certain number of lines:
kubectl logs --tail=20 nginx

# View the logs from a previous time duration:
kubectl logs --since=1h nginx

# View the logs from a container within a pod within a deployment:
kubectl logs deployment/nginx -c nginx

# Redirect the output of the logs to a file:
kubectl logs counter -c count-log-1 > count.log
```

# Troubleshooting

## Applications

### Use Termination Reason

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod2
spec:
  containers:
  - image: busybox
    name: main
    command:
    - sh
    - -c
    - 'echo "I''ve had enough" > /var/termination-reason ; exit 1'
    terminationMessagePath: /var/termination-reason
```

### Healthz

* **Not all pods have healthz configured**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness
spec:
  containers:
  - image: linuxacademycontent/candy-service:2
    name: kubeserve
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8081
```

### Steps

```yaml
kubectl describe po pod2
kubectl logs pod-with-defaults
kubectl get po pod-with-defaults -o yaml --export > defaults-pod.yaml
```

## Cluster

```bash
# Check the events in the kube-system namespace for errors
kubectl get events -n kube-system

kubectl logs [kube_scheduler_pod_name] -n kube-system

# Check the status of the Docker service:
sudo systemctl status docker
sudo systemctl enable docker && systemctl start docker

# Check the status of the kubelet service:
sudo systemctl status kubelet
sudo systemctl enable kubelet && systemctl start kubelet

# Turn off swap on your machine
sudo su -
swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab

# Check if you have a firewall running:
sudo systemctl status firewalld
sudo systemctl disable firewalld && systemctl stop firewalld
```

## Worker Node

```bash
kubectl get nodes
kubectl describe nodes chadcrowell2c.mylabserver.com

# Create New Worker Server
# Generate a new token after spinning up a new server:
sudo kubeadm token generate

# Create the kubeadm join command for your new worker node:
# sudo kubeadm token create [token_name] --ttl 2h --print-join-command

# View the journalctl logs:
sudo journalctl -u kubelet

# View the syslogs:
sudo more syslog | tail -120 | grep kubelet
```

## Networking

### DNS

```bash
# Run an interactive busybox pod:
kubectl run -it --rm --restart=Never busybox --image=busybox:1.28 sh

# From the pod, check if DNS is resolving hostnames:
nslookup hostnames

# From the pod, cat out the /etc/resolv.conf file:
cat /etc/resolv.conf

# From the pod, look up the DNS name of the Kubernetes service:
nslookup kubernetes.default
nslookup kube-dns.kube-system.svc.cluster.loca

# Look up a service in your Kubernetes cluster
nslookup [pod-ip-address].default.pod.cluster.local

# Logs Core Dns
kubectl logs [coredns-pod-name]
```

### Kube-Proxy

```bash
# View the endpoints for your service:
kubectl get ep

# Communicate with the pod directly (without the service):
wget -qO- 10.244.1.6:9376

# Check if kube-proxy is running on the nodes:
ps auxw | grep kube-proxy

# Check if kube-proxy is writing iptables:
kubectl get services -o wide
iptables-save | grep hostnames
sudo iptables-save | grep KUBE | grep <service-name>

# View the list of kube-system pods:
kubectl get pods -n kube-system

# Connect to your kube-proxy pod in the kube-system namespace:
kubectl exec -it kube-proxy-cqptg -n kube-system -- sh
```

### Change CNI Plugin

```bash
# Delete the flannel CNI plugin:
kubectl delete -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml

# Apply the Weave Net CNI plugin:

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

