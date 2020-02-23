# Kops

```bash
# Install Kops
wget https://github.com/kubernetes/kops/releases/download/1.13.0/kops-linux-amd64

chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops

export REGION=eu-west-1

# Create S3
aws s3 mb s3://fsantos-k8s-state-store --region ${REGION}
aws s3api put-bucket-versioning --bucket fsantos-k8s-state-store --versioning-configuration Status=Enabled
aws s3api put-bucket-encryption --bucket fsantos-k8s-state-store --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'

export KOPS_STATE_STORE=s3://fsantos-k8s-state-store
export NAME=tiagomsantos.com


kops create secret --name ${NAME} sshpublickey admin -i ~/.ssh/id_rsa.pub

kops create cluster \
       --state ${KOPS_STATE_STORE} \
       --zones "eu-west-1a,eu-west-1b,eu-west-1c"  \
       --api-loadbalancer-type public \
       --master-count 3 \
       --master-size=t2.micro \
       --master-volume-size 8 \
       --node-count 3 \
       --node-size=t2.micro \
       --node-volume-size 8 \
       --name ${NAME} \
       --cloud aws \
       --networking calico \
       --topology private \
       --ssh-public-key ~/.ssh/id_rsa.pub \
       --api-ssl-certificate arn:aws:acm:eu-west-1:395563851492:certificate/9190b1fe-8a63-4432-9f6f-01a631c4f1b2 \
       --bastion \
       --yes
```