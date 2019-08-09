# Example File

## Simple Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment 
  labels: 
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx 
    spec: 
      containers:
      - name: nginx
        image: darealmc/nginx-k8s:v1
        ports:
        - containerPort: 80
```

## Node affinity

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: pref
spec:
  replicas: 5
  template:
    metadata:
      labels:
        app: pref
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 80
            preference:
              matchExpressions:
              - key: availability-zone
                operator: In
                values:
                - zone1
          - weight: 20
            preference:
              matchExpressions:
              - key: share-type
                operator: In
                values:
                - dedicated
      containers:
      - args:
        - sleep
        - "99999"
        image: busybox
        name: main

```

# Update Image

```bash
kubectl set image deployment.v1.apps/example-deployment nginx=darealmc/nginx-k8s:v2
```

# MicroServices Example

```bash
cd ~/
git clone https://github.com/linuxacademy/robot-shop.git
kubectl create namespace robot-shop
kubectl -n robot-shop create -f ~/robot-shop/K8s/descriptors/
kubectl get pods -n robot-shop -w
# Access in http://$kube_server_public_ip:30080
```

# Application LifeCycle Manager

## Update

```bash
kubectl apply -f kubeserve-deployment.yaml

kubectl replace -f kubeserve-deployment.yaml
```

## Rolling Update

```bash
kubectl set image deployments/kubeserve app=linuxacademycontent/kubeserve:v2 --v 6
```

## Rollback

```bash
# Use --record flag to create the deployment - kubectl create -f kubeserve-deployment.yaml --record
kubectl rollout undo deployments kubeserve
kubectl rollout history deployment kubeserve
kubectl rollout undo deployment kubeserve --to-revision=2
```

## Pause / Resume

```bash
kubectl rollout undo deployment kubeserve --to-revision=2
kubectl rollout resume deployment kubeserve
```

## Readiness Probe

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubeserve
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kubeserve
  minReadySeconds: 10
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
    type: RollingUpdate
  template:
    metadata:
      name: kubeserve
      labels:
        app: kubeserve
    spec:
      containers:
      - image: linuxacademycontent/kubeserve:v3
        name: app
        readinessProbe:
          periodSeconds: 1
          httpGet:
            path: /
            port: 80
```