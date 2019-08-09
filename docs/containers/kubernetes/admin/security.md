# End-To End Tests
 
## CheckList

1. Deployments can run

2. Pods can run

3. Pods can be directly accessed

4. Logs can be collected

5. Commands run from pod

6. Services can provide access

7. Nodes are healthy

8. Pods are healthy

# Service Accounts

## Get

```bash
kubectl get serviceaccounts
```

## Create 

```bash
kubectl get serviceaccounts
kubectl get serviceaccounts jenkins -o yaml

```

## Pod Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  serviceAccountName: jenkins
  containers:
  - image: busybox:1.28.4
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always
```

## View the token file from within a pod

```bash
kubectl get pods -n my-ns
kubectl exec -it <name-of-pod> -n my-ns sh
cat /var/run/secrets/kubernetes.io/serviceaccount/token
```

# Users

## Create

```bash
kubectl config view
kubectl config set-credentials chad --username=chad --password=password
# Create a role binding for anonymous users (not recommended in production):
kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=system:anonymous

# Need Copy /etc/kubernetes/pki/ca.crt to remote machine
# Remote Machine (Install Kubectl)
kubectl config set-cluster kubernetes --server=https://172.31.41.61:6443 --certificate-authority=ca.crt --embed-certs=true
kubectl config set-credentials chad --username=chad --password=password
kubectl config set-context kubernetes --cluster=kubernetes --user=chad --namespace=default
kubectl config use-context kubernetes
```

# Roles

## Role 

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: web
  name: service-reader
rules:
- apiGroups: [""]
  verbs: ["get", "list"]
  resources: ["services"]
```

## RoleBinding

```yaml
kubectl create rolebinding test --role=service-reader --serviceaccount=web:default -n web
```

# Cluster Roles

## Cluster Role

```bash
kubectl create clusterrole pv-reader --verb=get,list --resource=persistentvolumes
```

## Cluster Role Binding

```bash
kubectl create clusterrolebinding pv-test --clusterrole=pv-reader --serviceaccount=web:default
```

## Test

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: curlpod
  namespace: web
spec:
  containers:
  - image: tutum/curl
    command: ["sleep", "9999999"]
    name: main
  - image: linuxacademycontent/kubectl-proxy
    name: proxy
  restartPolicy: Always
```

```bash
kubectl apply -f curl-pod.yaml
kubectl get pods -n web
kubectl exec -it curlpod -n web -- sh
curl localhost:8001/api/v1/persistentvolumes
```

# TLS Certficates

## Create

```bash
# Find the CA certificate on a pod in your cluster:
kubectl exec busybox -- ls /var/run/secrets/kubernetes.io/serviceaccount
# Download the binaries for the cfssl tool:
wget -q --show-progress --https-only --timestamping \
  https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 \
  https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64

# Make the binary files executable:
chmod +x cfssl_linux-amd64 cfssljson_linux-amd64
sudo mv cfssl_linux-amd64 /usr/local/bin/cfssl
sudo mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
cfssl version
# Create a CSR file
cat <<EOF | cfssl genkey - | cfssljson -bare server
{
  "hosts": [
    "my-svc.my-namespace.svc.cluster.local",
    "my-pod.my-namespace.pod.cluster.local",
    "172.168.0.24",
    "10.0.34.2"
  ],
  "CN": "my-pod.my-namespace.pod.cluster.local",
  "key": {
    "algo": "ecdsa",
    "size": 256
  }
}
EOF

# Create a CertificateSigningRequest API object:
cat <<EOF | kubectl create -f -
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: pod-csr.web
spec:
  groups:
  - system:authenticated
  request: $(cat server.csr | base64 | tr -d '\n')
  usages:
  - digital signature
  - key encipherment
  - server auth
EOF

# View the CSRs in the cluster:
kubectl get csr

# View additional details about the CSR:
kubectl describe csr pod-csr.web

# Approve the CSR:
kubectl certificate approve pod-csr.web

# View the certificate within your CSR:
kubectl get csr pod-csr.web -o yaml

# Extract and decode your certificate to use in a file:
kubectl get csr pod-csr.web -o jsonpath='{.status.certificate}' \
    | base64 --decode > server.crt
