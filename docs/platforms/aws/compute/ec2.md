## Restore Snapshots

**Read all blocks to eliminate penalty in production**

```bash
sudo dd if=/dev/xvdf of=/dev/null bs=1M
# Or
sudo fio –filename=/dev/xvdf –rw=read –bs=128k –iodepth=32 –ioengine=libaio –direct=1 –name=volume-initialize
```

## Configure SSH Config

[Proxy SSH](https://heipei.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful/)

## Recover Instance 

**Recover Instance without console access**

[How To Repair An AWS EC2 Instance Without Console](https://www.rootusers.com/how-to-repair-an-aws-ec2-instance-without-console/)

[Duplicate XFS UUIDs](https://serverfault.com/questions/948408/mount-wrong-fs-type-bad-option-bad-superblock-on-dev-xvdf1-missing-codepage)