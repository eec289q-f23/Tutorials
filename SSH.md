# SSH

We will use SSH keys for both HPC2 and GitHub. We will assume you are using Linux (via WSL2 or natively) or macOS, which should have SSH installed natively. If there are issues, see [Install SSH on Linux](#install-ssh-on-linux) or [Install SSH on macOS](#install-ssh-on-macos) below. If using Windows directly, see [Install SSH on Windows](#install-ssh-on-windows) below.

Key points with SSH:
- SSH works by generating two files, a "public key" ending in `.pub`, e.g. `id_rsa.pub` and a "private key" with no extension, e.g. `id_rsa`
- **You send your public key to HPC2/GitHub**, you do NOT send your private key
- **Never share your private key with anyone**. If you need to copy the key to a different machine use a physical USB drive or an encrypted service such as SCP or SFTP. See the HPC2 FAQ for more details under "How to move and copy SSH keys"

Other resources:
- See [GitHub: About SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh) for how to add an SSH key to GitHub
- See [HPC2: FAQ](https://www.hpc.ucdavis.edu/faq) for many SSH-related questions
- See [ServiceHub: Generating SSH Keypair to Connect to HPC Clusters](https://servicehub.ucdavis.edu/servicehub?id=ucd_kb_article&sysparm_article=KB0007273) for additional information (make sure to still read the recommendations below)

## SSH Keys

### Managing One SSH Key

- Generate an SSH key pair with default path
  - Default name is `id_rsa` which stores private key as `~/.ssh/id_rsa` and public key as `~/.ssh/id_rsa.pub` 

```console
❯ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (~/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in ~/.ssh/id_rsa
Your public key has been saved in ~/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:1f46Lyq+1c...
The key's randomart image is:
+---[RSA 3072]----+
|        o.  .    |
...
|       .oo=o.*o  |
+----[SHA256]-----+
```

- Add the key to your SSH agent to avoid typing in the password every time

```console
❯ eval $(ssh-agent)
Agent pid 16922
❯ ssh-add ~/.ssh/id_rsa
```

- Update permissions for new key and config (assuming linux and macOS)
  - Private key: file permissions must be `600` = `u=rw` = `rw-------`
  - Public key and config: file permissions must be `644` = `u=rw,g=r,o=r` = `rw-r--r--`
  - SSH folder: directory permissions must be `700` = `u=rwx` = `drwx------`

```console
❯ chmod u=rw ~/.ssh/id_rsa
❯ chmod u=rw,g=r,o=r ~/.ssh/id_rsa.pub
❯ chmod u=rwx ~/.ssh
```

- Output the **public key** to add to the HPC2 account request form
  - *VERY IMPORTANT*: Make *sure* this is the public key ending in `.pub`, NOT the private key

```console
❯ cat ~/.ssh/id_rsa.pub
```

- Verify you output a public key by checking the output. It should look like the following with `ssh-rsa`, followed by a long string of alphanumeric characters *with no spaces* ending in `==`. Then there is a comment which my default is your username@hostname that you're on (this part doesn't matter)
  - This is *just* an example, your public key will be different
  ```console
  ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
  GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
  Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
  t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
  mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
  NrRFi9wrf+M7Q== schacon@mylaptop.local
  ```

- If we SSH into HPC2 with `ssh <kerberosid>@hpc2.engr.ucdavis.edu`, the SSH client will automatically search for `id_rsa` and find this key. You can see this if you use `ssh -v <kerberosid>@hpc2.engr.ucdavis.edu`.

### Managing Multiple SSH Keys

If you have multiple SSH keys, e.g. for GitHub and HPC2, it is best to use a different output filename and modify your SSH config to use the right key with the right host.

- Generate an SSH key with a specific path
  - We will use `hpc2_id_rsa` as if generating for HPC and store in `~/.ssh` as usual

```console
❯ ssh-keygen -f ~/.ssh/hpc2_id_rsa
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in ~/.ssh/hpc2_id_rsa
Your public key has been saved in ~/.ssh/hpc2_id_rsa.pub
The key fingerprint is:
SHA256:upmhbeZwsXgDg...
The key's randomart image is:
+---[RSA 3072]----+
|                 |
...
|.   +**     . .. |
+----[SHA256]-----+
```

- Add the key to your SSH agent to avoid typing in the password every time

```console
❯ eval $(ssh-agent)
Agent pid 16922
❯ ssh-add ~/.ssh/hpc2_id_rsa
```

- Add a section in `~/.ssh/config` for this host with the following, using your Kerberos username for <USER>
  - If this file doesn't exist, create it first with `touch ~/.ssh/config`
  - Without this config you would have to do `ssh -i ~/.ssh/hp2_id_rsa <kerberosid>@hpc2.engr.ucdavis.edu`
  - With this config you can just do `ssh hpc2`

```txt
Host hpc2 hpc2.engr.ucdavis.edu
    HostName hpc2.engr.ucdavis.edu
    IdentityFile ~/.ssh/hpc2_id_rsa
    User <KerberosID>
```

- \[Optional\] You can add the following section as well to your `~/.ssh/config`, at the end of the file 
  - `ForwardAgent yes` allows the remote server to use your local SSH keys, e.g. for GitHub or another service
  - `ForwardX11 yes` allows for X11 forwarding for any graphical interfaces
  - `IdentitiesOnly yes` forces a 1:1 mapping from entries in this file to your keys, which can speed up the process (this is optional, you can try it both ways)
  - `ServerAliveInterval` and `ServerAliveCountMax` help keep connections alive and prevent "broken pipe" errors (see [this post](https://unix.stackexchange.com/a/3027))

```text
Host *
    IdentitiesOnly yes
    ForwardAgent yes    
    ForwardX11 yes
    ServerAliveInterval 60
    ServerAliveCountMax 10
```

- Update permissions for new key and config (assuming linux and macOS)
  - Private key: file permissions must be `600` = `u=rw` = `rw-------`
  - Public key and config: file permissions must be `644` = `u=rw,g=r,o=r` = `rw-r--r--`
  - SSH folder: directory permissions must be `700` = `u=rwx` = `drwx------`

```console
❯ chmod u=rw ~/.ssh/hpc2_id_rsa
❯ chmod u=rw,g=r,o=r ~/.ssh/hpc2_id_rsa.pub
❯ chmod u=rwx ~/.ssh
```

## Login

Once you've copied the public SSH key (by uploading to a website for GitHub/HPC2, or via `ssh-copy-id` for most other servers) you should be able to login. Note for HPC2 it may take a day or two for the account to become active. See [HPC2](./HPC2.md) for more info on HPC2.

Here are some HPC2 examples. The shorthand version (e.g. `ssh hpc2`) only works if you've made the changes to `~/.ssh/config` above.

```console
# Works if key is id_rsa
❯ ssh <user>@hpc2.engr.ucdavis.edu

# Works if key is hpc2_id_rsa
❯ ssh -i ~/.ssh/hpc2_id_rsa <user>@hpc2.engr.ucdavis.edu

# Works if key and host are in ~/.ssh/config
❯ ssh hpc2
```

##  Error: Permission Denied (publickey)

This is a common error telling you that SSH cannot find your local private key corresponding to your public key on the server. You can spend time Googling it to search for answers or start below and see if this advice works to help you. 

Before anything, make sure you have submitted the HPC2 account creation form and have gotten an email saying your account is setup. Let's assume your private key is located at `~/.ssh/hpc2_id_rsa` (and your public key at `~/.ssh/hpc2_id_rsa.pub`), and your kerberos username is `msmith`.
1. Verify both the private and public keys exist in the same folder. In macOS/Linux, this would be `ls -al ~/.ssh`
  - Technically you don't need the public key to SSH in, but we're going to use it in step #2
2. Make sure you have the right password to your private key by running `ssh-keygen -y -f  ~/.ssh/hpc2_id_rsa`
  - You are passing in your private key, and it should print your public key. If it asks for a password, enter it, and this is what you will need to use when using SSH with this key.
3. Double check your permissions, comparing the output in `ls -al ~/.ssh` with the permission changes you did above.
4. Try to SSH again, explicitly passing in this path: `ssh -i ~/.ssh/hpc2_id_rsa msmith@hpc2.engr.ucdavis.edu`
  - This should connect normally. If it asks you about a "known host" and whether you want to connect, say yes.
5. Try again, with verbose output: `ssh -v -i ~/.ssh/hpc2_id_rsa msmith@hpc2.engr.ucdavis.edu`
  - Read this output to understand what's going on and narrow down the problem further
  - First it's looking for config files and applying rules, then its checking known hosts, then it's finding and using your private key you passed in with `-i` .
6. If it still isn't obvious, go to the [ServiceHub](https://servicehub.ucdavis.edu/servicehub) and look at the "HPC2 Account request" ticket to verify your public key
  - The first thing in the ticket at the very bottom will be the key you submitted
  - Compare this to your public key by outputting it, e.g. `cat ~/.ssh/hpc2_id_rsa.pub`

Do your best to fix the issue yourself, but if you get stuck, reach out to the TA for help.

## Installing SSH

### Install SSH on Linux

SSH should already be installed on Ubuntu, but if it is not, see [How to Install SSH on Ubuntu Linux Using apt-get](https://www.cyberciti.biz/faq/how-to-install-ssh-on-ubuntu-linux-using-apt-get/). Other linux distributions should have nearly identical steps, but with a different package manager. 

### Install SSH on macOS

SSH should already be installed on macOS, but for additional details see [Setup Personal SSH Keys on macOS](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-macos/).

### Install SSH on Windows

On Windows you have two choices. Technically you should be able to use PowerShell w/ OpenSSH to generate a keypair, but there are some situations where it doesn't work. HPC2 recommends using MobaXTerm, and we recommend using that or WSL2 w/ Ubuntu. 

For the MobaXTerm instructions, go to https://www.hpc.ucdavis.edu/faq and follow the instructions under "How to generate SSH keypair in a Windows operating system?".

For WSL2, search how to install WSL2 on Windows and choose Ubuntu. Then follow the Linux instructions above (this is our recommended approach). This also integrates very well with VSCode if you're going to use that moving forward.
