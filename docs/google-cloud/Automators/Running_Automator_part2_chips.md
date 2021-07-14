### PART 2.0.b Running CHIPS_automator.py
Please refer to the PART 1 of the automator documentation before proceeding to this section.


This section is designed to help you understand how to run the CHIPS automator. Using the "chips_automator" source code, we will submit a run and the automator will create an instance named "chips-auto-fast-test".  Please note that this is a test for the sake of this tutorial.  If you are running clinical trial data, you will upload config.yaml sheets for your sample and there will be additional steps at the end before you delete this instance.

***Before moving on, make sure that Gali has added your ssh keys to the chips image; Only proceed if the keys have been added.***

NOTE: These steps are completed on the automator instance created in Part 1.  

0. Log into your automator instance:
```bash
gcloud compute ssh <username>@{username}-automator-instance:~/.ssh/
```


1. Clone the chips_automator repository (if you don’t have a copy already):
``` bash
cd chips_automator
git clone git@bitbucket:plumbers/chips_automator
```
#Note: if you cloned the chips_automator repository previously, check if there are any updates:

``` bash
cd chips_automator
git pull git@bitbucket.org:plumbers/chips_automator
```


2. Check the config.yaml file.  
In your local chips_automator source code directory, you will find a file called 'test.config.yaml'.  This file can be directly used for the test run without any modifications.
For actual trial runs, config.yaml files should be generated with all of the correct parameters.  However, if this is not the case, “test.config.yaml” can be used as a template to create the actual config file for that particular case.

Parameters that are unique to each chips run, include the following:
    - instance_name:
        define the instance name- any arbitrary string but cannot contain '.'
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
        define the sample names and the google bucket paths to their fastqs/bams.
    -  metasheet:
        define run name and corresponding treatment and control sample names.


3. Run chips automator:
    (activate the automator environment if you have not done so already)
    ```bash
    source activate automator
    ```

    run chips_automator:
    ```bash
    ./chips_automator.py -c test.config.yaml -u [your google cloud username--usually your hostname] -k ~/.ssh/google_compute_engine
    E.g. ./chips_automator.py -c test.config.yaml -u sal26 -k ~/.ssh/google_compute_engine
    #chips automator should run successfully, it will print diagnostic messages  until it finishes.
    #If you encounter an error, please send the output of chips automator to Gali.
    ```
    NOTE: -c is where you specify the chips automator config file.
    NOTE: it may take a few minutes to finish, and you'll see lots of print outs.
    The final line should look like this-

    """
    The instance chips-auto-fast-test is running at the following IP: {IP address}
    please log into this instance and to check-in on the run
    """

***Use the instance name, chips-auto-fast-test to check for the instance name on the GCP console for use in the next step. Note that users can change the instance name in the test.config.yaml***

4. Check on the run:
    Users need to login to the instance to check on the runs.
        1. Log into the instance:
          ```bash
          gcloud compute ssh  [username]@chips-auto-fast-test
          cd /mnt/ssd/chips
          ```
        2. run top:
          ```bash
          top
          ```
          - wait until the first item is 'dockerd' or 'containerd' or 'top'
          - if you see these, then you know the run is done


###Note: If you are running chips_automator with actual clinical trial data, then transfer the version info using the transfer script provided in the VM image to the google bucket. Also, follow the ingestion documentation before proceeding to the below section.

### PART 3. WHEN the run is complete:
NOTE: This is a VERY important part of the process, otherwise the instance is wasting money after the runs are done.
  1. Delete the instance:
      a. goto: https://console.cloud.google.com/compute/instances?organizationId=636937865278&project=cidc-biofx&instancessize=50
      b. select "chips-auto-fast-test" instance and then click the trash can
          icon at the top of the screen
