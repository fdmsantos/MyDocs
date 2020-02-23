## Manifest File

**Edit and re-deploy is automatically**

```bash
/etc/kubernetes/manifests/kube-controller-manager.yaml
```

###Metrics-Server

**Change Scale Down Time**

* Change --horizontal-pod-autoscaler-downscale-stabilization
* Default 5 minutes
* [Link](https://stackoverflow.com/questions/56903563/how-to-prevent-kubernetes-horizontal-auto-scaler-from-scaling-down/56905137#56905137)



