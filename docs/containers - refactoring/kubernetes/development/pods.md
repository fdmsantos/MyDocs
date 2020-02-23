# Pods

## Get

```bash
kubectl get pods --all-namespaces
kubectl get pods --all-namespaces -o wide
kubectl get pods --namespace=podexample -o wide

kubectl get pods -o custom-columns=POD:metadata.name,NODE:spec.nodeName --sort-by spec.nodeName -n kube-system
```

## Example File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: examplepod
  namespace: pod-example
spec:
  schedulerName: default-scheduler # To change scheduler - my-scheduler
  volumes:
  - name: html
    emptyDir: {}
  containers:
  - name: webcontainer
    image: nginx
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
  - name: filecontainer
    image: debian
    volumeMounts:
    - name: html
      mountPath: /html
    command: ["/bin/sh", "-c"]
    args:
      - while true; do
         date >> /html/index.html;
         sleep 1;
        done
```

## Resources Requests and Limits

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-pod2
spec:
  nodeSelector:
    kubernetes.io/hostname: "chadcrowell3c.mylabserver.com"
  containers:
  - image: busybox
    command: ["dd", "if=/dev/zero", "of=/dev/null"]
    name: pod2
    resources:
      requests:
        cpu: 1000m
        memory: 20Mi
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: limited-pod
spec:
  containers:
  - image: busybox
    command: ["dd", "if=/dev/zero", "of=/dev/null"]
    name: main
    resources:
      limits:
        cpu: 1
        memory: 20Mi
```

## Create

* **From File**

```bash
kubectl create -f ./pod-example.yaml
```

## Delete

```bash
kubectl --namespace=podexample delete pod examplepod
```

## Get Containers Name inside a pod

```bash
kubectl get pods examplepod -n podexample -o jsonpath='{.spec.containers[*].name}*'
```

## Exec in Container

```bash
kubectl exec -ti examplepod -c webcontainer -n podexample /bin/bash
```

## Use port forwarding to access a pod directly

```bash
kubectl port-forward $pod_name 8081:80
```

# Namespaces

## Get

```bash
kubectl get namespaces
```

## Create

```bash
kubectl create namespace podexample
```


kubectl get pods examplepod -n podexample -o jsonpath='{.spec.containers[*].name}*'