# How to Run Rstudio server on kraken

### install singularity and Rocker

Also read Ming Tang's blog post https://divingintogeneticsandgenomics.rbind.io/post/run-rstudio-server-with-singularity-on-hpc/


You can not run any jobs on the login node, even conda install is not allowed.

First, submit an interactive job to get a node:

```bash
ssh kraken
srun -t 1600 --mem=60G -c 4 --pty bash
```


From `man srun`:

```
       --time-min=<time>
              Set a minimum time limit on the job allocation.  If specified, the job may have it's --time limit
              lowered to a value no lower than --time-min if doing so permits the job to begin  execution  ear‐
              lier  than  otherwise  possible.  The job's time limit will not be changed after the job is allo‐
              cated resources.  This is performed by a backfill scheduling algorithm to allocate resources oth‐
              erwise  reserved  for  higher  priority  jobs.   Acceptable time formats include "minutes", "min‐
              utes:seconds", "hours:minutes:seconds", "days-hours", "days-hours:minutes"  and  "days-hours:min‐
              utes:seconds". This option applies to job allocations.
       -c, --cpus-per-task=<ncpus>
              Request  that  ncpus be allocated per process. This may be useful if the job is multithreaded and
              requires more than one CPU per task for optimal performance. The default is one CPU per  process.
              If  -c is specified without -n, as many tasks will be allocated per node as possible while satis‐
              fying the -c restriction. For instance on a cluster with 8 CPUs per node, a  job  request  for  4
              nodes and 3 CPUs per task may be allocated 3 or 6 CPUs per node (1 or 2 tasks per node) depending
              upon resource consumption by other jobs. Such a job may be unable to execute more than a total of
              4  tasks.   This option may also be useful to spawn tasks without allocating resources to the job
              step from the job's allocation when running multiple job steps with the --exclusive option.
       -N, --nodes=<minnodes[-maxnodes]>
              Request that a minimum of minnodes nodes be allocated to this job.  A maximum node count may also
              be specified with maxnodes.  If only one number is specified, this is used as  both  the  minimum
              and maximum node count.  The partition's node limits supersede those of the job.  If a job's node
              limits are outside of the range permitted for its associated partition, the job will be left in a
              PENDING  state.   This  permits  possible  execution at a later time, when the partition limit is
              changed.  If a job node limit exceeds the number of nodes configured in the  partition,  the  job
              will  be  rejected.  Note that the environment variable SLURM_JOB_NUM_NODES (and SLURM_NNODES for
              backwards compatibility) will be set to the count of nodes actually allocated to the job. See the
              ENVIRONMENT VARIABLES section for more information.  If -N is not specified, the default behavior
              is to allocate enough nodes to satisfy the requirements of the -n and -c options.  The  job  will
              be allocated as many nodes as possible within the range specified and without delaying the initi‐
              ation of the job.  If number of tasks is given and a number of requested nodes is also given  the
              number  of  nodes  used from that request will be reduced to match that of the number of tasks if
              the number of nodes in the request is greater than the number of tasks.  The node count  specifi‐
              cation  may  include  a  numeric  value  followed by a suffix of "k" (multiplies numeric value by
              1,024) or "m" (multiplies numeric value by 1,048,576). This option applies to job and step  allo‐
              cations.

       -n, --ntasks=<number>
              Specify  the  number of tasks to run. Request that srun allocate resources for ntasks tasks.  The
              default is one task per node, but note that the --cpus-per-task option will change this  default.
              This option applies to job and step allocations.}
```

After you get a computing node, you will see the prompt becomes something like:

`(base) [mtang@node03 mtang]$`.


Note, conda `singularity` has problems to run.

Ask the admin to install `singularity` on the server instead.
Luckily, it is on `kraken` already.

```bash
module load singularity
```

pull the rocker image.  read https://www.rocker-project.org/use/singularity/

```bash
cd /liulab/mtang
mkdir singularity_images; cd !$
singularity pull --name rstudio.simg docker://rocker/tidyverse
```

To enable password authentication, set the PASSWORD environment variable and add the `--auth-none=0 --auth-pam-helper-path=pam-helper` options:

```bash
PASSWORD='xyz' singularity exec --bind=/liulab/mtang  rstudio.simg rserver --auth-none=0  --auth-pam-helper-path=pam-helper  --www-address=127.0.0.1
```

This will run rserver in a Singularity container. The `--www-address=127.0.0.1` option binds to localhost (the default is `0.0.0.0`, or all IP addresses on the host). listening on `127.0.0.1:8787`.

From your local workstation (e.g., mac book), ssh tunneling to the compute node.

on your local mac:

```bash
# check which ports are open
sudo lsof -i -P -n | grep TCP
ssh -t -t kraken -L 59083:localhost:59083 ssh node03 -L 59083:localhost:8787
```
The prompt will change to the compute node.

Go to your mac local browser, open `localhost:59083`, type your HPC username and `xyz` as the password in this dummy example.
You should have a Rstudio server running!


### set up local library

You will want to install libraries so that the singularity container can use.

If you go to your browser and inside Rstudio:

```r
> .libPaths()
[1] "/usr/local/lib/R/site-library" "/usr/local/lib/R/library" 
```

You will see the library is pointing to the ones inside the container.

Set up a `.Renviron` file in your HPC login node:

```bash
if [ ! -e ${HOME}/.Renviron ]
then
  printf '\nNOTE: creating ~/.Renviron file\n\n'
  echo 'R_LIBS_USER=~/R/%p-library/%v' >> ${HOME}/.Renviron
fi
```

Now, go to Rstudio in the browser ---> `Session`---> `Restart R`.

```r
> .libPaths()
[1] "/homes6/mtang/R/x86_64-pc-linux-gnu-library/3.6" "/usr/local/lib/R/site-library"                  
[3] "/usr/local/lib/R/library
```

You now see that `"/homes6/mtang/R/x86_64-pc-linux-gnu-library/3.6"` is my local library. If I install packages inside Rstudio,
it will be installed there.





