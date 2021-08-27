# Expand root partition
**Author: Gali Bai, Len Tiang, Aashna Jhaveri**
**Last Modified: 2021-Aug-05 Aashna Jhaveri**

If the user runs out of space while using the instance, then this tutorial can be used to expand the partition system for more space.


### To expand the root partition:

1. On the GCP webpage, click Disks (Sidebar) and click the disk of the instance--usually named the same as the instance
2. Click the edit icon and change the size (in GB) to the desired size.
3. on the instance:

```bash

# and look for the device name, usually sda1
$ sudo lsblk

# Grow the  partition:
$ sudo growpart /dev/sda1

#resize the fs:
$ sudo resize2fs /dev/sda1

# to ensure everything worked
$ df -h
```
