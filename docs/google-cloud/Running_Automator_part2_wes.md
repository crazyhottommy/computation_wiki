### PART 2 Running WES_automator.py
Please refer to the PART 1 of the automator documentation before proceeding to this section.

This section is designed to help you understand how to run the WES automator. Using the "wes_automator" source code, we will submit a run and the automator will create an instance named "wes-auto-fast-test".  Please note that this is a test for the sake of this tutorial.  If you are running clinical trial data, you will upload config.yaml sheets for your sample and there will be additional steps at the end before you delete this instance.

***Before moving on, make sure that Len has added your ssh keys to the wes
image; Only proceed if the keys have been added!***

NOTE: These steps are completed on the automator instance created in Part 1.  

0. Log into your automator instance:
```bash
gcloud compute ssh <username>@{username}-automator-instance:~/.ssh/
```

1. Clone the wes_automator repository (if you don’t have a copy already):

``` bash
cd wes_automator
git clone git@bitbucket:plumbers/wes_automator
```
#Note: if you cloned the wes_automator repository previously, check if there are any updates:

``` bash
cd wes_automator
git pull git@bitbucket.org:plumbers/wes_automator
```

2. Check the config.yaml file.  

In your local wes_automator source code directory, you will find a file called 'test.config.yaml'.  This file can be directly used for the test run without any modifications.
For actual trial runs, config.yaml files should be generated with all of the correct parameters.  However, if this is not the case, “test.config.yaml” can be used as a template to create the actual config file for that particular case.

Parameters that are unique to each wes run, include the following:

    - instance_name:
        define the instance name- any arbitrary string but cannot contain '.' (For clinical trial data, this name will be the first part of the CIMAC ID -- all but the ".01" or ".02" -- in lower case)
    - cores:
        define the number of cores for the instance--default is 32
    - disk_size:
        define the size of the attached disk
    - google_bucket_path:
        define the google bucket path to store the results when run is complete
    - wes_ref_snapshot:
        define which version of wes reference snapshot to use
    - (optional) wes_commit:
        define the exact wes commit version to use
    - samples:
        define the sample names and the google bucket paths to their fastqs
    -  metasheet:
        define run name and replicates


3. Run wes automator:
    (activate the automator environment if you have not done so already)
    ```bash
    source activate automator
    ```

    run wes_automator:

    ```bash
    ./wes_automator.py -c test.config.yaml -u [your google cloud username--usually your hostname] -k ~/.ssh/google_compute_engine
    E.g. ./wes_automator.py -c test.config.yaml -u sal26 -k ~/.ssh/google_compute_engine
    #wes automator should run successfully, it will print diagnostic messages until it finishes.
    #If you encounter an error, please send the output of wes automator to Len.
    ```
    NOTE: -c is where you specify the wes automator config file.
    NOTE: It may take a few minutes to finish, and you'll see lots of print outs.
    The final line should look like this-

    """
    The instance wes-auto-fast-test is running at the following IP: {IP address}
    please log into this instance and to check-in on the run
    """

    ***Use the instance name, wes-auto-fast-test to check for the instance name on the GCP console for use in the next step. Note that users can change the instance name in the test.config.yaml***

4. Check on the run:

    There are two options to check on the run
    Option 1: manually check on the instance

        1. Log into the instance:
          ```bash
          gcloud compute ssh  [username]@wes-auto-fast-test
          cd /mnt/ssd/wes
          ```


        2. run top:
          ```bash
          top
          ```
          - wait until the first item is 'dockerd' or 'containerd' or 'top'
          - if you see these, then you know the run is done

    Option 2: check the slack channel
        goto: https://app.slack.com/client/T01G4FTEDS8/C01FRJXJZRU
        NOTE: ask Len for access

#Note: If you are running WES_automator with actual clinical trial data, then transfer the version info using the transfer script provided in the VM image to the google bucket. Also, follow the ingestion documentation before proceeding to the below section.

### PART 3. WHEN the run is complete:
NOTE: This is a VERY important part of the process, otherwise the instance is wasting money after the runs are done.
  1. Delete the instance:
      a. goto: https://console.cloud.google.com/compute/instances?organizationId=636937865278&project=cidc-biofx&instancessize=50
      b. select "wes-auto-fast-test" instance and then click the trash can
          icon at the top of the screen
