# HPC information for Liu lab

Nikos George <nikos[at]ds.dfci.harvard.edu> is the head of our Department Computing. Contact Nikos to get the Kraken (dept server) account. 

documentation is at http://dscomputing.dfci.harvard.edu/index.php/kraken/
you can only access when you login the parterns' VPN (pvc.partners.org/saml).

Nikos will ask for your ssh public key.

Generate a key pair by 

```bash
$ ssh-keygen -b 2048
```


send `~/.ssh/id_rsa.pub` to Nikos. Remember sending only out the public key, not the private key `id_sra`.

>After you’ve added your public key to the remote host, try logging in a few times. You’ll notice that you keep getting prompted for your SSH key’s password. If you’re
scratching your head wondering how this saves time, there’s one more trick to know: `ssh-agent`. The ssh-agent program runs in the background on your local machine,and manages your SSH key(s). ssh-agent allows you to use your keys without entering their passwords each time—exactly what we want when we frequently connect to servers. SSH agent is usually already running on Unix-based systems, but if not, you can use eval ssh-agent to start it. Then, to tell ssh-agent about our key, we use `ssh-add`:

From [`bioinformatics data skills`](http://shop.oreilly.com/product/0636920030157.do).

```bash
$  ssh-add
```

>You have write permission to /liulab which is a lab share and to /cluster/liulab which is on a fast (IO intensive) cluster filesystem, strictly to be used as scratch space.
Please note that /cluster/liulab is only available to the work nodes, but you can point your TMP_DIR to it

#### Home Directory Storage
Each user is given a home directory (/homes/username) that is mounted on all cluster nodes. It has a size limit of 20Gb. Please use it for basic login scripts and simple submit jobs. It is backed up daily, and backups are kept for 3 months.

#### Lab Storage
Each user has write permissions to their appropriate lab share (/name-of-lab.) Lab shares are mounted on all cluster nodes and can also be mounted on desktops and laptops. Size limits depend on the particular lab, this is where you put your regular data and work files. It is backed up weekly, and backups are kept for 3 months

#### High Performance Scratch Storage
Every lab has access to our high performance scratch space (/cluster/name-of-lab) Each user can create their own folder. This filesystem is managed by the GPFS parallel filesystem and is apropriate for data intensive jobs. It is mounted on all work nodes, but not on the head nodes. It is considered as a temporary storage. Files are not backed up and if the storage fills up we may delete any files, so once your analysis has been completed please move your files to your lab share.

#### Local tmp storage
Every work node has a small storage partition (approximately 100Gb) that is suitable for temp files (/tmp). This partition is not backed up and files can be deleted at any time. It is best not to use it since it is specific to the node and not shared across nodes. If your application is contained within the same node you can point TMPDIR to it.
* Contact Annie Ng <annie@ds.dfci.harvard.edu> to connect to the Iris / Daisy (LiuLab server, cistrome.org).