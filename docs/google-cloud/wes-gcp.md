# How to run WES pipeline in google cloud
*Authors: Ming (Tommy) Tang with input from Len Taing and Aashna Jhaveri*
*Last updated: 2021-Aug-06, Aashna Jhaveri and Jen Altreuter*

### how to get your google cloud credentials 
   (for more complete directions, please see "Getting Started with gcp")

   a. install the gcloud sdk (if you have not already done so.  It's on conda):

   ```bash
   conda install -c conda-forge google-cloud-sdk
   ```

   b. Authenticate:

   ```bash
   gcloud auth login
   ```

   NOTE: opens up a webbrowser, where I need to select the google account to allow access

   c. set project (if you have not done so previously):

   ```bash
   gcloud config set project cidc-biofx
   ```



###  CREATE the new instance:

a. Goto [google cloud platform](https://console.cloud.google.com/): Google Compute Engine -> click "Create Instance"

1. give your instance type a unique name. Please include your name in the instance name.  e.g. `wes-aashna-1`

2. Machine type: select `high-mem-64`  (this has 64 cores)

3. Boot disk: click "Change".
      - click "Custome Images" (next to Application Images).
      - select latest wes image-. AS OF 2021-05-08, it is `wes-ver2-25`

4. Click "Management, security, disks, networking, sole tenancy".
      - click Disks
      - click "Add new disk"
      - scroll down to size, and **HERE** you need to try to predict
      how much space you need to do your analysis e.g. 2T = 2048 GB (binary)

5. scroll to the bottom, click "Create"

NOW you will be brought back to the Google Compute Engine page

   - to start your instance, click on your instance and choose the start button at the top of the page.
   - login to your instance on the gcloud sdk (in your terminal window):

```bash
gcloud compute ssh --tunnel-through-iap wes-aashna-1
```

WHERE `wes-aashna-1` is the instance name.


Please read [Connecting to Linux instances](https://cloud.google.com/compute/docs/instances/connecting-to-instance)
and [Connecting to instances using advanced methods](https://cloud.google.com/compute/docs/instances/connecting-advanced#provide-key) for more details.

###  formatting and mounting the second drive-

After logging in, the next step is to mount and format the drive that you apportioned in step 1.a.4 so that you can use it.

a. finding the disk name: type

```bash    
sudo lsblk
```

Then look for the drive name, e.g. `/dev/sdb`, that corresponds to the  space that you called for.  In most cases, it's `/dev/sdb`


b. format the drive: type:

**VERY IMPORTANT-- PLEASE READ!!!!**
you only have to format a drive IF it is newly created.  IF you are using a drive that was created from before, FORMATTING will delete everything you had from
before.  SO only do this once in the life of a disk!

IF using an old disk, skip this step!


```bash
sudo /home/taing/utils/formatDisk.sh [drive part, e.g. 'sdb' from 2a]
e.g.
sudo /home/taing/utils/formatDisk.sh sdb
```

c. mount the drive:

make a mount directory:
EXAMPLE:

```bash
sudo mkdir /mnt/ssd
```


2. mount the drive:

```bash
sudo /home/taing/utils/mountDrv.sh [drive part, e.g. 'sdb'] /mnt/[mount point from above]
```

Example:

```bash
sudo /home/taing/utils/mountDrv.sh sdb /mnt/ssd
```

This will mount `/dev/sdb` to `/mnt/ssd`

d. create a directory you can use on `/mnt/ssd`:

```bash
sudo mkdir /mnt/ssd/[username]
sudo chown [username]:[username] /mnt/ssd/[username]
```

EXAMPLE:

```bash
sudo mkdir /mnt/ssd/aashna
sudo chown aashna:aashna /mnt/ssd/aashna
```

NOW you can read and write files to `/mnt/ssd/aashna` without being sudo

e. REDIRECT `/tmp`
NOTE: sometimes the `/tmp` directory can get full. To ensure this doesn't happen, I usually move /tmp off of the root partition


```bash
cd /mnt/ssd #or where your newly created disk is
sudo mkdir tmp
sudo chmod a+w tmp
sudo chmod a+r tmp
```

soft-link

```bash
cd /
sudo mv tmp/ tmp.bak
sudo ln -s /mnt/ssd/tmp
```


### Steps for WES setup
   f. run sentieon license:

```bash
cd /home/taing/utils/
nohup ./sentieonLicense.sh &
```

### how to setup a wes run: (in the directory created in 2d.)

change into your directory from 2d:

```bash
cd /mnt/ssd/[username]

# clone the wes repository:
git clone git@bitbucket.org:plumbers/cidc_wes.git

# create a data directory:
mkdir data
# upload your fastqs into data
```

copy out the config.yaml and metasheet.csv:

```bash
cp cidc_wes/config.yaml .
cp cidc_wes/metasheet.csv .
```

Edit the config.yaml to

1. fill the sentieon path which I believe is something: `/home/taing/sentieon/sentieon.../bin`
2. fill the  samples section- #ask Len or Aashna about this

Edit metasheet.csv to define the Normal/Tumor pairs

link the reference files:
The reference files include, for example, the bwa index and the genome's FASTA file.

```bash
ln -s /mnt/cidc_nfs/wes/ref_files
```

Now you are ready to run

### RUN wes:

```bash
source activate wes
```

Your cmd line should be pre-pended with (wes)
do a dry run to check for errors in config or metasheet

```bash
snakemake -s cidc_wes/wes.snakefile -n
```

If all is green you are good to go
If there are errors, fix them

FULL run:

```bash
nohup time snakemake -s cidc_wes/wes.snakefile -j 64 > nohup.out &
```
The 'nohup' allows you to log off.  
The 'time' will time the run for you.  
the -j 64 means to use 64.  Use whatever number you want.