```

# Container Registry

## Create

```bash
# Create a new docker-registry secret:
kubectl create secret docker-registry acr --docker-server=https://podofminerva.azurecr.io --docker-username=podofminerva --docker-password='otj701c9OucKZOCx5qrRblofcNRf3W+e' --docker-email=user@example.com

# Modify the default service account to use your new docker-registry secret:
kubectl patch sa default -p '{"imagePullSecrets": [{"name": "acr"}]}'
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: acr-pod
  labels:
    app: busybox
spec:
  containers:
    - name: busybox
      image: podofminerva.azurecr.io/busybox:latest
      command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
      imagePullPolicy: Always
```

# Security Contexts

* **The YAML for a container that runs as a user**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: alpine-user-context
spec:
  containers:
  - name: main
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      runAsUser: 405
```


* **The YAML for a pod that runs the container as non-root**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: alpine-nonroot
spec:
  containers:
  - name: main
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      runAsNonRoot: true
```

* **The YAML for a privileged container pod**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privileged-pod
spec:
  containers:
  - name: main
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      privileged: true
```

* **The YAML for a container that will allow you to change the time**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kernelchange-pod
spec:
  containers:
  - name: main
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      capabilities:
        add:
        - SYS_TIME
```

* **The YAML for a container that removes capabilities**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: remove-capabilities
spec:
  containers:
  - name: main
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      capabilities:
        drop:
        - CHOWN
```

* **The YAML for a pod container that can’t write to the local filesystem**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readonly-pod
spec:
  containers:
  - name: main
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      readOnlyRootFilesystem: true
    volumeMounts:
    - name: my-volume
      mountPath: /volume
      readOnly: false
  volumes:
  - name: my-volume
    emptyDir:
```

* **The YAML for a pod that has different group permissions for different pods**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: group-context
spec:
  securityContext:
    fsGroup: 555
    supplementalGroups: [666, 777]
  containers:
  - name: first
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      runAsUser: 1111
    volumeMounts:
    - name: shared-volume
      mountPath: /volume
      readOnly: false
  - name: second
    image: alpine
    command: ["/bin/sleep", "999999"]
    securityContext:
      runAsUser: 2222
    volumeMounts:
    - name: shared-volume
      mountPath: /volume
      readOnly: false
  volumes:
  - name: shared-volume
    emptyDir:
```

# Persistent Key Value Store

```bash
# Generate a key for your https server:
openssl genrsa -out https.key 2048

# Generate a certificate for the https server:
openssl req -new -x509 -key https.key -out https.cert -days 3650 -subj /CN=www.example.com

# Create an empty file to create the secret:
touch file

# Create a secret from your key, cert, and file:
kubectl create secret generic example-https --from-file=https.key --from-file=https.cert --from-file=file
```

* **Create the configMap that will mount to your pod**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: config
data:
  my-nginx-config.conf: |
    server {
        listen              80;
        listen              443 ssl;
        server_name         www.example.com;
        ssl_certificate     certs/https.cert;
        ssl_certificate_key certs/https.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

    }
  sleep-interval: |
    25
```

* **The YAML for a pod using the new secret**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-https
spec:
  containers:
  - image: linuxacademycontent/fortune
    name: html-web
    env:
    - name: INTERVAL
      valueFrom:
        configMapKeyRef:
          name: config
          key: sleep-interval
    volumeMounts:
    - name: html
      mountPath: /var/htdocs
  - image: nginx:alpine
    name: web-server
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
      readOnly: true
    - name: config
      mountPath: /etc/nginx/conf.d
      readOnly: true
    - name: certs
      mountPath: /etc/nginx/certs/
      readOnly: true
    ports:
    - containerPort: 80
    - containerPort: 443
  volumes:
  - name: html
    emptyDir: {}
  - name: config
    configMap:
      name: config
      items:
      - key: my-nginx-config.conf
        path: https.conf
  - name: certs
    secret:
      secretName: example-https
```

```bash
# Use port forwarding on the pod to server traffic from 443:
kubectl port-forward example-https 8443:443 &

# Curl the web server to get a response:
curl https://localhost:8443 -k
```

