# CIDC Software team ingestion script how-to

By Len Taing 2020-05-18
ref: https://stagingportal.cimac-network.org/analyses//cli-instructions

### Create a conda environment for the cidc-cli script:
   NOTE: `wes images >= wes-ver1-99c` already have the cidc-cli conda env
   located at: `/home/taing/miniconda3/envs/cidc-cli`
   so there's no need to build a new one for wes google images

a. write a file called `cidc-cli.env.yml` with the following content:

```
name: cidc-cli
channels:
- bioconda
- defaults
dependencies:
- python=3
- pip:
  - cidc-cli
```

b. create the conda env:  
      `conda env create -f cidc-cli.env.yml -n cidc-cli`  
c. When done, save a new google image, e.g. for WES I created `wes-ver1-99c`  
d. start a new google image with the image you just created


FROM this point on in the how-to, it is assumed that the cidc-cli conda env
and scripts are installed correctly.  

The workflow from the software team is:  
1. the software team will generate a automator config file (e.g. wes_automator
   config file) and a corresponding .xslx sheet for a patient/batch of samples
   to RUN and ingest  
2. The bioinformatics team (that's us) will run the automator config file
   given in step 1.  This how to ALSO assumes that the pipeline run from 
   the automator ran successfully and it is now time to ingest results 
   into the cidc software team's db


### AFTER the wes/rnaseq automator run is complete (without errors)

a. activate the cidc-cli conda env:  
   `source activate cidc-cli`  
b. UPLOAD the .xlsx sheet associated with the automator config (given by the software team) to the instance:
   NOTE: in this example, the .xlsx sheet is called  "wes_analysis_CTTTP02N1.00_20200129.xlsx"
   EXAMPLE:  
   `scp -i ~/.ssh/google_compute_engine wes_analysis_CTTTP02N1.00_20200129.xlsx taing@35.237.164.38:/mnt/ssd/wes`  
   c. LOG in:   
   1. goto: 
      	 https://stagingportal.cimac-network.org/analyses//cli-instructions  
   2. scroll to the bottom of page and copy login cmd (i.e. last line):  
      	 `cidc login ...[long key string]...`  
   3. paste cmd into ssh shell  
   d. RUN ingestion script:  
      `cidc analyses upload --analysis wes_analysis --xlsx ./wes_analysis_CTTTP01N1.00_20200129.xlsx`  
      
**NOTE:** the script my prompt you with questions-   
answer "Yes" to all of them; for the google auth step, copy and paste the
URL given and complete the authentication process.

**NOTE:** the parameter --analysis may be different depending on your pipeline

**NOTE:** the ingestion script will now try to upload the files and it may take some time


