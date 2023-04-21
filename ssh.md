# SSH - Secure Shell

SSH is used to connect to remote machines securely, usually on port 22. Data that is transferred is encrypted and can use public keys to authenticate a log in. This document won't explain how SSH and keys, work but it will tell you how to use them. You'll need a more advanced networking guide for that, I haven't the foggiest.

All hosts and IPs used as examples have been changed so they won't work and you can stop trying to force your way into my machines.

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

You can then connect to a remote machine via ssh. 

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

Keys look complicated but they are just a string of characters used instead of a password. Using a key to authenticate via ssh is far more secure than using password authentication; ssh is often used in malicious attacks that attempt to brute force their way into machines with various usernames and passwords. Key based authentication is done on the local machine so your authentication details are not exposed to the network; it is very secure and best practise.

When you generate an SSH key pair on a machine it creates a pair of keys - the public key and the private key. *Never share the private key.* The file for the public key is also known as the identity file. In order for a key-based authentication to pass, a local machine must know the public key of the remote machine. The remote machine must have the private key configured so that when the authentication is attempted, the two keys can view each other and pass, only the correct pairing will pass authentication.

SSH keys are a hash and can be saved in different file formats, [this link demonstrates the difference between *rsa* and *pem*.](https://hstechdocs.helpsystems.com/manuals/globalscape/eft7-3/mergedprojects/eft/server_ssh_key_formats.htm#:~:text=EFT%20imports%20the%20PEM%20format,key%20on%20a%20Linux%20computer.)

### Generating SSH Keys

To generate ssh key pairs:

`ssh-keygen`

You will be prompted with an option of where to save the key files (`/home/[user]/.ssh/id_rsa` or `C:\Users\[user]/.ssh/id_rsa`). Press enter to confirm. (The *.ssh* folder is where you will find the files used by the ssh client and server.) You will be prompted to enter a passphrase for the encryption generation, either leave it blank or choose a password and enter it twice. If you decide to make a password you will have to enter it whenever you use the key. The private key is id_rsa and the public key is id_rsa.pub.

Arguments and tags can be included in the ssh-keygen for more complex functionality. One such option is to specify the type of key generated with *-t*:

`ssh-keygen -t [keytype]`

Possible values are *dsa*, *ecdsa*, *ecdsa-sk*, *ed25519*, *ed25519-sk* and *rsa*.

Remember that a key will only work with it's pair, if you lose the credentials you will have to generate a new key pair and update all your remote systems with it.

#### Adding Keys to a Windows Account with ssh-agent

```
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\[key_file]
```

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

```
# Get the public key file generated previously on your client
$authorizedKey = Get-Content -Path $env:USERPROFILE\.ssh\[key_file]

# Set a script to copy the key to the remote authorized_keys file
$remotePowershell = "powershell Add-Content -Force -Path $env:ProgramData\ssh\administrators_authorized_keys -Value '$authorizedKey';icacls.exe ""$env:ProgramData\ssh\administrators_authorized_keys"" /inheritance:r /grant ""Administrators:F"" /grant ""SYSTEM:F"""

# Connect and run the script
ssh [username]@[host/IP_address] $remotePowerShell
```

Alternatively, do it manually. You're on Windows, use notepad or whatever.

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

So editing files and running commands in a remote shell is all well and good, but what if you want to copy files to a remote machine?

### SCP - Secure Copy

Built off SSH and RCP (remote copy), SCP is a tool for securely copying files that are encrypted during the transfer. SCP is often preinstalled but unlike SFTP and Rsync is not being actively developed and has fewer features. Generally they are better options than SCP if available.

To copy a file to a remote machine with scp:

`scp [relative/path/to/file] [user]@[remote_host]:/path/of/directory/to/save/file`

To copy from a remote machine:

`scp [user]@[remote_host]:/path/to/file "/path/of/directory/to/save/file"`

To copy a directory, the recursive tag is required:

`scp -r -C [relative/path/to/directory] [user]@[remote_host]:/path/of/directory/to/save/directory`

The *-r* is recursive and the *-C* compresses files whilst transferring, not essential but a decent idea for bigger transfers.

To copy between two remote systems:

`scp user@host1.com:/path/to/directory/file.txt user@host2.com:/path/to/other/directory`

If you want this routed through your local system, use the *-3* tag:

`scp -3 user@host1.com:/path/to/directory/file.txt user@host2.com:/path/to/other/directory`

### SFTP - Secure File Transfer Protocol

SFTP is an extension of SSH and is used for secure file transfers and management over a network.

You can connect to a remote machine using sftp instead of ssh as the command:

`sftp root@172.105.186.216`

Password and key authentication work in the same manner as normal. You will be entered into an sftp shell where you can input commands.

```
put - Upload a file
get - Download file
cd [dir] - Change remote directory to [dir]
lcd [dir]  - Change local directory to [dir]
pwd - Print the working directory of the remote machine
lpwd - Print the working directory of the local machine
ls - List remote working directory files
lls - List local working directory files
mkdir - Make remote directory
lmkdir - Make local directory
exit - Quit the shell
! - pop out of the shell until 'exit' is entered to the terminal
```

Download a file from remote machine with sftp:

```
# Log in via sftp, enter password if prompted
sftp root@host1
# Use cd and lcd to change the remote and local directories for the transfer
cd docs
lcd home/documents
# Use get to copy files to local machine
get [file]
exit
```

SFTP has other functionality but this covers the basics.

### Rsync - Remote Sync

Rsync only moves changed portions of files, smart eh? Set up key based authentication between two machines and Rsync can move files and keep directories synchronised. It's quite a useful backup tool.

To sync the contents of one folder to another on the same machine:

`rsync -a dir1/ dir2`

If you do not include the slash after dir1, dir1 itself will be copied into dir2 rather than its contents.

*-a* (archive) works similarly to *-r* (recursive) but also preserves symbolic links and special files.

To test an Rsync command:

`rsync -anv dir1/ dir2`

The *-n* tag executes the command as a dry run rather than in actuality,*-v* is verbose.

To push a local folder to a remote system:

`rsync -a ~/dir1 username@remote_host:destination_directory`

To pull a remote folder to a local system:

`rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine`

Other useful tags:

```

# Compresses uncompressed files during the transfer
-z
# Displays a progress bar and allows the resumption of an interrupted transfer
-P
# Ensures files are deleted from the synched folder when deleted from the original
--delete
# Excludes files from the sync matching the pattern
--exclude=[pattern]
# Stores backups of files in the specified directory
--backup --backup-dir=[directory]
```

### GUI Tools

There are plenty of applications that use ssh to connect to a remote machine in a more interactive manner than the traditional command line interface.

#### FileZilla

FileZilla is an old but perfectly capable application for transferring files between machines over various protocols, including SFTP. How to use FileZilla is fairly self-explanatory.

#### Visual Studio Code

If the *Remote Explorer*, *Remote - SSH*, *Remote - SSH: Editing Configuration Files* extensions are installed and the *.ssh/config* file has entries, you can use the Remote Explorer extension to edit remote files. Ensure the remote option is selected in the dropdown and select a remote to connect to over SSH, I'd suggest connecting in a new window. This is a great way in particular to edit code as an alternative to using nano or vim in an ssh shell, but the tool can also be used to copy files.
