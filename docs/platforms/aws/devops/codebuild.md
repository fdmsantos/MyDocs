# AWS Code Build

## Code Build Example with Github Tokens

```yaml
version: 0.2

env:
  git-credential-helper: no
  secrets-manager:
    GithubToken: "FabioSantosGitHub:GitHubToken"
    GitHubUser: "FabioSantosGitHub:GitHubUser"

phases:
  install:
    runtime-versions:
      ruby: 2.7
    commands:
      - echo "Install Terraform"
      - wget https://releases.hashicorp.com/terraform/"$TERRAFORM_VERSION"/terraform_"$TERRAFORM_VERSION"_linux_amd64.zip
      - unzip terraform_"$TERRAFORM_VERSION"_linux_amd64.zip
      - mv terraform /usr/local/bin/
      - echo "Install Terraspace"
      - bundle install
      - rbenv rehash
  pre_build:
    commands:
      - echo "Setting up git credentials"
      - git config --global credential.helper 'store'
      - echo "https://$GitHubUser:$GithubToken@github.com" > ~/.git-credentials
      - echo "Configure AWS Credentials"
      - export CREDENTIALS=`curl 169.254.170.2$AWS_CONTAINER_CREDENTIALS_RELATIVE_URI`
      - export AWS_ACCESS_KEY_ID=$(echo "${CREDENTIALS}" | jq -r '.AccessKeyId')
      - export AWS_SECRET_ACCESS_KEY=$(echo "${CREDENTIALS}" | jq -r '.SecretAccessKey')
      - export AWS_SESSION_TOKEN=$(echo "${CREDENTIALS}" | jq -r '.Token')
      - echo "Download Terraform Modules"
      - terraspace bundle
  build:
    commands:
      - echo "Deploy Masterlink VPC"
      - terraspace up vpc -y
#      - echo "Deploy Masterlink Redis Distributed Cache"
#      - terraspace up distributed-cache -y
#      - echo "Deploy Masterlink Auth Service"
#      - terraspace up auth-service -y
#      - echo "Deploy Masterlink Notification Service"
#      - terraspace up notification-service -y
#      - echo "Deploy Masterlink EventScheduler Service"
#      - terraspace up eventscheduler-service -y
#      - echo "Deploy Masterlink MSGraph Service"
#      - terraspace up msgraph-service -y
#      -  echo "Deploy Masterlink BI Service"
#      - terraspace up bi-service -y
#      - echo "Deploy Masterlink BPM Service"
#      - terraspace up bpm-service -y
#      - echo "Deploy Masterlink CMS Service"
#      - terraspace up cms-service -y
#      - echo "Deploy Masterlink Media Service"
#      - terraspace up media-service -y
#      - echo "Deploy Masterlink Services API Gateway"
#      - terraspace up api-gateway -y
#      - echo "Deploy Masterlink Factory"
#      - terraspace up factory-service -y
#      - echo "Deploy Masterlink Factory Operation BPM"
#      - terraspace up factory-bpm-operaton-service -y
```