# SSH - Secure Shell

SSH is used to connect to remote machines securely, usually on port 22. Data that is transferred is encrypted and can use public keys to authenticate a log in. 

## Connecting via SSH

To connect to a remote machine via sssh:

`ssh [username]@[host/IP_adrees]`

Example:

`ssh pi@45.293.611.140`

You will be prompted to trust the host if it is the first time connecting, type yes and hit enter. You will then be prompted to enter the password, it will hide the characters you type, just type the password and hit enter. The terminal will take you to a shell for the remote machine where you can traverse the directory tree and enter commands.

Sometimes the ssh server of the remote machine is configured to a different port than the default 22, in this case you will need to use the *-p* tag and specify the correct port:

`ssh -p [port] [username]@[host/IP_adrees]`

## SSH Keys

Often the ssh of a machine will be configured so that authentication by password is disabled, other times you will want to connect via a script and the password input is troublesome, at others you may just feel lazy. In these cases you can use an authentication key instead of a password to log in. 

Using a key to authenticate via ssh is far more secure than using password authentication; ssh is often used in malicious attacks that attempt to brute force their way into machines with various usernames and passwords.



