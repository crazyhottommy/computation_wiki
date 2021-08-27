# How to run chips on Amazon Web Services
**Author: Len Taing, Gali Bai**

**Last modified: 04/30/2021**

This documentation will guide you through the whole process of running chips pipeline on the amazon web services.

### Step0. Before you begin

1. Goto https://895602348532.signin.aws.amazon.com/console

2. Use your credentials to access your account (Email Len to get access).

### Step1. Launch an AWS instance

1. Make sure your region is set to `N.viginia`.

2. In `Services`, select `EC2`.

3. Click `instances` in the side-bar, then click `Launch instances` on the right top.

4. Choose an AMI: Click My AMIs,  and then choose an image.
NOTE: the latest chips image is chips-ver1.6.

5. Choose an Instance Type: choose the machine type that you would like to use. `m4.16xlarge` is used for chips.

6. Configure Instance Details:
  - Purchasing option: `Resquest Spot instance`
  - Persistent request: `Persistent request`
  - Interruption behavior:: `Stop` ( This will stop the instance instead of terminating it)
  - Subnet: `us-east-1b`

7. Add Storage: You almost always want to add another disk because the root disk is not large enough to run any analysis.
  - Volume Type: `EBS`
  - Devices: `/dev/sdb`
  - Size: `250`
  - Deletion on Termination: Both `yes`

8. Click through Tags and goto Configure Security Group:
  - `select an existing security group`
  - select `launch-wizard-1`

9. Review and Launch:
  - click `Launch`
  - `choose an existing key pair`
  - select a key pair: `liulab_aws.pem`(Email Len if you did not receive it)

### Step2. Logging into the instance

1. Go back to the instance page, and select the instance you just created.

2. Copy the IPv4 Public Address

3. To log into the instance:

```bash
ssh -i [path to]/liulab_aws.pem ubuntu@[IPv4 Public Address]

e.g.
ssh -i ~/.ssh/liulab_aws.pem ubuntu@3.45.127.45

NOTE: all users must use the “ubuntu” user account to log into these instances
```

### Step3. Setup the instance

1. Format the EBS derive
  - find the device name.
  - Format

```bash

lsblk

~/utils/formatDisk.sh xvdb

Note:  may take a few seconds.

```

2. Mount the EBS derive

```bash
sudo ~/utils/mountDrv.sh /mnt/ssh
```

3. Setup /tmp

```bash
sudo mkdir /mnt/ssd/tmp
sudo chmod 777 /mnt/ssd/tmp
cd /
sudo mv tmp tmp.bak
sudo ln -s /mnt/ssd/tmp
```

4. Setup Sentieon

```bash
~/utils/sentieonDiagnostics.sh
```

Note: If you see the following, then sentieon is OK: 
3200 
The Sentieon license server is granting requests.

Otherwise: follow the instruction below

  - restart the sentieon license server

  ```bash
  #Log into the sentieon license server
  ssh -i ~/.ssh/liulab_aws.pem ubuntu@3.80.65.228

  rm sentieon_licsrv.log

  ./sentieon-genomics-201808.05/bin/sentieon licsrvr --start -l sentieon_licsrv.log ~/Harvard_Liu_aws_cluster.lic
  ```

  - Try running sentieon again on your instance and you should get a success message

  ```bash
  ~/utils/sentieonDiagnostics.sh

  ```

### Step 4. Setup the Chips run

1. Create chips project dir

```bash
cd /mnt/ssd/
mkdir chipsTest
chipSetup.sh
```

NOTE: chipsSetup.sh will clone the cidc_chips repo (from plumbers), copy over the config.yaml, metasheet.csv, and link the reference files stored on /mnt/liulab-efs.

2. You will need to upload your data and edit config.yaml and metasheet.csv accordingly.

3. Run chips

```bash
source activate chips
#Do a dry-run: 
snakemake -s cidc_chips/chips.snakefile -n

#IF no errors, then run: 
nohup time snakemake -s cidc_chips/chips.snakefile -j 64 > nohup.out &
```
