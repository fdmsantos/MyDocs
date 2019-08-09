# etcdctl

## Install

```bash
wget https://github.com/etcd-io/etcd/releases/download/v3.3.12/etcd-v3.3.12-linux-amd64.tar.gz
tar xvf etcd-v3.3.12-linux-amd64.tar.gz
sudo mv etcd-v3.3.12-linux-amd64/etcd* /usr/local/bin
```

## Snapshot

```bash
sudo ETCDCTL_API=3 etcdctl snapshot save snapshot.db --cacert /etc/kubernetes/pki/etcd/server.crt --cert /etc/kubernetes/pki/etcd/ca.crt --key /etc/kubernetes/pki/etcd/ca.key

# View that the snapshot was successful
ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db
```

## Using Docker Container

### Find Command Parameters

```bash
ps -ef | grep etcd
```

### Get All Keys

```bash
docker exec -it 3606376c1aba /bin/sh -c "export ETCDCTL_API=3 && etcdctl --endpoints=https://127.0.0.1:2379 --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --cacert=/etc/kubernetes/pki/etcd/ca.crt get / --prefix --keys-only"
```