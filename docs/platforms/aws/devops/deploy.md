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
  WebLaunchConfig: # Example with cfn-init in launch template
      Type: AWS::AutoScaling::LaunchConfiguration
      Metadata:
        Comment: Install a simple application
        AWS::CloudFormation::Init:
          config:
            packages:
              yum:
                httpd: []
            files:
              "/var/www/html/index.html":
                content:
                  Fn::Join:
                    - "\n"
                    - - <img src=https://s3-eu-west-1.amazonaws.com/cloudformation-examples-eu-west-1/cloudformation_graphic.png" alt="AWS CloudFormation Logo"/>
                      - "<h1>Congratulations, you have successfully launched the AWS CloudFormation
                          sample.</h1>"
                mode: '000644'
                owner: root
                group: root
              "/etc/cfn/cfn-hup.conf":
                content:
                  Fn::Join:
                    - ''
                    - - "[main]\n"
                      - stack=
                      - Ref: AWS::StackId
                      - "\n"
                      - region=
                      - Ref: AWS::Region
                      - "\n"
                mode: '000400'
                owner: root
                group: root
              "/etc/cfn/hooks.d/cfn-auto-reloader.conf":
                content:
                  Fn::Join:
                    - ''
                    - - "[cfn-auto-reloader-hook]\n"
                      - 'triggers=post.update'
                      - 'path=Resources.WebLaunchConfig.Metadata.AWS::CloudFormation::Init'
                      - 'action=/opt/aws/bin/cfn-init -v '
                      - "         --stack "
                      - Ref: AWS::StackName
                      - "         --resource WebLaunchConfig "
                      - "         --region "
                      - Ref: AWS::Region
                      - "\n"
                      - 'runas=root'
                mode: '000400'
                owner: root
                group: root
            services:
              sysvinit:
                httpd:
                  enabled: 'true'
                  ensureRunning: 'true'
                cfn-hup:
                  enabled: 'true'
                  ensureRunning: 'true'
                  files:
                    - "/etc/cfn/cfn-hup.conf"
                    - "/etc/cfn/hooks.d/cfn-auto-reloader.conf"
      Properties:
        KeyName: !Ref KeyPair
        LaunchConfigurationName: !Sub ${Name}-web-launchconfig
        ImageId: ami-08935252a36e25f85
        InstanceType: t2.micro
        SecurityGroups:
          - Ref: WebInstancesSecurityGroup
        UserData:
          Fn::Base64:
            Fn::Join:
              - ''
              - - "#!/bin/bash -xe\n"
                - 'yum update -y aws-cfn-bootstrap'
                - 'yum update -y aws-cli'
                - "/opt/aws/bin/cfn-init -v "
                - "         --stack "
                - Ref: AWS::StackName
                - "         --resource WebLaunchConfig "
                - "         --region "
                - Ref: AWS::Region
                - "\n"
                - "/opt/aws/bin/cfn-signal -e $? "
                - "         --stack "
                - Ref: AWS::StackName
                - "         --resource WebServerGroup "
                - "         --region "
                - Ref: AWS::Region
                - "\n"
```