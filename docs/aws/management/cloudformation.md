# Macro

## Dynamic Resources

[Github Example](https://github.com/pabloperfer/CFN_Macros/tree/master/Looping)

### Macro Template

```yaml
AWSTemplateFormatVersion: 2010-09-09
Resources:
  Macro:
    Type: AWS::CloudFormation::Macro
    Properties:
      Name: !Sub '${AWS::StackName}'
      Description: Transform the original template performing a for loop that adds the requested number of Users resources.
      FunctionName: !Ref MacroProcessor
  MacroProcessor: 
    Type: "AWS::Lambda::Function"
    Properties: 
      FunctionName: MacroProcessor
      Runtime: "python3.6"
      Description: IAM Users Macro processor function
      Handler: "index.lambda_handler"
      Code:
        ZipFile: |
          import json
    
          def lambda_handler(event, context):
            FinalFragment= event["fragment"]
            Number = FinalFragment["Parameters"]["NumberOfUsers"]
            #I declare a new dictionary to have the resources object
            NewUser = {}
            NewUser = FinalFragment["Resources"]
            for i in range(len(Number)):     
              #I modify the IAM User resource logical id in order to add it later to "Resources" object the number of times requested 

              NewUser["myuser"+str(i+2)] = NewUser["myuser"].copy()
             
              FinalFragment["Resources"].update(NewUser)

              NewUser["EC2Instance"+str(i+2)] = NewUser["EC2Instance"].copy()

              FinalFragment["Resources"].update(NewUser)  
            response = {}
            response["requestId"] = event["requestId"]
            response["status"] = "success"
            response["fragment"] = FinalFragment
            print (FinalFragment["Resources"])
            return response

      Timeout: "100"
      MemorySize: 128
      Role: !GetAtt LambdaExecutionRole.Arn

  LambdaExecutionRole:
      Type: AWS::IAM::Role
      Properties:
        RoleName: !Sub MacroProcessor-lambda-role-${AWS::Region}
        AssumeRolePolicyDocument:
          Version: 2012-10-17
          Statement:
            -
              Effect: Allow
              Principal:
                Service:
                  - lambda.amazonaws.com
              Action:
                - sts:AssumeRole
        Path: /

  LambdaPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName: !Sub MacroProcessor-lambda-policy-${AWS::Region}
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          -
            Effect: Allow
            Action: "cloudformation:*"
            Resource: "*"      
          -
            Effect: Allow
            Action: "logs:*"
            Resource: "*"      
      Roles:
        -
          !Ref LambdaExecutionRole
```

### CFN Template

```yaml
AWSTemplateFormatVersion: 2010-09-09 
Description: This template generates the requested number of IAM users that will have the same custom EC2 policy for EC2 resources tagged with Owner:devteam. The user will be prompted to reset their passwords on next sign-in. Also an EC2 instance will be created for each user simulating a classroom.
Transform: "Macro"
Parameters:
  NumberOfUsers:
    Type: String
    Description: Enter the number of users  to create for the Dev Team
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access to the web server
    Type: 'AWS::EC2::KeyPair::KeyName'
    ConstraintDescription: must be the name of an existing EC2 KeyPair.
  InstanceType:
    Description: WebServer EC2 instance type
    Type: String
    Default: t2.small
    AllowedValues:
      - t1.micro
      - t2.nano
      - t2.micro
      - t2.small
      - t2.medium
      - t2.large
    ConstraintDescription: must be a T2 EC2 instance type.
  SSHLocation:
    Description: >-
      Lockdown SSH access to the bastion host (default can be accessed from
      anywhere)
    Type: String
    MinLength: '9'
    MaxLength: '18'
    Default: 0.0.0.0/0
    AllowedPattern: '(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})/(\d{1,2})'
    ConstraintDescription: must be a valid CIDR range of the form x.x.x.x/x.

Mappings:
  AWSRegion2AMI:
    us-east-1: 
      AMI: "ami-97785bed"
    us-west-2: 
      AMI: "ami-f2d3638a"
    us-west-1: 
      AMI: "ami-824c4ee2"
    eu-west-1: 
      AMI: "ami-d834aba1"
    eu-west-2: 
      AMI: "ami-403e2524"
    eu-west-3: 
      AMI: "ami-8ee056f3"
    eu-central-1: 
      AMI: "ami-5652ce39"
    ap-northeast-1: 
      AMI: "ami-ceafcba8"
    ap-northeast-2: 
      AMI: "ami-863090e8"
    ap-northeast-3: 
      AMI: "ami-83444afe"
    ap-southeast-1: 
      AMI: "ami-68097514"
    ap-southeast-2: 
      AMI: "ami-942dd1f6"
    ap-south-1: 
      AMI: "ami-531a4c3c"
    us-east-2: 
      AMI: "ami-f63b1193"
    ca-central-1:   
      AMI: "ami-a954d1cd"
    sa-east-1: 
      AMI: "ami-84175ae8"
    cn-north-1: 
      AMI: "ami-cb19c4a6"
    cn-northwest-1: 
      AMI: "ami-3e60745c"

Resources:
  myuser:
    Type: AWS::IAM::User
    Properties:
      Path: "/"
      LoginProfile:
        Password: myP@ssW0rd
        PasswordResetRequired: yes
      ManagedPolicyArns:
        - !Ref DevTeamEC2Policy

  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      KeyName: !Ref KeyName
      InstanceType: !Ref InstanceType
      ImageId: !FindInMap [ AWSRegion2AMI, !Ref "AWS::Region", "AMI" ]
      SecurityGroups:
        - !Ref EC2SecurityGroup
      Tags:
        - Key: Owner
          Value: devteam

  EC2SecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: !Ref SSHLocation
    
  DevTeamEC2Policy: 
    Type: AWS::IAM::ManagedPolicy
    Properties: 
      Description: "Policy for EC2"
      Path: "/"
      PolicyDocument: 
        Version: "2012-10-17"
        Statement: 
         - Effect: Allow
           Action:
            - ec2:StartInstances
            - ec2:StopInstances
            - ec2:Describe*
            - ec2:Get*
           Resource: !Sub "arn:aws:ec2:${AWS::Region}:${AWS::AccountId}:instance/*"
           Condition:
             StringEquals: 
               ec2:ResourceTag/Owner: "devteam"
```

# Nested Stacks

## Nested Template

* **Must be stored in S3**

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Template to Create the Code Commit Repositories to DEV Env
Parameters:
  Name:
    Type: String
    Description: Repository Name
  Description:
    Type: String
    Description: Repository Description
Resources:
  Repository:
    Type: AWS::CodeCommit::Repository
    Properties:
      RepositoryDescription:
        Ref: Description
      RepositoryName:
        Ref: Name
```

## Main Template

```yaml
CodeCommitRepo:
    Type: AWS::CloudFormation::Stack
    Properties:
      Parameters:
        Name: ""
        Description: ""
      TemplateURL: https://dev-env-cfn-templates.s3-eu-west-1.amazonaws.com/codecommit-repos.yml
```

# Troubleshooting

## WaitCondition

**If WaitCondition times out or returns an error. There is an error in your cloudformation::init code**

1. Analyze cfn-init.log and cfn-wire.log for details collect logs via CloudWatch logs
2. –on-failure DO_NOTHING to keep instance from rolling back so you can log in and examine the logs
3. Most common error: URLs for referenced resources (scripts, MSIs, etc.) are returning HTTP 403 or 404 errors