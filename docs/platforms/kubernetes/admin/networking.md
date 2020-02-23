## Flannel

### Redhat

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### Ubuntu

* **On all nodes**

```bash
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

* **Master**

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

## Weave Net

* Setting up

```bash
# First, log in to both worker nodes and enable IP forwarding:
sudo sysctl net.ipv4.conf.all.forwarding=1
echo "net.ipv4.conf.all.forwarding=1" | sudo tee -a /etc/sysctl.conf

# The remaining commands can be done using kubectl. To connect with kubectl, you can either log in to one of the control nodes and run kubectl there or open an SSH tunnel for port 6443 to the load balancer server and use kubectl locally.
# You can open the SSH tunnel by running this in a separate terminal. Leave the session open while you are working to keep the tunnel active:
ssh -L 6443:localhost:6443 user@<your Load balancer cloud server public IP>

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')&env.IPALLOC_RANGE=10.200.0.0/16"

# Now Weave Net is installed, but we need to test our network to make sure everything is working.
# First, make sure the Weave Net pods are up and running:

kubectl get pods -n kube-system
#cThis should return two Weave Net pods, and look something like this:
NAME              READY     STATUS    RESTARTS   AGE
weave-net-m69xq   2/2       Running   0          11s
weave-net-vmb2n   2/2       Running   0          11s

# Next, we want to test that pods can connect to each other and that they can connect to services. We will set up two Nginx pods and a service for those two pods. Then, we will create a busybox pod and use it to test connectivity to both Nginx pods and the service.
# First, create an Nginx deployment with 2 replicas:

cat << EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      run: nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# Next, create a service for that deployment so that we can test connectivity to services as well:

# kubectl expose deployment/nginx

# Now let's start up another pod. We will use this pod to test our networking. We will test whether we can connect to the other pods and services from this pod.
kubectl run busybox --image=radial/busyboxplus:curl --command -- sleep 3600
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")

# Now let's get the IP addresses of our two Nginx pods:
kubectl get ep nginx

# There should be two IP addresses listed under ENDPOINTS, for example:
NAME      ENDPOINTS                       AGE
nginx     10.200.0.2:80,10.200.128.1:80   50m

# Now let's make sure the busybox pod can connect to the Nginx pods on both of those IP addresses.
kubectl exec $POD_NAME -- curl <first nginx pod IP address>
kubectl exec $POD_NAME -- curl <second nginx pod IP address>

# Both commands should return some HTML with the title "Welcome to Nginx!" This means that we can successfully connect to other pods.
# Now let's verify that we can connect to services.

kubectl get svc

# This should display the IP address for our Nginx service. For example, in this case, the IP is 10.32.0.54:
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.32.0.1    <none>        443/TCP   1h
nginx        ClusterIP   10.32.0.54   <none>        80/TCP    53m

# Let's see if we can access the service from the busybox pod!
kubectl exec $POD_NAME -- curl <nginx service IP address>
```

## Network Policies

### Plugin Canal

```bash
wget -O canal.yaml https://docs.projectcalico.org/v3.5/getting-started/kubernetes/installation/hosted/canal/canal.yaml
```

### Deny All

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

### Pod Selector

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-netpolicy
spec:
  podSelector:
    matchLabels:
      app: db
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: web
    ports:
    - port: 5432
```

### Namespace Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ns-netpolicy
spec:
  podSelector:
    matchLabels:
      app: db
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          tenant: web
    ports:
    - port: 5432
```

### Block IP

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ipblock-netpolicy
spec:
  podSelector:
    matchLabels:
      app: db
  ingress:
  - from:
    - ipBlock:
        cidr: 192.168.1.0/24
```

### Egress Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: egress-netpol
spec:
  podSelector:
    matchLabels:
      app: web
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: db
    ports:
    - port: 5432
```

## DNS

### Kube-Dns

```bash
kubectl create -f https://storage.googleapis.com/kubernetes-the-hard-way/kube-dns.yaml

# Verify that the kube-dns pod starts up correctly:

kubectl get pods -l k8s-app=kube-dns -n kube-system

# You should get output showing the kube-dns pod. It should look something like this:
NAME                        READY     STATUS    RESTARTS   AGE
kube-dns-598d7bf7d4-spbmj   3/3       Running   0          36s

# Make sure that 3/3 containers are ready, and that the pod has a status of Running. It may take a moment for the pod to be fully up and running, so if READY is not 3/3 at first, check again after a few moments.
# Now let's test our kube-dns installation by doing a DNS lookup from within a pod. First, we need to start up a pod that we can use for testing:
kubectl run busybox --image=busybox:1.28 --command -- sleep 3600
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")

# Next, run an nslookup from inside the busybox container:
kubectl exec -ti $POD_NAME -- nslookup kubernetes

# You should get output that looks something like this:
Server:    10.32.0.10
Address 1: 10.32.0.10 kube-dns.kube-system.svc.cluster.local

Name:      kubernetes
Address 1: 10.32.0.1 kubernetes.default.svc.cluster.local

```

### Custom DNS

```yaml
apiVersion: v1
kind: Pod
metadata:
  namespace: default
  name: dns-example
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 8.8.8.8
    searches:
      - ns1.svc.cluster.local
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```