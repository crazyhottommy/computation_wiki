# update chips image

Thanks Len for the instructions! There is a `chips-ver1-4` image in the google cloud set up by Len.
The `Snakemake` running `conda` environment is ready to use. I modified the `chips` ATACseq pipeline and 
added `fastp` step for trimming the adaptors and low quality reads. I will need to update the conda
environment and make a new image.


### spin up an instance and use the `chips-1-4` as booting disk.

1. log into google cloud platform. click `VM instanes` --> create instance --> click `change` in `Boot disk`
and select --> `Custom images` --> choose `chips-ver1-4` in the dropdown.


2. ssh into the VM instance 

You can become any other user on GCP by typing 

```bash
$ sudo -su [username]
```

For example you can become  user `taing` and edit the conda environment like this:

1. switch user- 

```bash
sudo -su taing
```
2. Source taing's miniconda environment-

make the following `taing_env.bash`

```bash
export CONDA_ROOT=/home/taing/miniconda3
export CONDA_ENVS_PATH=$CONDA_ROOT/envs
export PATH=$CONDA_ENVS_PATH/chips/bin:$CONDA_ROOT/bin:$PATH
```


```bash
source taing_env.bash 
```

install `fastp`

```bash
conda activate chips
conda install fastp -c bioconda
```

### creating a new image 

Thanks Aashna for the instruction.

1. STOP the instance (STOP, do not *delete*)
2. Go to Disks
3. Select the root disk that you want to make an image, i.e "chips-ver1-4" which is 20GB)--click on it an it should bring you to the disk page
4. on the disk page, select "Create Image" from the top
Here are the fields you will have to fill:
name: name of the image, i.e. rnaseq-ver1-0
source: (do not change it) it should say "disk"
source disk: (do not change it) is should say "rnaseq-data"
location: select regional, and then select us-east1 (south carolina)
family: rnaseq
description: [add a brief description]
click add label, then add two labels:
key: group value: plumbers
key: pipeline value: rnaseq

then click "Create" (at the bottom)

change back to your own user for running the `chips`.

```bash
sudo -su mtang
conda activate chips
```






