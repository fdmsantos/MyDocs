## Exam tips

```bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc

export ns=default
alias k='kubectl -n $ns' # This helps when namespace in question doesn't have a friendly name 
# não funciona
alias kdr= 'kubectl -n $ns -o yaml --dry-run'.  # run commands in dry run mode and generate yaml.

export KUBE_EDITOR="nano"

$ k run nginx --image=nginx --restart=Never --dry-run -o yaml > mypod.yaml

$ k run nginx --image=nginx   (deployment)
$ k run nginx --image=nginx --restart=Never   (pod)
$ k run busybox --image=busybox --restart=OnFailure   (job)
$ k run busybox --image=busybox --schedule="* * * * *"  --restart=OnFailure (cronJob)

$ k explain pod.spec.containers
$ k explain deployment.spec.template.metadata
```

## Useful Links

[Exercises](https://github.com/dgkanatsios/CKAD-exercises)

[CKAD-Home](https://www.cncf.io/certification/ckad/)

[Kubernetes Docs](https://kubernetes.io/docs/home/)
