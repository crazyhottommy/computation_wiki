
Thanks Len for writing the instructions!

### clone the chips automator repository:

```bash
git clone git@bitbucket.org:plumbers/chips_automator.git
#NOTE: you only need to do this step once
```

### build the chips_automator conda environment and activate it:
   
```bash
cd chips_automator
conda env create -f chips_automator_env.yml
source activate chips_automator
#NOTE: you only need to do this step once
```

### Authenticate/get copy of google cloud key:

```bash
gcloud auth application-default login

#And follow the directions
#NOTE: if successful, there should be a new file called ~/.ssh/google_cloud_engine
#NOTE: you only need to do this step once
```

By default, it is authenticated using Cloud SDK.

To follow this, has to use a [ssh authentication](https://cloud.google.com/source-repositories/docs/authentication#ssh).
Go to https://source.cloud.google.com/user/ssh_keys and copy your public key `~/.ssh/id_rsa.pub` to register the key.

### Create a chips_automator run configuration file:

a. copy over template:
NOTE: in this example we're going to name our chips_automator conf file `test.config.yaml`

b. Edit test.config.yaml:

- instance_name:
      	 define the instance name- any arbitrary string but cannon contain '.'
- cores:
      	 define the number of cores for the instance--default is 32
- disk_size: define the size of the attached disk
- google_bucket_path: 
      	 define the google bucket path to store the results when run is complete
- (optional) chips_commit: define the exact chips commit version to use
- samples: 
      	 define the sample names and the google bucket paths to their fastqs
-  meta: group samples into Treat/Control (i.e. input)

### Run chips automator:

```bash
./chips_automator.py -c test.config.yaml -u [your google cloud username--usually your hostname] -k ~/.ssh/google_cloud_enging
```


Chips automator should run successfully, it will print diagnostic messages  until it finishes.  If you encounter an error, please send the output of 
chips automator to Len. The last output should be the ip addr of the instance.

### Log into the instance to check on the run:

```bash
ssh -i ~/.ssh/google_cloud_engine [username]@[ip addr from last line of chips_automator]
cd /mnt/ssd/chips
check the state of the chips run by looking at /mnt/ssd/chips/nohup.out
```

**When the run is complete or stops at an error state, don't forget to delete the instance.**
   
      
