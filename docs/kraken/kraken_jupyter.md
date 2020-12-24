# Run Juypter Notebook on the remote cluster

## Run Juypter Notebook on the remote cluster

The note is taken from https://docs.rc.fas.harvard.edu/kb/jupyter-notebook-server-on-cluster/

### start a job on the remote server

require an interactive node

```bash
$ ssh kraken

# time in mins
$ srun -t 1600 --mem=20G -c 4 --pty bash

# use mamba to replace conda for faster installation on kraken
$ mamba create -n jupyter_3.6 python=3.6 jupyter  ## you can add any other package to install you might need.
$ source activate jupyter_3.6

```
To help you connect to the Jupyter server, run the following command to get the hostname.
Select an available port between 6818 and 11845 (in this example, 6820 is the first such available port):

```bash
$ for myport in {6818..11845}; do ! nc -z localhost ${myport} && break; done
$ echo "ssh -NL $myport:$(hostname):$myport $USER@kraken.dfci.harvard.edu"
# ssh -NL 6819:node21:6819 mtang@kraken.dfci.harvard.edu
```

Starting teh ntoebook server:

```bash
jupyter-notebook --no-browser --port=$myport --ip='0.0.0.0'

```
### on your local computer

```bash
ssh -NL 6819:node21:6819 mtang@kraken.dfci.harvard.edu
```

Then in your workstation/laptop browser. Make sure to copy the token from the Jupyter notebook server and update the token below.

`http://localhost:6819/?token=<TokenFromYourServer>`




## Run Juypter Notebook on the remote cluster in a container
The note is from https://github.com/nteract/hydrogen/issues/1184

I am running in a Singularity container on a remote cluster, working on node called 'node01'.

### on the Server


1. edit ~/.jupyter/jupyter_notebook_config.py, commenting in c.NotebookApp.token = 'my_new_token_that_I_wrote'
2. run jupyter notebook, here for my install:

```bash
singularity exec -B /path/to/my/jupyter_notebook:/home/bmoran/jupyter_notebook /path/to/singularity/containter.for.analysis.sif jupyter-notebook --no-browser --port=8891 --ip=127.0.0.1
```

### on your Local machine 

1. ssh tunnel to server `ssh -N -f -L 127.0.0.1:8891:127.0.0.1:8891 node01 -v -v`

you should be able to copy/paste the URL printed to screen on server and connect locally in browser now.

### Atom
0. Packages -> Settings -> Manage Packages -> Hydrogen Settings [[scroll to Kernel Gateways, adding following]] [{ "name": "node01", "options": { "baseUrl": "http://127.0.0.1:8891", "token": "my_new_token_that_I_wrote" } }] and restart Atom
1. Open a file in Atom that you want to run in the kernel that you are connected to in browser
2. Packages -> Hydrogen -> Connect to Remote Kernel [[select a gateway, click node01, enter token]]
3. The filename open in the browser should be available to select, and run in kernel, edit and save in your current script. NB that any script in Atom can now be run in kernel...very nice!