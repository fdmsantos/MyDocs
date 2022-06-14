
# Helm

## Basic Commands

* Use same kubectl config
```bash
helm ls <-> helm list
helm search wordpress
helm install stable/wordpress --version 5.0.1
helm delete fantastic-molly
helm upgrade fantastic-molly
helm home -> where helm is install # at <helm_home>/repository/repositories.yaml -> where is the repos
helm search apache
helm inspect stable/wordpress

helm install ./helmcharts/jenkins.tar  -> Install local
helm install --name storage-release --set service.type=clusterip --set persistence.storageClass=rook-ceph-block stable/wordpress 
# or
helm install --values=./valuestor/customer-wordpress.yaml stable/wordpres
```

   
## Custom Helm

* Chart.yaml -> Chart Metadata
* requirements.yaml -> dependencies. Stored in charts folder
* values.yaml - Default Configuration - Can be override un heml install. use helm inspect to check the values
* templates -> Templates that combine with values to generate kubernetes manifest files


```bash
helm fetch --untar stable/wordpress 
# OR
helm create custom

helm package wordpress
rm -rf wordpress # Only upload the rar file
helm repo index .
git add .
git commit -m 'Initial'
git push
```

* Inside helm chart commands

```bash
helm dep list -> get the status of dependecies
helm dep update
heml install . -> install local
```

## Repositories

```bash
heml repo add custom_repo https://raw-.githubusercontent.com/darealmc/helmcharts/master
helm install custom_repo/wordpress
```


## Releases

* Needs to Change version on Chart.yaml, and package again
* helm repo index . => to update the index.yaml with new release
* helm search wordpress
* helm repo update
* helm upgrade --recreate-pods ironic-mink custom_repo/wordpress


## Server Tiller

```bash
kubectl get pods --all-namespaces | grep tiller
```


