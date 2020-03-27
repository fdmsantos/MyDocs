# VMWare

## VM Import

[AWS Link](https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html)

### Create Ova File

* Install ovf tool

[ovftool](https://github.com/djui/docker-ovftool)

* Build the image with the new version bundle file downloaded from VMWare site

```bash
docker run --rm -it -v $(pwd):/tmp ovftool --noSSLVerify vi://<user>@<ip>/LAB/vm/aws /tmp/aws.ova
```

### AWS PreRequisites

* Create vmimport role

    * [Documentation](https://docs.aws.amazon.com/vm-import/latest/userguide/vmie_prereqs.html#vmimport-role)
    * The documentation has a error in trust policy "sts:Externalid": "vmimport" should be "sts:ExternalId": "vmimport"
        * [Error explain](https://forums.aws.amazon.com/thread.jspa?threadID=179090)
 
* Upload to S3

    * Use Multipart Upload 

[MultiPart Upload](../storage/s3.md)

### Import Image

```bash
aws ec2 import-image --description "My server VM" --disk-containers "file://containers.json" --region eu-west-1

# Monitor
aws ec2 describe-import-image-tasks --import-task-ids ${TASK_ID} --region eu-west-1
```

* Containers.json file

```json
[
    {
      "Description": "My Server OVA",
      "Format": "ova",
      "UserBucket": {
          "S3Bucket": "fsantos-vmware-ova-templates",
          "S3Key": "aws.ova"
      }
}]
```
