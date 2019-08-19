# Install

```bash
wget https://releases.hashicorp.com/packer/1.4.2/packer_1.4.2_linux_amd64.zip
unzip packer_1.4.2_linux_amd64.zip
rm packer_1.4.2_linux_amd64.zip
sudo mv packer /usr/bin/
packer -v
```

# Template

* **packer.json**

## AMI

```json
{ 
  "variables": { 
    "subnet_id": "", 
    "instance_size": "t2.micro", 
    "ami_name": "bastion", 
    "ssh_username": "ec2-user" 
  }, 
  "builders": [ 
    { 
      "type": "amazon-ebs", 
      "instance_type": "{{user `instance_size`}}", 
      "ssh_username": "{{user `ssh_username`}}", 
      "ssh_timeout": "20m", 
      "ssh_pty": "true", 
      "ami_name": "{{user `ami_name`}}", 
      "subnet_id": "{{user `subnet_id`}}", 
      "source_ami_filter": { 
        "filters": { 
          "virtualization-type": "hvm", 
          "name": "amzn2-ami-hvm-2.0.*-x86_64-gp2*", 
          "root-device-type": "ebs" 
        }, 
        "owners": ["amazon"], 
        "most_recent": true 
      }, 
      "tags": { 
        "Name": "{{user `ami_name`}}", 
        "BuiltBy": "Packer" 
      } 
    } 
  ], 
  "description": "AWS Bastion AMI", 
  "provisioners": [ 
    { 
      "type": "shell", 
      "inline": [ 
        "sudo yum update -y", 
        "sudo hostnamectl set-hostname bastion" 
      ] 
    } 
  ] 
} 
```

## Docker

```json
{
  "variables": {
    "repository": "la/express",
    "tag": "0.1.0"
  },
  "builders": [
    {
        "type": "docker",
        "author": "Fabio Santos",
        "image": "node",
        "commit": "true",
        "changes": [
          "EXPOSE 3000"
        ]
    }
  ],
  "provisioners": [
      {
        "type": "shell",
        "inline": [
          "apt-get update -y && apt-get install curl -y",
          "mkdir -p /var/code",
          "cd /root",
          "curl -L https://github.com/linuxacademy/content-nodejs-hello-world/archive/v1.0.tar.gz -o code.tar.gz",
          "tar zxvf code.tar.gz -C /var/code --strip-components=1",
          "cd /var/code",
          "npm install"
        ]
      }
  ],
  "post-processors": [
    {
      "type": "docker-tag",
      "repository": "{{user `repository`}}",
      "tag": "{{user `tag`}}"
    }
  ]
}
```

# Commands

```bash
packer validate packer.json
packer build -var 'tag=0.0.1' -var 'another=212' packer.json

# Not Tested it
packer fix
packer validate
```
