# SSH

We will use SSH keys for both HPC2 and GitHub. We will assume you are using Linux (via WSL2 or natively) or macOS, which should have SSH installed natively. If there are issues, see [Install SSH on Linux](#install-ssh-on-linux) or [Install SSH on macOS](#install-ssh-on-macos) below. If using Windows directly, see [Install SSH on Windows](#install-ssh-on-windows) below.

Two key points:
- HPC2 says to "**use a strong, memorable passphrase**" along for your SSH key. To avoid typing the password in, you can use SSH-agent or keychain to store the password (shown below).
- Remember to **never share your private key with anyone**. If you need to copy the key to a different machine use a physical USB drive or an encrypted service such as SCP or SFTP. See the HPC2 FAQ for more details under "How to move and copy SSH keys"
- HPC2 says "for RSA we recommend using a key size of 2048 or 4096" which we can specify with `ssh-keygen -t rsa -b <size>`. On Linux the default is an RSA key with 3072 bits which should be fine.

Other resources:
- See [GitHub: About SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh) for how to add an SSH key to GitHub
- See [HPC2: FAQ](https://www.hpc.ucdavis.edu/faq) for many SSH-related questions
- See [ServiceHub: Generating SSH Keypair to Connect to HPC Clusters](https://servicehub.ucdavis.edu/servicehub?id=ucd_kb_article&sysparm_article=KB0007273) for additional information (make sure to still read the recommendations below)

## SSH Keys

### Managing One SSH Key

- Generate an SSH key with default path
  - Default key is `id_rsa` which stores private key as `~/.ssh/id_rsa` and public key as `~/.ssh/id_rsa.pub` 

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

- Copy the key to your server
  - *NOTE: We do not have to do this for HPC2 or GitHub, but you will need to do this for any personal machines you may have.* (see [HPC2](./HPC2.md) for more info on HPC2)

```console
❯ ssh-copy-id -i ~/.ssh/id_rsa <user>@<hostname>
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

- If we SSH somewhere with `ssh <username>@<hostname>`, the SSH client will automatically search for `id_rsa` and find this key. You can see this if you use `ssh -v <username>@<hostname>`.

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

- Copy the key to your server
  - *NOTE: We do not have to do this for HPC2 or GitHub, but you will need to do this for any personal machines you may have*

```console
❯ ssh-copy-id -i ~/.ssh/hpc2_id_rsa <user>@<hostname>
```

- \[Recommended\] Add a section in `~/.ssh/config` for this host with the following, using your Kerberos username for <USER>
  - If this file doesn't exist, create it first
  - Without this you would have to do `ssh -i ~/.ssh/hp2_id_rsa <user>@<full_hostname>`
  - With this you can just do `ssh <short_hostname>`, e.g. `ssh hpc2`

```txt
Host hpc2 hpc2.engr.ucdavis.edu
    HostName hpc2.engr.ucdavis.edu
    IdentityFile ~/.ssh/hpc2_id_rsa
    User <USER>
```

- \[Optional\] You can add the following section as well to your `~/.ssh/config` 
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

Once you've copied the private SSH key (either via `ssh-copy-id` for your own server, or uploading to a website for GitHub/HPC2) you should be able to login. Note for HPC2 it may take a day or two for the account to become active. See [HPC2](./HPC2.md) for more info on HPC2.

Here are some HPC2 examples. The shorthand version (e.g. `ssh hpc2`) only works if you've made the changes to `~/.ssh/config` above.

```console
# Works if key is id_rsa
❯ ssh <user>@hpc2.engr.ucdavis.edu

# Works if key is hpc2_id_rsa
❯ ssh -i ~/.ssh/hpc2_id_rsa <user>@hpc2.engr.ucdavis.edu

# Works if key and host are in ~/.ssh/config
❯ ssh hpc2
```

## Installing SSH

### Install SSH on Linux

SSH should already be installed on Ubuntu, but if it is not, see [How to Install SSH on Ubuntu Linux Using apt-get](https://www.cyberciti.biz/faq/how-to-install-ssh-on-ubuntu-linux-using-apt-get/). Other linux distributions should have nearly identical steps, but with a different package manager. 

### Install SSH on macOS

SSH should already be installed on macOS, but for additional details see [Setup Personal SSH Keys on macOS](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-macos/).

### Install SSH on Windows

On some windows versions, openssh may be installed, but that version is sometimes outdated. To check if this is installed, in an elevated powershell, run

```
PS> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : NotPresent

Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent
```

The output should be as above (`NotPresent`). To install the latest version, there are 2 supported methods. If winget is installed, it is the recommended method
* **OpenShh Portable**
  Install the .msi package from [this](https://github.com/PowerShell/Win32-OpenSSH/releases/latest) GitHub release page
* **With `winget`**
  To install openssh with winget, run the following command in a powershell
  ```
  PS> winget install Microsoft.OpenSSH.Beta
  ```

It might also help to install the new [Windows Terminal](ms-windows-store://pdp/?ProductId=9N0DX20HK701) (alternatively, [here](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701)) to replace the default Console host.

Also see the following questions in the [HPC2 FAQ](https://www.hpc.ucdavis.edu/faq): "How to generate SSH keypair in a Windows operating system?"