# How to run chips on Google Cloud
**Author: Len Taing, Ming Tang, Aashna, Gali Bai**

**Last modified: 02/03/2021**

This documentation will guide you through the whole process of running chips pipeline on the google cloud platform.

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
      - click `Add new disk` or `Attach existing disk`
      - If Attaching existing disk: select chips-test 200GB
      - If Add new disk: scroll down to size, and you need to try to predict
      how much space you need for your analysis, e.g. 2T = 2048.

8. Scroll to the bottom, click `Create`.

9. NOW you will be brought back to the Google Compute Engine page:

   - click on your new instance when it is up and copy the `External IP`
   - log in to your instance:

### Step1. Log in to your instance

**a. If first time logging in:**

1. Install a google cloud conda environment

```bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# Add and update channels
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge

```

```bash
conda install mamba -c conda-forge
mamba create -n gcloud -c conda-forge google-cloud-sdk

```


```bash
conda activate gcloud
gcloud compute ssh chips-test-stanford
```

WHERE: `chips-test-stanford` is the instance name.

NOTE: this creates `~/.ssh/google_compute_engine` and `~/.ssh/google_compute._engine.pub`

**b. For old users:**
After `~/.ssh/google_compute_engine` and `~/.ssh/google_compute._engine.pub` is created, you can use ssh to log in instead:

```bash
ssh -i [your google-cloud-engine key] [your username]@[external IP]
e.g.
ssh -i ~/.ssh/google_compute_engine galib@XX.YY.ZZ.AA
```


### Step2. Formatting and mounting the second drive
1. Check the path of second drive: the additional disk you assigned when spinning up the disk:

```bash    
sudo lsblk
```

Then look for the drive name, e.g. `/dev/sdb`, that corresponds to the  space that you called for.  In most cases, it's `/dev/sdb`.

2. Format the second drive:

**VERY IMPORTANT:**

**Skip this step if using an old existing disk**

you only have to format a drive IF it is newly created.  IF you are using a drive that was created from before, FORMATTING will delete everything you had from
before. So only do this once in the life of a disk!


```bash
sudo /home/taing/utils/formatDisk.sh [drive part, e.g. 'sdb' from 2a]
e.g.
sudo /home/tain/utils/formatDisk.sh sdb
```

3. mount the drive:

```bash
sudo mkdir /mnt/ssd
```

```bash
sudo /home/taing/utils/mountDrv.sh [drive part, e.g. 'sdb'] /mnt/[mount point from above]
e.g.
sudo /home/taing/utils/mountDrv.sh sdb /mnt/ssd
```

This will mount `/dev/sdb` to `/mnt/ssd`

4. create a directory you can use on `/mnt/ssd`:

```bash
sudo mkdir /mnt/ssd/[username]
sudo chown [username]:[username] /mnt/ssd/[username]
E.g.
sudo mkdir /mnt/ssd/galib
sudo chown galib:galib /mnt/ssd/galib
```

NOW you can read and write files to `/mnt/ssd/galib` without being sudo.

5. REDIRECT `/tmp`

```bash
cd /mnt/ssd #or where your newly created disk is
sudo mkdir tmp
sudo chmod a+w tmp
sudo chmod a+r tmp
```

```bash
cd /
sudo mv tmp/ tmp.bak
sudo ln -s /mnt/ssd/tmp
```



### Step3. Chips Run

```bash
cd mnt/ssd/galib/
git clone git@bitbucket.org:plumbers/cidc_chips.git
```

You can follow the documentation on cidc_chips to set up the chips working directory.

```bash
source /home/taing/miniconda3/bin/activate chips
snakemake -s cidc_chips/chips.snakefile -j 16 -np
nohup snakemake -s cidc_chips/chips.snakefile -j 16 &
```
