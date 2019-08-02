# Root Volumes

## Resize

* **Manual method**

    1. Create a snapshot of the current root volume
    2. Create a new volume from the snapshot with new storage specifications
    3. Select larger "size", thus increasing IOPS
    4. Must select the same availability zone as the source
    5. Stop the Instance
    6. Detach the original root volume
    7. Attach the new volume to the instance at same mount point (xvda)

* **"Automated" method**
    1. We can replace the launch configuration of an Auto Scaling Group
    2. In a true 3-tier application that is decoupled, we should then be able to terminate instances one by one to recreate them using new configuration

**Note:** If the size of the volume was modified, extend the volume's file system to take advantage of the increased storage capacity