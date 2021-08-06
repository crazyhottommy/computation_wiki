###Frequently Asked Questions

**Last Updated: 2021-Aug-05 Aashna Jhaveri**

#### Q1. How to update conda environment under /home/taing/miniconda?

You can become any other user on GCP by typing:

```bash
$ sudo -su [username]
```

For example you can become  user `taing` and edit the conda environment like this:

1. switch user

```bash
sudo -su taing
```
2. Source taing's miniconda environment

make the following `taing_env.bash`

```bash
touch taing_env.bash
nano taing_env.bash

```

Copy the following lines in taing_env.bash

```bash
export CONDA_ROOT=/home/taing/miniconda3
export CONDA_ENVS_PATH=$CONDA_ROOT/envs
export PATH=/home/taing/miniconda3/bin:$PATH
unset PYTHONPATH
export HOME=/home/taing
```


```bash
source taing_env.bash
```
3. Add tools

E.g. You want to add fastp in current conda env:

install `fastp`

```bash
conda activate chips
conda install fastp -c bioconda
```

Ctrl + D will switch back to your own user.
**Remember to create a new image based on updated instance, otherwise your update will not be saved.**


#### Q2. Automator: Sending command `gsutil` remotely from your computer to the instance causing error message `bash: gsutil: command not found`?

1. Log in to the instance
```bash
$ which gsutil
# if there is no path under /usr/local/bin:
$ ln -s ~/gsutil /usr/local/bin/
```
2. Create a new image for the current updated instance.

#### Q3. Permission denied for running sentieon with error messages `/home/taing/utils/setup03_sentieonLic.sh: line 6: /home/taing/nohup.sentieon.out: Permission denied` or
`Failed to contact the license server at gcp.sentieon.com:9003`.

1. Go to /home/taing/sentieon

2. Switch user to taing.

```bash
sudo su taing
```

3. Open permissions to the sentieon and related files.

```bash
chmod 777 /home/taing/sentieon

```

4. Create a new image for the current updated instance.

#### Q4: Unable to resolve host error message  
Please follow the steps mentioned in 'Common ingestion errors' document.
