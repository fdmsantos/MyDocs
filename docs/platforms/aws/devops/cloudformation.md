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

### Init

[Samples](https://github.com/widdix/aws-cf-templates)

Logs are stored in:

* /var/log/cfn-init*
* /var/log/cloud-init*

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Cloud Formation Template to create Jenkins Cluster
Parameters:
  VPC:
    Type: AWS::EC2::VPC::Id
  MasterSubnet:
    Type: AWS::EC2::Subnet::Id
  MasterInstanceType:
    Type: String
    Default: t2.micro
  KeyPair:
    Type: AWS::EC2::KeyPair::KeyName
Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      -
        Label:
          default: "Network Configuration"
        Parameters:
          - VPC
      -
        Label:
          default: "Master Jenkins Configuration"
        Parameters:
          - MasterSubnet
          - MasterInstanceType
      - Label:
          default: "Common Configuration"
        Parameters:
          - KeyPair
    ParameterLabels:
      VPC:
        default: "Which VPC should this be deployed to?"
      MasterSubnet:
        default: "Which Subnet should this be deployed to?"
      MasterInstanceType:
        default: "Which Instance type should this use?"
      KeyPair:
        default: "Which KeyPair should this use?"
Mappings:
  AMI:
    eu-west-1:
      "HVM64": "ami-04d5cc9b88f9d1d39"
Resources:
  Master:
    Type: AWS::EC2::Instance
    Metadata:
      AWS::CloudFormation::Init:
        configSets:
          full_install:
            - prepare
            - install
        prepare:
          files:
            /etc/yum.repos.d/jenkins.repo:
              source: http://pkg.jenkins-ci.org/redhat/jenkins.repo
          commands:
            jenkins-import-key:
              command: rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
              test: rpm -q gpg-pubkey-d50582e6-4a3feef6 | grep "not installed"
        install:
          packages:
            yum:
              java-1.8.0-openjdk-devel: []
              jenkins: []
          services:
            sysvinit:
              jenkins:
                enabled: "true"
                ensureRunning: "true"
    Properties:
      ImageId: !FindInMap [AMI, !Ref "AWS::Region", HVM64]
      InstanceType: !Ref MasterInstanceType
      KeyName: !Ref KeyPair
      SubnetId: !Ref MasterSubnet
      UserData:
        'Fn::Base64': !Sub |
          #!/bin/bash -x
          yum update -y
          /opt/aws/bin/cfn-init -v -c full_install --stack ${AWS::StackName} --resource Master --region ${AWS::Region}
          /opt/aws/bin/cfn-signal -e 0 --stack ${AWS::StackName} --resource Master --region ${AWS::Region}
      Tags:
        - Key: Name
          Value: Jenkins-Master
```