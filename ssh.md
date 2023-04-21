# SSH - Secure Shell

SSH is used to connect to remote machines securely, usually on port 22. Data that is transferred is encrypted and can use public keys to authenticate a log in. 

SSH is a topic where there are some differences between Windows and Unix operating systems, though the ssh commands themselves will remain the same.

## Connecting via SSH

To connect to a remote machine via sssh:

`ssh [username]@[host/IP_adrees]`

Example (Apologies to whoever that ip leads to):

`ssh laportag@45.293.611.140`

You will be prompted to trust the host if it is the first time connecting, type yes and hit enter. You will then be prompted to enter the password, it will hide the characters you type, just type the password and hit enter. The terminal will take you to a shell for the remote machine where you can traverse the directory tree and enter commands.

Sometimes the ssh server of the remote machine is configured to a different port than the default 22 (for instance, 2222), in this case you will need to use the *-p* tag and specify the correct port:

`ssh -p [port] [username]@[host/IP_adrees]`

## SSH Keys

Often the ssh of a machine will be configured so that authentication by password is disabled, other times you will want to connect in a way that makes the password input is troublesome, at others you may just feel lazy. In these cases you can use an identity key instead of a password for authentication. 

Using a key to authenticate via ssh is far more secure than using password authentication; ssh is often used in malicious attacks that attempt to brute force their way into machines with various usernames and passwords. Key based authentication is done on the local machine so your authentication details are not exposed to the network; it is very secure and best practise.

When you generate an SSH key pair on a machine it creates a pair of keys - the public key and the private key. *Never share the private key.* The file for the public key is also known as the identity file. In order for a key-based authentication to pass, a local machine must know the public key of the remote machine. The remote machine must have the private key confgured so that when the authentication is attempted, the two keys can view each other and pass, only the correct pairing will pass authentication.

SSH keys differ between Windows and Linux systems. Read both sections for full understanding because some things overlap and I don't want to write everything twice.

### Connecting with a Key File

To connect to a remote machine via ssh using key based authentication:

`ssh -i [relative/path/to/identity/file] [username]@[host/IP_adrees]`

The path is relative to the direcctory the terminal is working in,

### Generating SSH Keys

#### Linux

To generate ssh key pairs on linux:

`ssh-keygen`

You will be prompted with an option of where to save the key files `/home/[user]/.ssh/id_rsa`. Press enter to confirm. (The *.ssh* folder is where you will find the files used by the ssh client and server.) You will be prompted to enter a passphrase for the encryption generation, either leave it blank or choose a password and enter it twice. If you decide to make a password you will have to enter it whenever you use the key. The private key is id_rsa and the public key is id_rsa.pub.

#### Windows

Key files in Windows are either *.pem* or *.ICantRemember* Windows does not have built in functions to create ssh keys so we have to get more creative.

### Copying a Key to a Remote Machine

#### Linux

If you copy your public id to the remote machine, you do not have to specify it in the ssh command. The public keys of all the machines authorised to access the remote machine via ssh are stored in `.ssh/authorized_keys`

There is a command that can copy a generated public key to a remote authorized_keys file automatically:

`ssh-copy-id [username]@[host/IP_adrees]`

If `ssh-copy-id` is not available you will have to do it manually. Password authentication must be enabled for this process:

```
```

To perform this process in one line of code (you will have to enter the password):

`cat ~/.ssh/id_rsa.pub | ssh [username]@[host/IP_adrees] "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"`

#### Windows

## config file

used to store ssh login details for various machines. 

## authorized_keys


