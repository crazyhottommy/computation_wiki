# Introduction
The Running Automator documention is divided into 3 main parts:

	Part 0 = generating access credentials.
	Part 1 = creating a general automator instance from which to launch WES/RIMA/CHIPS jobs.
	Part 2_* = running a specific pipeline's automator.  Part 2 is actually three separate documents -- one for each pipeline.  (Part2_wes, Part2_rima, Part2_chips)

If you have completed Part 0 (this document) in the past for your pipeline, you can skip this section.

## Getting your credentials to add to the Pipeline VM image(s)
Automators are designed to run the three CIDC pipelines: WES, CHIPS (ATACseq) and RIMA.  Each of them have their own specific VM images that need to be updated with appropriate user credentials.

To be able to use the CIDC Automators, you will need to open an instance and generate an ssh key on the instance.  The ssh key needs to be copied to your bitbucket account and to the specific pipeline VM image.

### Step 1:  Open an instance.

Log into the cloud
```bash
gcloud auth application-default login

# Copy and paste the link into a browser
# Select your google account
# Click "Allow"
# Copy the verification code and paste it into the terminal prompt

```

Set your project (if you have not done so in the past)

```bash
gcloud config set project cidc-biofx
```

Start an instance
The --image-family parameter will copy the latest version of the VM image for a particular pipeline.  Use the following for image-family (without quotes) for the pipeline that you wish to run in the below command line. 
WES = "wes"
CHIPS = "chips"
RIMA = "rima"


```bash
 gcloud compute instances create {yourname}-{pipeline}-keyaddition --machine-type n2-standard-2  --image-family {imagefamilyname} --service-account biofxvm@cidc-biofx.iam.gserviceaccount.com --scopes https://www.googleapis.com/auth/devstorage.read_write,https://www.googleapis.com/auth/logging.write --zone us-east1-b

```

### Step 2: Generate a key on the instance

Log into the instance
```bash
gcloud compute ssh --tunnel-through-iap username@{yourname}-{pipeline}-keyaddition

```

Generate ssh keys 
```bash
ssh-keygen -f instance_ssh

#hit Enter and DO NOT enter a passphrase; then Enter again

```

### Step 3: Add the instance ssh key to your bitbucket account

```bash
cat ~/.ssh/instance_ssh.pub # on the instance
```

1. Copy the contents of the output

2. Go to bitbucket.org -> Click "Profile and Settings" (lower left initials) -> Select Personal Settings: 

  - Under Security- Click "SSH keys"
  
	- Click "Add Key"
	
	    1. Label: [username]@GCP for example, taing@GCP-- use your username.
	    2. Key*: paste the contents of your instance "instance_ssh.pub" into the box.
	    3. Click "Add Key".
	    
  - TEST whether it worked: On the instance,try to clone the corresponding repository that you want to use: {WES,CHIPS or RIMA} 
  
```bash
1: git clone git@bitbucket.org:plumbers/cidc_wes.git
2: git clone git@bitbucket.org:plumbers/cidc_chips.git
3: git clone git@bitbucket.org:plumbers/cidc_rima.git

```


### Step 4: have your instance ssh key copied from the instance to the automator image.

Stop the instance
```bash
# on the instance
logout

# on your local computer
gcloud compute instances stop {yourname}-{pipeline}-keyaddition

```

Let the appropriate person know that your instance is ready to be copied to the VM image.
Len:WES
Gali: CHIPS
Aashna: RIMA
