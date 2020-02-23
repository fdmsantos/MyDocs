## Create Cluster

```bash
# Here are the commands used in the demo (note that these have to be run on both controller servers, with a few differences between them):
wget -q --show-progress --https-only --timestamping \
  "https://github.com/coreos/etcd/releases/download/v3.3.5/etcd-v3.3.5-linux-amd64.tar.gz"
tar -xvf etcd-v3.3.5-linux-amd64.tar.gz
sudo mv etcd-v3.3.5-linux-amd64/etcd* /usr/local/bin/
sudo mkdir -p /etc/etcd /var/lib/etcd
sudo cp ca.pem kubernetes-key.pem kubernetes.pem /etc/etcd/

# Set up the following environment variables. Be sure you replace all of the <placeholder values> with their corresponding real values:
export ETCD_NAME=<cloud server hostname>
export INTERNAL_IP=$(curl http://169.254.169.254/latest/meta-data/local-ipv4)
export INITIAL_CLUSTER=<controller 1 hostname>=https://<controller 1 private ip>:2380,<controller 2 hostname>=https://<controller 2 private ip>:2380

# Create the systemd unit file for etcd using this command. Note that this command uses the environment variables that were set earlier:
cat << EOF | sudo tee /etc/systemd/system/etcd.service
[Unit]
Description=etcd
Documentation=https://github.com/coreos

[Service]
ExecStart=/usr/local/bin/etcd \\
  --name ${ETCD_NAME} \\
  --cert-file=/etc/etcd/kubernetes.pem \\
  --key-file=/etc/etcd/kubernetes-key.pem \\
  --peer-cert-file=/etc/etcd/kubernetes.pem \\
  --peer-key-file=/etc/etcd/kubernetes-key.pem \\
  --trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-client-cert-auth \\
  --client-cert-auth \\
  --initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
  --advertise-client-urls https://${INTERNAL_IP}:2379 \\
  --initial-cluster-token etcd-cluster-0 \\
  --initial-cluster ${INITIAL_CLUSTER} \\
  --initial-cluster-state new \\
  --data-dir=/var/lib/etcd
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

# Start and enable the etcd service:
sudo systemctl daemon-reload
sudo systemctl enable etcd
sudo systemctl start etcd

# You can verify that the etcd service started up successfully like so:
sudo systemctl status etcd

# Use this command to verify that etcd is working correctly. The output should list your two etcd nodes:
sudo ETCDCTL_API=3 etcdctl member list \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/kubernetes.pem \
  --key=/etc/etcd/kubernetes-key.pem
```

## ETCD Command Line

### Install

```bash
wget https://github.com/etcd-io/etcd/releases/download/v3.3.12/etcd-v3.3.12-linux-amd64.tar.gz
tar xvf etcd-v3.3.12-linux-amd64.tar.gz
sudo mv etcd-v3.3.12-linux-amd64/etcd* /usr/local/bin
```

### Snapshot

```bash
sudo ETCDCTL_API=3 etcdctl snapshot save snapshot.db --cacert /etc/kubernetes/pki/etcd/server.crt --cert /etc/kubernetes/pki/etcd/ca.crt --key /etc/kubernetes/pki/etcd/ca.key

# View that the snapshot was successful
ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db
```

### Docker

#### Find Command Parameters

```bash
ps -ef | grep etcd
```

#### Get All Keys

```bash
docker exec -it 3606376c1aba /bin/sh -c "export ETCDCTL_API=3 && etcdctl --endpoints=https://127.0.0.1:2379 --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --cacert=/etc/kubernetes/pki/etcd/ca.crt get / --prefix --keys-only"
```


## Backup and Restore

```bash
# Get the etcd binaries:
wget https://github.com/etcd-io/etcd/releases/download/v3.3.12/etcd-v3.3.12-linux-amd64.tar.gz

# Unzip the compressed binaries:
tar xvf etcd-v3.3.12-linux-amd64.tar.gz

# Move the files into /usr/local/bin:
sudo mv etcd-v3.3.12-linux-amd64/etcd* /usr/local/bin

# Take a snapshot of the etcd datastore using etcdctl:
sudo ETCDCTL_API=3 etcdctl snapshot save snapshot.db --cacert /etc/kubernetes/pki/etcd/server.crt --cert /etc/kubernetes/pki/etcd/ca.crt --key /etc/kubernetes/pki/etcd/ca.key

# View the help page for etcdctl:
ETCDCTL_API=3 etcdctl --help

# Browse to the folder that contains the certificate files:
cd /etc/kubernetes/pki/etcd/

# View that the snapshot was successful:
ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db

# Zip up the contents of the etcd directory:
sudo tar -zcvf etcd.tar.gz /etc/kubernetes/pki/etcd

# Copy the etcd directory to another server:
scp etcd.tar.gz cloud_user@18.219.235.42:~/
```