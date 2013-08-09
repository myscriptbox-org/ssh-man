# man page for ssh-man
## NAME 
**ssh-man** manages the ssh key database and ssh links to remote accounts

## SYNOPSIS
**ssh-man** [objectPath] [ objectId1 ... objectIdN ] -action [ actionArg1 .. actionArgM]

## DESCRIPTION 
### config.batch-mode
Only if all conditions for **BatchMode** are fulfilled, you will be able to execute scripts in a remote account without supplying a password. Otherwise, the attempt will either fail or else wait indefinitely for user input. It is not a good practice to build systems that execute automated actions on other servers with no guarantee that ssh will not wait for user input.

### db
Use this object path to manipulate the entire database in one go, mostly for backing up, restoring, or deleting the entire database of security credentials.

### stash
before making changes to the database, you may want to ensure that you can undo your changes. Stash your database. At any time you can always unstash back its stashed version.

### known-host
A known host is a server domain name along with its IP address for which you have retrieved the public key previously. From there, as a security measure, ssh will expect the known host to keep its existing IP address and its existing key. 

### known-hosts
Use actions at the level of the **known-hosts** can manipulate the entire known hosts collection with one command.

### own-keys
Use actions at the level of your **own-keys** to manipulate both private and public key.

### own-keys.email
The **own-keys** have hold an email address. Use actions at this level to manipulate the email address for your own keys.

### own-keys.private
Use actions at this level to manipulate your own private key.

### own-keys.public
Use actions at this level to manipulate your own public key.

### remote-account
A **remote account** is an account on a remote server for which you have username and password. You can create or remove a link between your own device and this remote account. A link is tied to your own private key.

### remote-accounts
Use actions at this level to manipulate the entire collection of **remote accounts**. One way of manipulating them, could consist in creating links for all accounts, then back up your db, and then delete the passwords. Do not ask me where to store your backup with passwords safely. The problem is inherently unsolvable. And yes, it may be a problem to remember them. And yes, you may need these passwords again, even after creating and verifying all the links to the remote accounts.

## OPTIONS

### --help
shows the general help paragraph.

### [objectpath] --help
shows general help for the object path.

### --version
shows the program's version.

### --license
shows the program's license.

### config.batch-mode -set
This command enables batch mode in the user's configuration. Batch mode prevents **ssh** from waiting for user input, causing the script to wait forever for user input that may never come.

### config.batch-mode -show
This command outputs **yes** if batch mode is enabled and **no** if not. Batch mode prevents **ssh** from waiting for user input, causing the script to wait forever for user input that may never come.

### config.batch-mode -unset
This command disables batch mode in the user's configuration. It is used by the '**ssh-man** remote-account -link' command to supply the password required for linking a remote server. In batch mode, it is not possible to supply a password to **ssh**.

### config.batch-mode -check
This command will check if all conditions are met for ssh to be able to run in batch mode.
Example output:  
    agent running        yes  
    key loaded           yes  
    batch mode enabled   no  
    overall              nok  
**agent running**: **no** means that the authentication agent, **ssh-agent**, is not running. Consequently, it will not supply the private key to the **ssh** program when it needs to authenticate to a remote ssh server. In batch mode **ssh** will fail. In interactive mode, **ssh** may stop for user interaction.  
**key loaded**: **no** means that the authentication agent has not loaded the private key. It needs to be loaded with **ssh-add**.
**batch mode enabled**: **no** means that **ssh** will potentially stop for user interaction when dealing with a remote ssh server that is not a known host or which has not been linked through key exchange.  
**overall**: **no** means that one of the conditions required, has not been satisfied.

### db -backup [arg]
This command creates a zip backup of the ssh user-level configuration database and saves it to the filepath supplied in [arg].
Examples:  
    **ssh-man** db -backup mybackup  
will create mybackup.zip  
    **ssh-man** db -backup mybackup.zip  
will also create mybackup.zip  
The backup zip contains all user-level configuration database files used by **ssh**.

### db -delete
This command deletes the entire ssh user-level configuration database.

### db -unstash
This command puts back the copy of the ssh user-level configuration database made earlier with '**ssh-man** db -stash'. Use it if you want to undo the changes made since the last time you stashed the database.

### db -restore [arg]
This command restores the ssh user-level configuration database from the zip file supplied as an argument. Example:  
    **ssh-man** db -restore mybackup.zip  
will restore mybackup.zip.

### db -stash
This command will create a local copy of the ssh user-level configuration database. Use it if you are not sure what effect your changes will have. You can put back the version stashed with: '**ssh-man** db -unstash'.

### known-host [obj] -add
This command adds a known host to the database. Example:  
    **ssh-man** known-host yahoo.com -add  
If the known-host exists already, it will be replaced with its current IP address and current SSH key.

### known-host [obj] -delete
This command will delete a known host. Example:  
    **ssh-man** known-host myserver.com -delete  
It will remove the known-host from the ssh user-level configuration database. ssh also stores the IP address for a host. If the IP address for the host has changed, you will have to remove this record and add the new record with '**ssh-man** known-host myserver.com -add'. Otherwise, ssh will refuse to connect to this host.

### known-host [obj] -fingerprint
This command prints the public key finger print for the known host.

### known-hosts -find [arg]
This command checks if a known host is registered in the database. If so, it returns the fingerprint of its public key. If not, it returns nothing.

### known-hosts -append [arg]
This command appends the known hosts from a backup file to the database.

### known-hosts -delete
This command deletes all the known hosts in the database.

