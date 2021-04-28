# Getting started with Google Cloud

### Prerequisites:

1. CIDC Google Cloud account -- email James Lindsay
2. Software:  miniconda3 -- information on how to install and use miniconda3 can be found here: https://docs.conda.io/en/latest/miniconda.html

### The Google Cloud Console

Some actions can be performed directly on the gcp console.  Most pipelines will be run using the command line interface (see below).

1. Go to https://cloud.google.com
2. Log in with your ds google credentials.


### Google Cloud's Command Line Interface (gcloud sdk)

1. install the gcloud sdk: (it's on conda)

`conda install -c conda-forge google-cloud-sdk`

2. Authenticate:

`gcloud auth login`

NOTE: opens up a webbrowser automatically.  Log into your google account, copy the code provided and paste into your terminal window.

3. set project:


`gcloud config set project cidc-biofx`
