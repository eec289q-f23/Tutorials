# HPC2 Usage

We will use the [HPC2 Cluster](https://hpc.ucdavis.edu/clusters) managed by the UC Davis HPC Core Facility for this course. Many aspects are outlined in the [HPC2 FAQ](https://www.hpc.ucdavis.edu/faq).

## Create an Account

- Generate an SSH key following the [SSH Tutorial](./SSH.md)
- Request account access with the [HPC2 Account Request Form](https://hpc.ucdavis.edu/form/account-request-form)
  - Under "Account Sponsor" select "EEC 289Q Performance Engr."
  - Under "SSH Public Key" paste your public SSH key, e.g. output from `cat ~/.ssh/hpc2_id_rsa.pub`
- It will take up to a few days to gain access (hopefully sooner, and you will get an email when it goes through)
- Post a question on Slack or Canvas if you have any issues

## Login

- After you get an email that your account is approved, you can login with SSH

```console
# Works if key is id_rsa
❯ ssh <user>@hpc2.engr.ucdavis.edu

# Works if key is hpc2_id_rsa
❯ ssh -i ~/.ssh/hpc2_id_rsa <user>@hpc2.engr.ucdavis.edu

# Works if key and host are in ~/.ssh/config
❯ ssh hpc2
```

## Setup Home Directory

Your home directory on HPC2 is like any other Linux home directory, except that it is shared among all nodes on HPC2. You can create a `~/.bashrc` file for additional configuration, clone GitHub directories to `~/` and copy files from your local machine.

For GitHub, we recommend creating a new SSH key on HPC2 to interact with Git. Alternatively you can set `ForwardAgent yes` in your `~/.ssh/config` on your _local machine_ to forward your GitHub SSH key from your local machine to HPC2 to use with GitHub. 

To copy files, you can use `scp` or `sftp` for encrypted transfers (e.g. files containing passwords or keys), or `rsync` for unencrypted transfers (e.g. homework and project source code). Using `rsync` is great for copying files from one directory to another locally as well, since it shows the file transfer in a more helpful way. See [this scp tutorial](https://www.garron.me/en/articles/scp.html) and [this rsync tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories) for more details.

**If developing both locally and remotely on HPC2, we recommend cloning the GitHub repo in both locations and using `git push`, `git pull` and `git stash` to handle syncing code.** This way you don't accidentally overwrite files and lose changes. Using VSCode will also prevent potential issues if your connection drops, since those unsaved files are cached locally.  

## Use Slurm

HPC2 uses Slurm to launch jobs. See our dedicated [Slurm](./SLURM.md) tutorial. This will be updated throughout the course as the assignments get more complicated so you don't get overwhelmed all at once.
