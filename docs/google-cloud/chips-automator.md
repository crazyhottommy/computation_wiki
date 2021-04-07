
# How to run chips automator
**Author: Len Taing, Gali Bai**

**Pre-requisites:**

1. CIDC Google Cloud account- email James Lindsay
2. software:
   a. git- read https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
   b. miniconda3- see how to install miniconda3 below in the appendix


### PART 0. Set up chips automator conda environment

1. clone the chips automator repository:

```bash
git clone git@bitbucket.org:plumbers/chips_automator.git
```
NOTE: you only need to do this step once


2. build the chips_automator conda environment and activate it:

```bash
cd chips_automator
conda env create -f chips_automator_env.yml
source activate chips_automator
```
NOTE: you only need to do this step once


### PART 1. Obtaining your Google Cloud Account login key


1. Install the google cloud sdk:

```bash
conda install -c conda-forge google-cloud-sdk
```

2. Authenticate your google cloud account:

```bash
gcloud auth application-default login

#And follow the directions
```

NOTE: if successful, there should be a new file called ~/.ssh/google_cloud_engine
NOTE: you only need to do this step once

   a. Copy and paste the link into a browser
   b. Select your google account
   c. Click "Allow"
   d. Copy the verification code and paste it into the terminal prompt

3. Set the project:

```bash
gcloud config set project cidc-biofx
```

4. Generate your key by logging into an existing google cloud instance:

  a. create an instance:

```bash
gcloud compute instances createå chips-test-del --machine-type n2-standard-2 --image chips-ver1-7c --service-account biofxvm@cidc-biofx.iam.gserviceaccount.com --scopes https://www.googleapis.com/auth/devstorage.read_write,https://www.googleapis.com/auth/logging.write --zone us-east1-b

```
NOTE: this should return something like this:

"""
Created [https://www.googleapis.com/compute/v1/projects/cidc-biofx/zones/us-east1-b/instances/chips-test-del].
NAME          ZONE        MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
chips-test-del  us-east1-b  n2-standard-2               10.142.0.29  35.243.249.170  RUNNING
"""

  b. Log into instance:

```bash
gcloud compute ssh chips-test-del
#hit Enter and DO NOT enture a passphrase; then Enter again
```
  c. generate ssh-keys:

```bash
ssh-keygen
#hit Enter and DO NOT enture a passphrase; then Enter again
```

  d. Log out of instance: hit CTRL-D
  e. download your ssh keys:
     $ cd ~/Downloads
     $ scp -i ~/.ssh/google_compute_engine [your username]@[ip addr]:~/.ssh/id_rsa* .
  f. **EMAIL Len your ssh keys; attach ~/Download/ida_rsa and ida_rsa.pub in
     the email

***Before moving on, make sure that Len has added your ssh keys to the chips
image; Only proceed if the keys have been added!***

### PART 2. Running chips_automator.py

In this section we're going to run the test example that is found in the
chips_automator source code. It will create an instance called
'chips-auto-fast-test'.

1. In the chips_automator source code directory, you will find a file called
   'test.config.yaml'
  a. copy over template:
  NOTE: in this example we're going to name our chips_automator conf file `test.config.yaml`

  b. Edit test.config.yaml:

  - instance_name:
      define the instance name- any arbitrary string but cannon contain '.'
  - cores:
      define the number of cores for the instance--default is 32
  - disk_size:
      define the size of the attached disk
  - google_bucket_path:
      define the google bucket path to store the results when run is complete
  - chips_ref_snapshot:
      define which version of chips reference snapshot to use
  - (optional) chips_commit:
      define the exact chips commit version to use
  - samples:
      define the sample names and the google bucket paths to their fastqs
  -  metasheet:
      define run name and replicates

2. Run chips automator:

```bash
./chips_automator.py -c test.config.yaml -u [your google cloud username--usually your hostname] -k ~/.ssh/google_cloud_enging

E.g. ./chips_automator.py -c test.config.yaml -u galib -k ~/.ssh/google_cloud_enging

#chips automator should run successfully, it will print diagnostic messages  until it finishes.  If you encounter an error, please send the output of
#chips automator to Len.
```
NOTE: -c is where you specify the chips automator config file


The final line should look like this-

"""
The instance is running at the following IP: 35.243.249.170
please log into this instance and to check-in on the run
"""

***COPY the IP address for use in the next step***

3. Log into the instance to check on the run:

```bash
ssh -i ~/.ssh/google_cloud_engine [username]@[ip addr from last line of chips_automator]
cd /mnt/ssd/chips
check the state of the chips run by looking at /mnt/ssd/chips/nohup.out
```

### PART 3. WHEN the run is complete:
NOTE: this is a VERY important part of the process, otherwise the instance
is wasting money
1. Delete the instance:
   a. goto: https://console.cloud.google.com/compute/instances?organizationId=636937865278&project=cidc-biofx&instancessize=50
   b. select "chips-auto-fast-test" instance and then click the trash can
      icon at the top of the screen
