### PART 2.0.c Running RIMA_automator.py
Please refer to the PART 1 of the automator documentation before proceeding to this section.

This section is designed to help you understand how to run the RIMA automator. Using the "rima_automator" source code, we will submit a run and the automator will create an instance named "rima-auto-fast-test".  Please note that this is a test for the sake of this tutorial.  If you are running clinical trial data, you will upload config.yaml sheets for your sample and there will be additional steps at the end before you delete this instance.

***Before moving on, make sure that Aashna has added your ssh keys to the chips image; Only proceed if the keys have been added.***

NOTE: These steps are completed on the automator instance created in Part 1.  

0. Log into your automator instance:
```bash
gcloud compute ssh --tunnel-through-iap <username>@{username}-automator-instance:~/.ssh/
```


1. Clone the chips_automator repository (if you don’t have a copy already):

``` bash
cd rima_automator
git clone git@bitbucket:plumbers/rima_automator.git
```
#Note: if you cloned the rima_automator repository previously, check if there are any updates:

``` bash
cd rima_automator
git pull git@bitbucket.org:plumbers/rima_automator
```


2. Check the config.yaml file.  
In your local rima_automator source code directory, you will find a file called 'test.config.yaml'.  This file can be directly used for the test run without any modifications.
For actual trial runs, config.yaml files should be generated with all of the correct parameters.  However, if this is not the case, “test.config.yaml” can be used as a template to create the actual config file for that particular case.

Parameters that are unique to each rima run, include the following:
    - instance_name:
        define the instance name- any arbitrary string but cannot contain '.'
    - cores:
        define the number of cores for the instance--default is 32
    - disk_size:
        define the size of the attached disk
    - google_bucket_path:
        define the google bucket path to store the results when run is complete
    - rima_ref_snapshot:
        define which version of rima reference snapshot to use. Currently , we have two versions: v22 and v27
    - (optional) rima_commit:
        define the exact rima commit version to use
    - samples:
        define the sample names and the google bucket paths to their fastqs
    -  metasheet:
        define sample name and patient name.


3. Run RIMA automator:
    (activate the automator environment if you have not done so already)
    ```bash
    source activate automator
    ```

    run rima_automator:
    ```bash
    ./rima_automator.py -c test.config.yaml -u [your google cloud username--usually your hostname] -k ~/.ssh/google_compute_engine
    E.g. ./rima_automator.py -c test.config.yaml -u sal26 -k ~/.ssh/google_compute_engine
    #rima automator should run successfully, it will print diagnostic messages  until it finishes.
    #If you encounter an error, please send the output of rima automator to Aashna.
    ```
    NOTE: -c is where you specify the rima automator config file.
    NOTE: it may take a few minutes to finish, and you'll see lots of print outs.
    The final line should look like this-

    """
    The instance rima-auto-fast-test is running at the following IP: {IP address}
    please log into this instance and to check-in on the run
    """

***Use the instance name, rima-auto-fast-test to check for the instance name on the GCP console for use in the next step. Note that users can change the instance name in the test.config.yaml***


4. Check on the run:
    Users need to login to the instance to check on the runs.
        1. Log into the instance:
          ```bash
          gcloud compute ssh --tunnel-through-iap [username]@rima-auto-fast-test
          cd /mnt/ssd/rima
          ```

        2. run top:
          ```bash
          top
          ```
          - wait until the first item is 'dockerd' or 'containerd' or 'top'.
          - If you see these, then you know the run is done.

###Note: If you are running rima_automator with actual clinical trial data, then transfer the version info using the transfer script provided in the VM image to the google bucket. Also, follow the ingestion documentation before proceeding to the below section.


### PART 3. WHEN the run is complete:
NOTE: This is a VERY important part of the process, otherwise the instance is wasting money after the runs are done.
  1. Delete the instance:
      a. goto: https://console.cloud.google.com/compute/instances?organizationId=636937865278&project=cidc-biofx&instancessize=50
      b. select "rima-auto-fast-test" instance and then click the 'trash can' icon at the top of the screen
