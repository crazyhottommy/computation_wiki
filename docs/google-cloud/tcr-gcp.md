# TCR ANALYSIS ON GCP

## BUILDING DISK FROM SCRATCH

### VM properties recommendation:
e2-medium (2 vCPU, 4GB memory)
### Disk properties recommendation:
Debian 10 image, 10GB storage
### R, R package versions: ###
These exact version numbers are by no means essential. They have simply been tested on Google Cloud previously.
```
R version 3.5.2

data.table 1.12.8
immunarch 0.6.5
naturalsort 0.1.3
dplyr 1.0.0
rjson 0.2.20
```
### Additional info for running VisualizIRR:
https://github.com/d-s-cohen/visualizirr

### 1. Setting up disk:
``` bash
# Set up TCR dir

sudo mkdir /tcr/
sudo chmod 777 -R /tcr/

# Install R and other packages

sudo apt-get update
sudo apt-get install r-base r-base-dev libtool pkg-config libnlopt-dev libcurl4-openssl-dev libxml2-dev libssl-dev 

# Get VisualizIRR files

cd /tcr/
curl -L -O 'https://github.com/d-s-cohen/visualizirr/archive/refs/heads/master.zip' --output master.zip
unzip master.zip
rm master.zip
mv * template
```

### 2. Change values in /tcr/template/r/config.R:
 - input_format value (Likely "ADAPTIVE" or "RHTCRSEQ")
 - json_out=TRUE
 - chains_search value (Likely c("TRB") or c("TRB", "TRA"))
 - input_prefix and input_suffix

### 3. Install R packages as root:
These can be installed automatically when VisualizIRR runs but it is best to install them ahead of time.
Immunarch takes a long time to install.
``` R
list.of.packages <- c("data.table","immunarch","naturalsort","dplyr","rjson","BiocManager")

new.packages <- list.of.packages[!(list.of.packages %in% installed.packages()[,"Package"])]
install.packages(new.packages, repos = "http://cran.us.r-project.org")

BiocManager::install("Biostrings")

# Test loading packages

suppressMessages(library(data.table))
suppressMessages(library(immunarch))
suppressMessages(library(naturalsort))
suppressMessages(library(dplyr))
suppressMessages(library(rjson))
suppressMessages(library(BiocManager))

suppressMessages(library(Biostrings))
```

NOTE: If Immunarch can't be downloaded from CRAN try:
``` R
install.packages("devtools")
devtools::install_github("immunomind/immunarch")
```

NOTE: If you have trouble with this section, relating to the pbkrtest package, try: 
``` R
install.packages("https://cran.r-project.org/src/contrib/Archive/pbkrtest/pbkrtest_0.4-7.tar.gz")
```

### 4. Add automator script as ```/tcr/automator.sh```:

``` bash
#!/bin/bash
  
set -x
cp -r /tcr/template $1_report

echo "input_dir = '$1_data'" >> $1_report/r/config.R
echo "output_dir = '$1_report/data'" >> $1_report/r/config.R
echo "output_name = '$1'" >> $1_report/r/config.R
echo "report_dir = NULL" >> $1_report/r/config.R
Rscript $1_report/r/immuneRepProcess.R $1_report/r/config.R
cp $1_meta.csv $1_report/data/meta.csv
tar -czvf $1_report.tar.gz $1_report
```

### 5. File permissions:

``` bash
chmod u+x /tcr/automator.sh
```


## RUNNING FROM PREBUILT IMAGE

### VM properties recommendation:
e2-medium (2 vCPU, 4GB memory)
### Disk image name:
tcr-vis-v040

### Example run:
See /tcr/test-run-cader/ in the prebuilt image for an example of a successful run.

### Logging into new instance:
Once your instance has been created with the tcr-vis-v040 image, log in using gcloud compute ssh

```
gcloud compute ssh --tunnel-through-iap <gcp_username>@<name_of_instance>

# for example:
gcloud compute ssh --tunnel-through-iap jen@jen-tcr-test
```

The tcr automator is located at /tcr/

In order to set up new files and run automator, you will need to change the disk owner to yourself:

```
sudo chown -R <gcp_username>:<gcp_username> /tcr
```

## SETTING UP METASHEET

