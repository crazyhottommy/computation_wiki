# sshfs to mount remote kraken cluster folder to local computer

follow https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh

### on mac

```bash
brew install --cask osxfuse
brew install sshfs

## create a config file 
$ cat ~/.ssh/config
Host *
 ServerAliveInterval 60

Host kraken
	 HostName kraken.dfci.harvard.edu
	 User mtang
	 RemoteForward 52698 localhost:52698

cd ~
mkdir kraken

## mount the remote dir to local ~/kraken
sshfs -o allow_other,default_permissions  kraken:/liulab/mtang kraken

## umount
umount kraken
```