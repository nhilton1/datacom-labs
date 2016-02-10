Analyze Linux Security Settings 
================================

In this lab, you will use command line tools to see what services are running, what user accounts exist, and what ports are open on a Linux computer.

Prerequisites
------------------
  - Vagrant installed and properly configured
  - Oracle VirtualBox installed

Create the Linux Virtual Machine
---------------------------------
Run the following commands to create a new Ubuntu server virtual machine and connect to it via SSH.

```
> vagrant init ubuntu/trusty64
> vagrant up
> vagrant ssh
```

Analyze Running Services
----------------------------------

In the terminal, run the following command to check the status of all services:

```
$ service --status-all
```

You should see output similar to the following:

```
 [ + ]  acpid
 [ - ]  apparmor
 [ ? ]  apport
 [ + ]  atd
 [ + ]  chef-client
 [ ? ]  console-setup
 [ + ]  cron
 [ ? ]  cryptdisks
 [ ? ]  cryptdisks-early
 [ - ]  dbus
 [ ? ]  dns-clean
 [ + ]  friendly-recovery
 [ - ]  grub-common
 [ ? ]  irqbalance
 [ ? ]  killprocs
 [ ? ]  kmod
 [ - ]  landscape-client
 [ ? ]  networking
 [ ? ]  ondemand
 [ ? ]  open-vm-tools
 [ ? ]  pppd-dns
 [ - ]  procps
 [ + ]  puppet
 [ ? ]  rc.local
 [ + ]  resolvconf
 [ + ]  rpcbind
 [ - ]  rsync
 [ + ]  rsyslog
 [ ? ]  screen-cleanup
 [ ? ]  sendsigs
 [ - ]  ssh
 [ - ]  sudo
 [ - ]  udev
 [ ? ]  umountfs
 [ ? ]  umountnfs.sh
 [ ? ]  umountroot
 [ - ]  unattended-upgrades
 [ - ]  urandom
 [ - ]  virtualbox-guest-utils
 [ ? ]  virtualbox-guest-x11
 [ - ]  x11-common
```

The `+` indicates it is running, `-` is stopped, and `?` is unknown.

Note that services typically run in the background. To get a list of processes running under the current user account, use the following command.

```
$ ps
```

To show every process (i.e. for all accounts), run the following command

```
$ ps -ef
```

Investigate Services
-------------------------------------
Some of the services might not be familiar to you. You can use the `man` command to read the help manual for many of the services by passing the services as a parameter. You may need to search the Internet to learn more about other services.

For example, run the following command to learn about the `acpid` service.

```
$ man acpid
```

Press `q` to quit the man program.

Do you think the acpid service is safe to run in a secure environment?

List Users
----------------------------------------
In many Linux distributions, the list of user accounts is stored in the file /etc/passwd. Use the following command to display just the usernames in that file:

```
$ cut -d: -f1 /etc/passwd
```

Run the following command to show the entire contents of /etc/passwd:

```
$ cat /etc/passwd
```

There are 7 columns of information, separated by colons (`:`).

1. Username
2. Password. If there is an 'x' character, it means the password is encrypted in /etc/shadow.
3. User ID (UID). 0 is reserved for root, 1-99 are for other predefined accounts, and 100-999 are for other system accounts.
4. Group ID (GID). The primary group ID referring to the groups in the /etc/group file.
5. User ID Info. Used for comments.
6. Home directory path
7. Command/shell. This is typically the absolute path to a shell (such as /bin/bash), but it does not have to be a shell.

Are there any user accounts that you think you can safely disable?


Disabling User Accounts
----------------------------
When individuals leave an organization, it is a best practice to immediately disable their accounts. After a period of time, the accounts can be deleted.

Run the following command to lock (i.e. disable) the `gnats` user account.

```
$ sudo passwd -l gnats
```

Run the following command to unlock the account.

```
$ sudo passwd -u gnats
```

Press the up arrow to cycle through your command history, and press enter when you find the following command:

```
$ cut -d: -f1 /etc/passwd
```

The `gnats` user will still be on the list.

Removing User Accounts
-----------------------------

Run the following command to delete the `irc` user:

```
$ sudo userdel irc
```

No message will be output. Press the up arrow to cycle through your command history, and press enter when you find the following command:

```
$ cut -d: -f1 /etc/passwd
```

The `irc` user should no longer be in the list.

Verifying Open Ports
----------------------------

The most reliable way to check open ports is to use port scanning software such as nmap. Run the following command to install nmap.

```
$ sudo apt-get install nmap
```

Run the following command to scan local ports. (Note the capital letter `O` [not zero]).

```
$ sudo nmap -sT -O localhost
```

Output should look similar to the following:

```
Starting Nmap 6.40 ( http://nmap.org ) at 2015-10-12 20:46 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000073s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
111/tcp open  rpcbind
```

In this example, two ports are open. What security vulnerability could exist on these two ports with the associated services?