# Expand root partition

Sometimes you want to increase the root parition (booting disk) after you created an instance.
Len showed me how to do it.

I used his `chips-1-4` image as a base (20G in size), and want to increase the root partition size.

### To expand the root partition:

1. On the GCP webpage, click Disks (Sidebar) and click the root disk of the instance--usually named the same as the instance
2. Click the edit icon and change the size (in GB) to the desired size
3. on the instance:

```bash

# and look for the device name, usually sda1
$ lsblk 

# Grow the partition:
$ sudo growpart /dev/sda1

#resize the fs: 
$ sudo resize2fs /dev/sda1

# to ensure everything worked
$ df -h 
```