# SSH - Secure Shell

SSH is used to connect to remote machines securely, usually on port 22. Data that is transferred is encrypted and can use public keys to authenticate a log in. This document won't explain how SSH and keys, work but it will tell you how to use them. All hosts and IPs used as examples have been changed so they won't work and you can stop trying to force your way into my machines.

SSH is a topic where there are some differences between Windows and Unix operating systems, though the ssh commands themselves will generally remain the same.

## OpenSSH Client and Server

The OpenSSH Client service is used to connect to remote machines, the OpenSSH Server is so remote machines can connect to the local machine.

### Linux

Starting these services is simple in linux, for a Debian based distribution:

`sudo /etc/init.d/ssh start`

or

`sudo service ssh start`

or

`sudo systemctl start ssh`

To stop or restart the service, change the keyword start to stop or restart respectively.

### Windows 10

First you need to ensure the OpenSSH client is installed, open a powershell terminal with administrator privileges and run:

`Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'`

If the OpenSSH Client and server are not present, you can install them respectively:

`Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0`

`Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0`

The client is used to connect to remote machines, the server is so remote machines can connect to the local machine. 

Then start the service and optionally set it to start automatically:

`Start-Service sshd`

`Set-Service -Name sshd -StartupType 'Automatic'`

Configure the firewall:

```
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```

You can then connect using `ssh 

## Connecting via SSH

To connect to a remote machine via ssh:

`ssh [username]@[host/IP_address]`

Example:

`ssh laportag@45.293.611.140`

You will be prompted to trust the host if it is the first time connecting, type yes and hit enter. You will then be prompted to enter the password, it will hide the characters you type, just type the password and hit enter. The terminal will take you to a shell for the remote machine where you can traverse the directory tree and enter commands.

Sometimes the ssh server of the remote machine is configured to a different port than the default 22 (for instance, 2222), in this case you will need to use the *-p* tag and specify the correct port:

`ssh -p [port] [username]@[host/IP_address]`

## SSH Keys

Often the ssh of a machine will be configured so that authentication by password is disabled, other times you will want to connect in a way that makes the password input is troublesome, at others you may just feel lazy. In these cases you can use an identity key instead of a password for authentication. 

Using a key to authenticate via ssh is far more secure than using password authentication; ssh is often used in malicious attacks that attempt to brute force their way into machines with various usernames and passwords. Key based authentication is done on the local machine so your authentication details are not exposed to the network; it is very secure and best practise.

When you generate an SSH key pair on a machine it creates a pair of keys - the public key and the private key. *Never share the private key.* The file for the public key is also known as the identity file. In order for a key-based authentication to pass, a local machine must know the public key of the remote machine. The remote machine must have the private key confgured so that when the authentication is attempted, the two keys can view each other and pass, only the correct pairing will pass authentication.

SSH keys are a hash and can be saved in different file formats, [this link demonstrates the difference between *rsa* and *pem*.](https://hstechdocs.helpsystems.com/manuals/globalscape/eft7-3/mergedprojects/eft/server_ssh_key_formats.htm#:~:text=EFT%20imports%20the%20PEM%20format,key%20on%20a%20Linux%20computer.)

### Generating SSH Keys

To generate ssh key pairs:

`ssh-keygen`

You will be prompted with an option of where to save the key files (`/home/[user]/.ssh/id_rsa` or `C:\Users\[user]/.ssh/id_rsa`). Press enter to confirm. (The *.ssh* folder is where you will find the files used by the ssh client and server.) You will be prompted to enter a passphrase for the encryption generation, either leave it blank or choose a password and enter it twice. If you decide to make a password you will have to enter it whenever you use the key. The private key is id_rsa and the public key is id_rsa.pub.

Arguments and tags can be included in the ssh-keygen for more complex functionality. One such option is to specify the type of key generated with *-t*:

`ssh-keygen -t [keytype]`

Possible values are *dsa*, *ecdsa*, *ecdsa-sk*, *ed25519*, *ed25519-sk* and *rsa*.

Remember that a key will only work with it's pair, if you lose the credentials you will have to generate a new key pair.

### Connecting via ssh with a Key pair

To connect to a remote machine via ssh using key based authentication and a public key file:

`ssh -i [relative/path/to/identity/file] [username]@[host/IP_address]`

The path is relative to the directory the terminal is working in.

### Copying a Key to a Remote Machine

Public keys can also be saved to a remote machine's *.ssh* folder to save from specifying a key file when connecting.

#### Linux

If you copy your public id to the remote machine, you do not have to specify it in the ssh command. The public keys of all the machines authorised to access the remote machine via ssh are stored in `.ssh/authorized_keys`

There is a command that can copy a generated public key to a remote authorized_keys file automatically:

`ssh-copy-id [username]@[host/IP_address]`

If `ssh-copy-id` is not available you will have to do it manually. Password authentication must be enabled for this process:

```
cat ~/.ssh/id_rsa.pub
# prints your public key to the terminal output
ssh [username]@[host/IP_address]
# ssh into remote machine, password will be prompted
mkdir -p ~/.ssh
# creates .ssh folder on remote machine
nano ~/.ssh/authorized_keys
# opens the authorized_keys file, paste the key hash as one line and save the file.
exit
# exit ssh session
ssh [username]@[host/IP_address]
# test if it worked
```

To perform this process in one line of code (you will have to enter the password):

`cat ~/.ssh/id_rsa.pub | ssh [username]@[host/IP_address] "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"`

The file should have a new key on a new line, prefixed with it's type. The keytype will be *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2- nistp384*, or *ecdsa-sha2-nistp521*, *ssh-ed25519*.

Each entry will look like:

`[keytype] [long_hash] [user@machine_name]`


#### Windows 10



## The .ssh/config File

`C:\Users\[user]\.ssh\config`

Used to store ssh login details for various machines, this is a critical file when using Visual Studio Code

The file is set up as so:
```
Host vm1
  HostName 20.142.129.144
  User azureuser
  IdentityFile "C:\directory\to\key\vm1_key.pem"
Host vm2
  HostName 20.217.131.177
  User azureuser
  IdentityFile "C:\directory\to\key\\vm2_key.pem"
Host hack
  HostName team3.uksouth.cloudapp.azure.com
  User hacker3
```
Host - The label/name you are giving this connection.

HostName - The actual host or IP address.

User - Username

IdentityFile - The location of a public key file (*.pem* etc)

You can then ssh into one of the specified machines using `ssh [Host]`.

## Copying files to a remote machine

### scp
### sftp
### gui tools
#### vsc
#### filezilla
