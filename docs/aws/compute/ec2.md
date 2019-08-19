# AMI

## Create

* **EC2 EBS Backed**


    1. Launch an instance
    2. Connect to your instance and customize it
    3. Stop Instances (stopping the instance ensures data integrity of the instance; a snapshot will be taken of the instance)
    4. Create the image 
    5. Bundle it (use bundle command in console) 
    6. AWS automatically registers it for you  

  
* **EC2 Instance store**

    1. Launch an instance
    2. Connect to your instance and customize it
    3. Bundle it (Consists of an image manifest -image.manifest.xml- and files -image.part.xx-) 
    4. Upload the bundle to S3 bucket
    5. Register AMI

# Snapshots

## Restore

**Read all blocks to eliminate penalty in production**

```sh
sudo dd if=/dev/xvdf of=/dev/null bs=1M
# Or
sudo fio –filename=/dev/xvdf –rw=read –bs=128k –iodepth=32 –ioengine=libaio –direct=1 –name=volume-initialize
```

# Security

## Proxy SSH

[Link](https://heipei.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful/)

# Troubleshooting

## Repair Without Console

[How To Repair An AWS EC2 Instance Without Console](https://www.rootusers.com/how-to-repair-an-aws-ec2-instance-without-console/)

[Duplicate XFS UUIDs.](https://serverfault.com/questions/948408/mount-wrong-fs-type-bad-option-bad-superblock-on-dev-xvdf1-missing-codepage)