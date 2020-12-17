# Run Juypter Notebook in a container on the remote cluster

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