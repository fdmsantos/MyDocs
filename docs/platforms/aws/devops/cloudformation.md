## CDK

[Workshop](https://cdkworkshop.com/)

## CloudFormation

### Deploy

```bash
aws cloudformation package 
    --template-file template.yaml 
    --s3-bucket ${bucket} 
    --output-template-file output-template.yaml

aws cloudformation deploy 
    --template-file output-template.yaml 
    --capabilities CAPABILITY_IAM 
    --stack-name ${stack_name}
```

### Delete Stack

```bash
aws cloudformation delete-stack --stack-name ${stack_name}
```