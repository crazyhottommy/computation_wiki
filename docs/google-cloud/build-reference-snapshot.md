# How to build reference snapshot
**Author: Gali Bai**
**Last Modified: 2021-04-01**

### Step0. Spin up an instance and use the `chips-ver1-6` as the boot disk.

1. Log in to the [google cloud platform](https://console.cloud.google.com/):
      - In the side bar, click `VM instanes`.

2. On the top, click `Create Instance`.

3. Give your instance type an unique `Name`, e.g. chips-test-stanford. Choose `Region` as `us-east1`.

4. Select `Machine type`: `e2-standard-32`.

5. Boot disk:
      - click `Change`
      - click `Custome Images` (next to Application Images).
      - select the latest chips image: `chips-ver1-6`

6. Firewall:
      - Allow HTTP traffic
      - Allow HTTPs traffic

7. Click `Management, security, disks, networking, sole tenancy`:
      - click `Disks`
      - click `Add new disk`
      - Add new disk for working directory: scroll down to size, and you need to try to predict
      how much space you need for your analysis, e.g. 2T = 2048.
      - Add new disk for reference: scroll down to size, and here you need to refer how large
      your reference files are, e.g. chips ref_files is 160G so I assigned 200G.

8. Scroll to the bottom, click `Create`.

9. NOW you will be brought back to the Google Compute Engine page:

   - click on your new instance when it is up and copy the `External IP`
   - log in to your instance:

```bash
ssh -i ~/.ssh/google_cloud_engine galib@`External IP`

```

### Step1. Mount Additional disks

1. Format and mount the disks:

```bash
sudo lsblk

#Here you will see two disks sdb and sdc unmounted. sdb is the first disk attached, which is for running chips. sdc is the second attached disk, we will store reference file here later.
```

```bash
cd /mnt
sudo mkdir chips_refs
sudo /home/tain/utils/formatDisk.sh sdc
sudo /home/taing/utils/mountDrv.sh sdc /mnt/chip_refs/
```

2. Set up reference directory:

Copy the most latest version of refernce file you are using to /mnt/chip_refs/

```bash
sudo cp -r ~/ref_files /mnt/chip_refs/

#Since we mounted disk sdc to /mnt/chip_refs/, here we are actually copying the reference file to the second disk.
```

4. Log out the instance: hit CTRL-D and stop the instance (do not *delete*).

5. Go to `Storage` -> `Disks`.

6. Select the boot disk that you stored the reference file.

4. On the disk page, select `Create Snapshot` from the top
Here are the fields you will have to fill:
    - name: name of the image, i.e. chips-ref-ver1-0
    - Description: briefly summarize the updates of current reference version
    - source: (do not change it) it should say "disk"
    - source disk: (do not change it)
    - location: select regional, and then select us-east1 (South Carolina)
    - description: [add a brief description of what you have added/changed]
    - click add label, then add two labels:
        - `key: group`, `value: plumbers`
        - `key: pipeline`, `value: chips`
    - then click "Create" (at the bottom)