Check the metasheet section in the VisualizIRR github readme (https://github.com/d-s-cohen/visualizirr) for the generalized format.
An example metasheet is stored in the prebuilt image at /tcr/test-run-cader/Cader_CD8_2020_meta.csv.
It is also included below. 

```
sample,Subcohort,Timepoint|Baseline|Pre-cycle 4,BOR-RECIST,PFS,PFS index,Race,Sex,Age,Subtype,Time between Diagnosis and Collection,Time biopsy - treatment,Time treatment - diagnosis,Time between ASCT and First Dose,Healthy Donor,Baseline Group,VisGroup
ID28_P108_baseline_CD8,A,0,Progressive Disease,0-9,1,White,Male,40-49,mixed cellularity,≥100,0-19,≥100,80-99,Non-donor,Relapsed/Refractory,P33
ID28_P110_PreCycle4_CD8,A,1,Progressive Disease,0-9,1,White,Male,40-49,mixed cellularity,≥100,0-19,≥100,80-99,Non-donor,,P33
ID60_P150_PreCycle4_CD8,C,1,Complete Response,10-19,1,White,Male,10-19,nodular sclerosis,0-19,20-39,20-39,0-19,Non-donor,,P47
ID60_P149_baseline_CD8,C,0,Complete Response,10-19,1,White,Male,10-19,nodular sclerosis,0-19,20-39,20-39,0-19,Non-donor,Relapsed/Refractory,P47
ID67_P131_PreCycle4_CD8,C,1,Partial Response,0-9,1,American Indian/Alaska Native,Male,20-29,not otherwise specified,40-59,0-19,40-59,20-39,Non-donor,,P40
ID67_P129_baseline_CD8,C,0,Partial Response,0-9,1,American Indian/Alaska Native,Male,20-29,not otherwise specified,40-59,0-19,40-59,20-39,Non-donor,Relapsed/Refractory,P40
```

 - The first column 'sample' contains the sample names and must correspond to the names of the input repertoire files with prefix and/or suffix stripped according to the input_prefix and input_suffix values defined in /tcr/template/r/config.R. This can be further checked by comparing these to the sample names in the intracohort_data.csv file generated for the report.
 - Further columns can be customized and represent each condition by which the cohort is split. As you can see above these include response, disease subtype, and others.
 - Condition groups can be simply defined as strings. See the Subcohort column as an example. In this example, groups are defined as A and C.
 - Confition groups can also be defined numerically. See the Timepoint column as an example. In this example, groups are definted as 0 and 1. The pipe seperator in the header defines the names to be displayed in the report corresponding to these numerical values. 0 = "Baseline" , 1 = "Pre-cycle 4". Defining groups numerically orders them, hence numerical grouping for the Timepoint condition.
 - The VisGroup column contains patient IDs and the Timepoint column contains different timepoints. These can be utilized together to conduct paired sample analysis. Above we see two samples with the VisGroup value P33. They have two different timepoints (0 and 1) and therefore will be paired accordingly.

## RUNNING ANALYSIS

 - Make sure directory containing repertoire files is located as ```/tcr/<cohort_name>_data/```
 - Make sure metadata file is located as ```/tcr/<cohort_name>_meta.csv```
 - You may also organize all work into /tcr/ subdirectories if you wish (EX: /tcr/cohort1/, /tcr/cohort2/)
 - Command for running VisualizIRR using the automator script:
``` bash
cd /tcr/

nohup ./automator.sh <cohort_name> > <cohort_name>.log &
```
 - Report output located at ```/tcr/<cohort_name>_report.tar.gz```

To download the report locally, use gcloud compute scp from your local computer:

```
gcloud compute scp --tunnel-through-iap <gcp_username>:<name_of_instance>:/tcr/<cohort_name>_report.tar.gz <location_to_transfer_to>
```

To view the report, you may need to use a python command.  See documentation [here](https://github.com/CIMAC-CIDC/cidc-ngs-pipeline-api/tree/master/cidc_ngs_pipeline_api/tcr)

## UPDATING METASHEET

The metasheet is located in data/meta.csv of the report. Updating the metasheet after the report has been generated is as simple as replacing the file. If you followed the instructions above you don't need to manually place the metasheet in this location. This information is for those who wish to make changes to the metasheet later.
