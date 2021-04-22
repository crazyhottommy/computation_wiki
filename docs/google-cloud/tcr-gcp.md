# TCR ANALYSIS ON GCP

## INITIAL SETTING UP

### VM properties recommendation:
n1-standard-1 (1 vCPU, 3.75 GB memory) 
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

# Install R

sudo apt-get update
sudo apt-get install r-base r-base-dev libtool pkg-config libnlopt-dev libcurl4-openssl-dev 

# Get VisualizIRR files

cd /tcr/
curl 'https://github.com/d-s-cohen/visualizirr/archive/refs/heads/master.zip' --output master.zip
unzip master.zip
rm master.zip
mv * template
```

### 2. Change values in template/r/config.R:
 - input_format value (Likely "ADAPTIVE" or "RHTCRSEQ")
 - json_out=TRUE

### 3. Install R packages as root:
These can be installed automatically when VisualizIRR runs but it is best to install them ahead of time.
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
suppressMessages(library(BiocManager)

suppressMessages(library(Biostrings))
```

NOTE: If you have trouble with this section, relating to the pbkrtest package, try: 
``` bash
sudo Rscript -e 'install.packages("https://cran.r-project.org/src/contrib/Archive/pbkrtest/pbkrtest_0.4-7.tar.gz")'
```

### 4. Add automator script as ```/tcr/automator.sh```:

``` bash
#!/bin/bash
  
set -x
cp -r /tcr/template $1_report

echo "input_dir = '$1_data'" >> $1_report/r/config.R
echo "output_dir = '$1_report/data'" >> $1_report/r/config.R
echo "output_name = '$1'" >> $1_report/r/config.R
echo "report_dir = '$1_report'" >> $1_report/r/config.R
Rscript $1_report/r/immuneRepProcess.R $1_report/r/config.R
cp $1_meta.csv $1_report/data/meta.csv
tar -czvf $1_report.tar.gz $1_report
```

### 5. File permissions:

``` bash
chmod u+x /tcr/automator.sh
```

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
