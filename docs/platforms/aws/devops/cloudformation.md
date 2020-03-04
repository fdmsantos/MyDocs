## CDK

[Workshop](https://cdkworkshop.com/)

### app.py

```python
app = core.App()
SecurityGroupsStack(app, "security-groups", env={
    'account': os.environ['CDK_DEFAULT_ACCOUNT'],
    'region': os.environ['CDK_DEFAULT_REGION']
})

app.synth()
```

### Stack.py

```python
# Reference a outside VPC
vpc = ec2.Vpc.from_lookup(self, id="VPC", vpc_id=config['VPC_ID'])
```

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