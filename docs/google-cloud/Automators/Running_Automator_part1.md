# Introduction
The Running Automator documention is divided into 3 main parts:

	Part 0 = generating access credentials.
	Part 1 = creating a general automator instance from which to launch WES/RIMA/CHIPS jobs.
	Part 2_* = running a specific pipeline's automator.  Part 2 is actually three separate documents -- one for each pipeline.  (Part2_wes, Part2_rima, Part2_chips)

If you have not had your access credentials added to the pipeline that you wish to run, please make sure you have completed Part 0 prior to this section.  

## Step 1. Create an automator instance from which to launch WES/RIMA/Chips jobs.

```bash
gcloud compute instances create {username}-automator-instance --machine-type n2-standard-2  --image-family=automator  --service-account biofxvm@cidc-biofx.iam.gserviceaccount.com --scopes https://www.googleapis.com/auth/devstorage.read_write,https://www.googleapis.com/auth/logging.write --zone us-east1-b
```

Upload your ~/.ssh/google_compute_engine key to the automator instance.
```bash
gcloud compute scp ~/.ssh/google_cloud_engine <username>@{username}-automator-instance:~/.ssh/
```

## Step 2. The environment has already been added onto the automator VM image.  Activate it to check that is working:

```bash
export CONDA_ROOT=/home/aashna/miniconda3
export PATH=/home/aashna/miniconda3/bin:$PATH
source activate automator
```
#Note: If the environment on the automator image doesn’t work or it needs to be updated, each pipeline has a common environment yml file in their respective bitbucket repository.  You can use this automator_env.yml file to build a new environment.