### known-hosts -replace [arg]
This command replaces the known hosts in the database from the known hosts in a backup file. Example:  
    **ssh-man** known-hosts -replace mybackup.zip  

### own-keys -replace [arg]
This command replaces the own keys in the database by the ones in the given backup file. Example:  
    **ssh-man** own-keys -replace mybackup.zip

### own-keys -show
This command shows the fingerprint of the public key in the database.

### own-keys -create
This command creates a new set of keys, private and public. If the database already has keys, they will be replaced.

### own-keys.email -show
This command shows the email registered in the own public key. Example:  
    $ **ssh-man** own-keys.email -show  
    john@doe.com

### own-keys.email -set [arg]
This command sets the email address for a public key. Example:  
    **ssh-man** own-keys.email set joe@doe.com

### own-keys.private -show
This command outputs the own private key. 

### own-keys.public -show
This command outputs the own public key. 

### remote-account [obj] -verify-pwd
This command verifies if a password is still valid. Example:  
    **ssh-man** remote-account john@doe.com -verify-pwd  
The output is **valid** or **invalid**.

### remote-account [obj] -unlink
This command unlinks a remote account. Example:  
    **ssh-man** remote-account john@doe.com -unlink  
It does this by removing the public key from the account's authorized keys. From there, passwordless login will no longer be possible from this database.

### remote-account [obj] -verify-link
This command verifies if a link is still valid. Example:  
    **ssh-man** remote-account john@doe.com -verify-link  
The output is **valid** or **invalid**.

### remote-account [obj] -delete-pwd
This command deletes the password for a remote-account from the database. Example:  
    **ssh-man** remote-account john@doe.com -delete-pwd  
It does not delete the link to this remote account, if the link exists.

### remote-account [obj] -set-pwd [arg]
This command adds or replaces a remote account. Example:  
    **ssh-man** remote-account john@doe.com -set-pwd 'john125!'  
Use quotes around the password if it contains non alphanumeric characters. From there, passwordless login will be possible from this database.

### remote-account [obj] -show
This command shows the status of a remote account. Example:  
    **ssh-man** remote-account john@doe.com -show  
    remote account      : john@doe.com  
    password            : john125!  
    pwd valid           : valid  
    link valid          : invalid  
The command also verifies if the password is valid and if the link is valid.

### remote-account [obj] -link
This command will link the device to a remote account. Example:  
    **ssh-man** remote-account john@doe.com -link  
This is effected by copying the public own key to the authorized keys in the remote account. The result is that you may login from this device into the account on the other device without supplying a password.

### remote-accounts -unlink
This command unlinks all remote accounts in the database.

### remote-accounts -verify-links
This command verifies the links for all remote-accounts in a database.

### remote-accounts -link
This command links all the remote accounts in the database.

### remote-accounts -delete-pwd
This command deletes all the passwords for the remote accounts from the database. It does not delete their links, however.

### remote-accounts -append [arg]
This command appends remote accounts from a backup file to the database. Example:  
    **ssh-man** remote-accounts -append mybackup.zip

### remote-accounts -replace [arg]
This command replaces the remote accounts in the database by the ones in a backup file. Example:  
    **ssh-man** remote-accounts -replace mybackup.zip

### remote-accounts -show
This command lists all the remote accounts in the database. Example output:  
    Remote Account      Password 
    john@doe.com        john125!     
    john@myserv.com     john122??

### remote-accounts -verify-pwd
This command verifies the passwords for all remote accounts in the database.

### stash -delete
This command deletes the stash.

### stash -exists
This command checks if the stash exists. It outputs "yes", if it exists, and "no", if it doesn't.


## ENVIRONMENT 
### SSH_FOLDER
By default, **ssh-man** will look for the ssh database in ~/.ssh. You can override this with the SSH_FOLDER environment variable. Example:  
    SSH_FOLDER=/var/test/myssh **ssh-man** ...  
Setting the variable just before invoking **ssh-man** will point the SSH_FOLDER elsewhere.
### SSH_STASHED_FOLDER
By default, **ssh-man** will stash the database in ~/.ssh.stashed. You can override this with the SSH_STASHED_FOLDER environment variable. Example:  
    SSH_STASHED_FOLDER=/var/test/mysshstash **ssh-man** ...

## EXIT-STATUS 
### 0
A zero exit code means that everything went ok.
### 1
A one exit code is usually an error generated by **ssh-man** itself.
### other
Another exit code is always an error generated by one of the underlying programs invoked by **ssh-man**.

## FILES 
By default, **ssh-man** looks for the ssh security database in **~/.ssh**.
### config
This file contains the user-level configuration options for **ssh**; **ssh-man** switch on and off **BatchMode** by adding or removing the corresponding configuration option in this file.
### id_rsa
This file contains the own private ssh key. This file must be present for approximately anything in **ssh** to work properly.
### id_rsa.pub
This file contains the own public ssh key. This file is copied to the remote account's **authorized_keys** file when establishing a permanent link.
### known_hosts
For each remote server, it contains an entry with their key and IP address. For **BatchMode** to function properly, the remote server's public key must be present in this file.
### remote_accounts
This file contains one entry per remote account along with its password. You can backup and remove the file after establishing the permanent remote links. If you ever need to re-establish the links, you will need to bring back this file.

## AUTHOR
Erik Poupaert <erik@sankuru.biz>

## REPORTING-BUGS
Report bugs to: erik@sankuru.biz

# COPYRIGHT
Licensed under: GPL
