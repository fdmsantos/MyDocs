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

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-liveness-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
    livenessProbe:
      exec:
        command:
        - echo
        - testing
      initialDelaySeconds: 5
      periodSeconds: 5
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

## Logs

### Cluster

#### Dirs

* **The directory where the continainer logs reside**

```bash
ls /var/log/containers
```


* **The directory where kubelet stores its logs**

```bash
ls /var/log
```

#### SideCar Container

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

### Application

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

## Troubleshooting

### Applications

#### Use Termination Reason

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

#### Healthz

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

#### Steps

```yaml
kubectl describe po pod2
kubectl logs pod-with-defaults
kubectl get po pod-with-defaults -o yaml --export > defaults-pod.yaml
```

### Cluster

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

### Worker Node

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

### Networking

#### DNS

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

#### Kube-Proxy

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

#### Change CNI Plugin

```bash
# Delete the flannel CNI plugin:
kubectl delete -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml

# Apply the Weave Net CNI plugin:

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

## Smoke Testing

### Data Encryption

```bash
# Create a test secret:
kubectl create secret generic kubernetes-the-hard-way --from-literal="mykey=mydata"

# Log in to one of your master servers, and get the raw data for the test secret from etcd:
sudo ETCDCTL_API=3 etcdctl get \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/kubernetes.pem \
  --key=/etc/etcd/kubernetes-key.pem\
  /registry/secrets/default/kubernetes-the-hard-way | hexdump -C

# Your output should look something like this:

00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
00000010  73 2f 64 65 66 61 75 6c  74 2f 6b 75 62 65 72 6e  |s/default/kubern|
00000020  65 74 65 73 2d 74 68 65  2d 68 61 72 64 2d 77 61  |etes-the-hard-wa|
00000030  79 0a 6b 38 73 3a 65 6e  63 3a 61 65 73 63 62 63  |y.k8s:enc:aescbc|
00000040  3a 76 31 3a 6b 65 79 31  3a fc 21 ee dc e5 84 8a  |:v1:key1:.!.....|
00000050  53 8e fd a9 72 a8 75 25  65 30 55 0e 72 43 1f 20  |S...r.u%e0U.rC. |
00000060  9f 07 15 4f 69 8a 79 a4  70 62 e9 ab f9 14 93 2e  |...Oi.y.pb......|
00000070  e5 59 3f ab a7 b2 d8 d6  05 84 84 aa c3 6f 8d 5c  |.Y?..........o.\|
00000080  09 7a 2f 82 81 b5 d5 ec  ba c7 23 34 46 d9 43 02  |.z/.......#4F.C.|
00000090  88 93 57 26 66 da 4e 8e  5c 24 44 6e 3e ec 9c 8e  |..W&f.N.\$Dn>...|
000000a0  83 ff 40 9a fb 94 07 3c  08 52 0e 77 50 81 c9 d0  |..@....<.R.wP...|
000000b0  b7 30 68 ba b1 b3 26 eb  b1 9f 3f f1 d7 76 86 09  |.0h...&...?..v..|
000000c0  d8 14 02 12 09 30 b0 60  b2 ad dc bb cf f5 77 e0  |.....0.`......w.|
000000d0  4f 0b 1f 74 79 c1 e7 20  1d 32 b2 68 01 19 93 fc  |O..ty.. .2.h....|
000000e0  f5 c8 8b 0b 16 7b 4f c2  6a 0a                    |.....{O.j.|
000000ea

# Look for k8s:enc:aescbc:v1:key1 on the right of the output to verify that the data is stored in an encrypted format!
```

### Deployments

```bash
# Create a a simple nginx deployment:
kubectl run nginx --image=nginx

# Verify that the deployment created a pod and that the pod is running:
kubectl get pods -l run=nginx

# Verify that the output looks something like this:
NAME                     READY     STATUS    RESTARTS   AGE
nginx-65899c769f-9xnqm   1/1       Running   0          30s
```

### Port Forwarding

```bash
# First, get the pod name of the nginx pod and store it an an environment variable:
POD_NAME=$(kubectl get pods -l run=nginx -o jsonpath="{.items[0].metadata.name}")

# Forward port 8081 to the nginx pod:
kubectl port-forward $POD_NAME 8081:80

# Open up a new terminal on the same machine running the kubectl port-forward command and verify that the port forward works.
curl --head http://127.0.0.1:8081

# You should get an http 200 OK response from the nginx pod.
```

### Logs

```bash
# First, let's set an environment variable to the name of the nginx pod:
POD_NAME=$(kubectl get pods -l run=nginx -o jsonpath="{.items[0].metadata.name}")

# Get the logs from the nginx pod:
kubectl logs $POD_NAME

# This command should return the nginx pod's logs. It will look something like this (but there could be more lines):
127.0.0.1 - - [10/Sep/2018:19:29:01 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
```

### Exec

```bash
# First, let's set an environment variable to the name of the nginx pod:
POD_NAME=$(kubectl get pods -l run=nginx -o jsonpath="{.items[0].metadata.name}")

# To test kubectl exec, execute a simple nginx -v command inside the nginx pod:
kubectl exec -ti $POD_NAME -- nginx -v

# This command should return the nginx version output, which should look like this:
nginx version: nginx/1.15.3

```

### Services

```bash
# First, create a service to expose the nginx deployment:
kubectl expose deployment nginx --port 80 --type NodePort

# Get the node port assigned to the newly-created service and assign it to an environment variable:
kubectl get svc

# The output should look something like this:
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.32.0.1    <none>        443/TCP        20d
nginx        NodePort    10.32.0.81   <none>        80:32642/TCP   2m

# Look for the service called nginx in that output. Under PORT(S), look for the second port, listed after 80:. In the example above, it is 32642. That is the node port, so make note of that value since you will need it in a moment.
# Next, log in to one of your worker servers and make a request to the service using the node port. Be sure to replace the placeholder with the actual node port:

curl -I localhost:<node port>

# You should get an http 200 OK response.
```

### Untrusted Workloads

```bash
# First, create an untrusted pod:
cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: untrusted
  annotations:
    io.kubernetes.cri.untrusted-workload: "true"
spec:
  containers:
    - name: webserver
      image: gcr.io/hightowerlabs/helloworld:2.0.0
EOF

# Make sure that the untrusted pod is running:
kubectl get pods untrusted -o wide

# Take note of which worker node the untrusted pod is running on, then log into that worker node.

# On the worker node, list all of the containers running under gVisor:
sudo runsc --root  /run/containerd/runsc/k8s.io list

# Get the pod ID of the untrusted pod and store it in an environment variable:
POD_ID=$(sudo crictl -r unix:///var/run/containerd/containerd.sock \
  pods --name untrusted -q)

# Get the container ID of the container running in the untrusted pod and store it in an environment variable:
CONTAINER_ID=$(sudo crictl -r unix:///var/run/containerd/containerd.sock \
  ps -p ${POD_ID} -q)

# Get information about the process running in the container:
sudo runsc --root /run/containerd/runsc/k8s.io ps ${CONTAINER_ID}

# Since we were able to get the process info using runsc, we know that the untrusted container is running securely as expected.
```