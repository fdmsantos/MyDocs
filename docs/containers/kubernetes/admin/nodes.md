# Get

```bash
kubectl get nodes
```

# Describe

```bash
kubectl describe node/node1
kubectl describe node/node2
kubectl describe node/master
```

# Api resources

```bash
kubectl api-resources -o wide
```

# Label

```bash
kubectl label node node1 availability-zone=zone1
kubectl label node node2 share-type=dedicated
```