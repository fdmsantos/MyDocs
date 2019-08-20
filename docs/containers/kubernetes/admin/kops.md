# Create

```bash
kops create cluster \
       --state ${KOPS_STATE_STORE} \
       --zones "eu-west-1a,eu-west-1b,eu-west-1c"  \
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
       --api-ssl-certificate arn:aws:acm:eu-west-1:720008680639:certificate/47ceabbf-4add-4798-a240-972965b4befe \
       --bastion \
       --yes
```