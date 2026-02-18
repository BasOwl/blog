# [2026-18-02] Recursive Descent Parser
A recursive descent parser is a "top-down parser that procesess input based on a set of recursive functions, where each function corresponds to a grammar rule"

This is done on the input from left to right and constructs a "parse tree" by matching the production rules.




# [2025-25-11] Day 53pt2: Network Troubleshooting Toolkit

## Task 1
I created a net_lab folder in my home directory and then created a report.txt file to keep notes

The first thing I did to get reachability, HTTP statis code and headers was to use curl with -i:
```
curl -i URL > curl_test.txt
```

To list the folder we use ls -l or ls -ltr

The command to lookup the hostname of a website is host WEBSITE NAME. I did this and outputted it into a file called host_test.txt

## Task 2

The second task required me to save a socket snapshot using ss and its various arguments to get specific protocols

I was required to find both TCP and UDP and did this by using ss -u and ss -t respectively

I then append this to a file with >>

## Task 3

For this I was required to check external availability similarily to task 1 however I was also tasked with acquring headers, status and timing metrics seperately

The first thing required was the header of a page using curl, we can get this and add it to a file by using curl with --head and using > or >> to append it to a file

The second was to get the status code of a page by using curl --head but then we have to use awk if we want to get just the status code:
```
curl --head https://google.com | awk '/^HTTP/ { print $2 }' >> curl_status.txt
```

HTTP means it will get any line that begins with HTTP caret^ being the character that defines this, this seperates by whitespace so $2 would be the 2nd field, being the status code

For timing metrics we have to use -w after the link like so:

```
curl https://google.com -w 
```

But we need to give -w something to output and we do this like so:

```
curl -o /dev/null https://google.com -w "namelookup: %{time_namelookup}\nconnect: %{time_connect}\nappconnect: %{time_appconnect}\npretransfer: %{time_pretransfer}\nstarttransfer: %{time_starttransfer}\ntotal: %{time_total}\n"
```
We include -o /dev/null to output the garbage data we dont need into the void. We have usable data but to clean it up and remove the progress bar we most make curl run silently:

```
curl -s -o /dev/null https://google.com -w "namelookup: %{time_namelookup}\nconnect: %{time_connect}\nappconnect: %{time_appconnect}\npretransfer: %{time_pretransfer}\nstarttransfer: %{time_starttransfer}\ntotal: %{time_total}\n"
```
We then append this to a file of out choosing, in our case we do >> curl_timing.txt

## Task 4 

We are tasked with confirming our SSH client and capturing the config preview, we get the version by using ssh -V >> ssh_version.txt

To get the config and test localhost without connecting we use ssh -G localhost >> ssh_config.txt

# [2025-25-11] Day 53: Curl and Ping commands

- curl is Linux's way to interact with urls, it will bring back the content of a url. curl -O URL will download the contents of a url

- ping works with the ipaddress and doesn't require the site to be online

# [2025-11-10] Day 52pt2: The SS Command

The SS command is a tool used to "dump socket statistics and display information" similar and faster but similar to netstat. It can display more TCP and state information

## TCP UDP AND UNIX

TCP (Transmission Control Protocol) is a set of rules that ensures data is sent successfully between two computers over the net

UDP (User Datagram Protocol) is a way for sending data over the internet without checking if it arrives correctly

UNIX is a way for programs on the same computer to talk to each other, it uses a special file for messages and is used in databases, web servers and local services without internet

## SS Command history

In 2010 the SS command was introduced in the iproute2 package to replace netstat
In 2016 SS gained more importance in network management and gained expanded support for additional socket types and protocols
In 2024 SS remains a key tool for Linux network management

SS can be used with -t to show only TCP connections, with -u to see only UDP connections and -x to get only UNIX 

# [2025-11-10] Day 52: SSH Keys

There are two main reasons we would want to use password-less entry through SSH:

- Repetitive logins

- Automation through scripts 

SSH Keys are generated at the user level:

- USER

- ROOT

These are then copied from the client to the server

For us to create ssh keys and copy them to the server we have to:

- Generate the key with "ssh-keygen"

- Copy the key to the server with "ssh-copy-id root@SERVERIP"

- Login to the server with "ssh root@SERVERIP"

# [2025-11-06] Day 51: SSH and Telnet

- Telnet is a un-secured connection between computer

- SSH is a secured connection between two machines

There are two packages for these services:

- Client package

- Server package 

## Configuring and Securing SSH

SSH stands for Secure Shell. A shell provides you with an interface to the Linux system and translates your commands to the kernel which then interacts with the hardware

Open SSH is a package/software

Its service daemon is sshd, it defaults to port 22

SSH itself is secure as it's encrypted but there are additional configurations that can be done to increase the security:

The first being IDLE TIMEOUT INTERVAL:

This is to avoid having an unattended SSH session and we can set this up by doing the following:

- Login as root, edit /etc/ssh/sshd_config and add "ClientAliveInterval TIMEINSECONDS" and "ClientAliveCountMax 0", Then restart the ssh demon with "systemctl restart sshd"

The second being disabling root login through SSH:

- Login as root, edit /etc/ssh/sshd_config to replace PermitRootLogin yes to no, restart the daemon with systemctl restart sshd

The third being the removal of the ability to login to accounts with empty passwords:

- Login as root, edit /etc/ssh/sshd_config and remove the # to uncomment the line with "PermitEmptyPasswords no", restart the daemon

The fourth being to limit the users that can be accessed by SSH:

- Login as root, edit /etc/ssh/sshd_config and add "AllowUsers USERNAME1 USERNAME2", restart the daemon

The fifth being to use SSH on a different port:

- Login as root, edit /etc/ssh/sshd_config and uncomment the line with "Port 22" and we can change that to select a different port, then we have to restart the service



# [2025-10-29] Day 50: New Network Utilities (mntui, nmcli, nm-connection-editor, GNOME)

NetworkManager is a "service that provides a set of tools designed specifically to make it easier to manage the networking configuration on Linux systems." It is the default on RHEL 8 and 9

- Different tools: GUI, nmtui, nmcli

## Network Config Methods

- nmcli - "network manager command line interface" This is useful when access to a gui is not available and can be used in scripts to make network config changes

- nmtui - "network manager text user interface" This can be run without a terminal window and allows changes to be made by making menu selections and entering data

- nm-connection-editor This is a full graphical management tool providing access to most of the NetworkManager configuration options

- GNOME Settings This is the network screen of the GNOME desktop enviroment's settings application and allows basic network management tasks to be performed


Using nmtui we can create a newer bond by using a new method called Team. We give a profile name, add slave adapters through either wifi or ethernet and we can give each device a static ip. We can also assign a static ip to the overall team

Using nmcli we can do multiple things such as assigning a static ip:
```
nmcli connection modify enp0s3(NETWORK ADAPTER NAME) ipv4.addresses IPADDRESS/24 (24 is the subnet mask)
nmcli connection modify enp0s3(NETWORK ADAPTER NAME) ipv4.gateway GATEWAYIP
nmcli connection modify enp0s3(NETWORK ADAPTER NAME) ipv4.method manual
nmcli connection modify enp0s3(NETWORK ADAPTER NAME) ipv4.dns 8.8.8.8
nmcli connection down enp0s3 down
nmcli connection down enp0s3 up
```

We can also use nmkcli to assign a secondary static ip:
```
nmcli connection modify enp0s3 +ipv4.addresses IPADDRESS/24
```

# [2025-10-21] Day 49pt2: NIC Bonding

NIC is the network interface card, usually an ethernet port

NIC bonding is the combination of multiple NICs into one

The purpose of bonding is to increase availability and add redundancy 

The command we use for this is:
```
modprobe bonding 

modinfo bonding
```

Create /etc/sysconfig/network-scripts/ifcfg-bond0 

Edit /etc/sysconfig/network-scripts/ethernet1
Edit /etc/sysconfig/network-scripts/ethernet2

We point eth1 and eth2 at bond0 and then Restart network with "systemctl restart network"

In our bond0 file we decide on a mode in "BONDING_OPTS" and those modes are:
```
| Mode | Policy                        | How it works                                                                                                                                                                                                                              | Fault Tolerance | Load Balancing |
|:----:|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------:|
| 0    | Round Robin                   | Packets are sequentially transmitted/received through each interface one by one.                                                                                                                                                          | No              | Yes            |
| 1    | Active Backup                 | One NIC active while another NIC is asleep. If the active NIC goes down, another NIC becomes active. Only supported in x86 environments.                                                                                                  | Yes             | No             |
| 2    | XOR (Exclusive OR)            | The MAC address of the slave NIC is matched against the incoming request’s MAC. Once the connection is established, the same NIC is used to transmit/receive for the destination MAC.                                                     | Yes             | Yes            |
| 3    | Broadcast**                   | All transmissions are sent on all slaves.                                                                                                                                                                                                 | Yes             | No             |
| 4    | Dynamic Link Aggregation      | Aggregated NICs act as one NIC, resulting in higher throughput and providing failover if a NIC fails. Requires a switch that supports IEEE 802.3ad.                                                                                       | Yes             | Yes            |
| 5    | Transmit Load Balancing (TLB) | Outgoing traffic is distributed depending on the current load of each slave interface. Incoming traffic is received by the current slave. If the receiving slave fails, another slave takes over the MAC address of the failed slave.     | Yes             | Yes            |
| 6    | Adaptive Load Balancing (ALB) | Unlike Dynamic Link Aggregation, Adaptive Load Balancing doesn’t require switch configuration. Only supported in x86 environments. Receiving packets are load balanced through ARP negotiation.                                           | Yes             | Yes            |

miimon:
Specifies the MII link monitoring frequency in milliseconds.  
This determines how often the link state of each slave is inspected for link failures.

```



# [2025-10-21] Day 49: Network Interface Card Information 

To find out information about the NIC we can use the command:
```
ethtool NICNAME
```

We can find the name of the mahcine's NICs by typing "ip a"

We will see another NIC which is "lo" This is a "loopback" device and is a special interface that the machine uses to talk to itself. Mainly for diagnostics and connecting to local servers.

Virtual machines with a network bridge may also have "virb0"


# [2025-10-19] Day 48pt2: Network Files and Commands(ping, ifup, ifdown, netstat, tcpdump)

- Interface Detection

- Assigning an IP Address

## Interface configuration files: 

- /etc/nsswitch.conf, /etc/hosts, /etc/sysconfig/network, /etc/sysconfig/network-scripts/ifcfg-nic, /etc/resolv.conf,

- /etc/hosts: We can add IPs here with hostnames in order to do things like: ping hostname and it will take the ip from hosts and run the command

- /etc/sysconfig/network: This file specifies additional information that is valid to all network interfaces on the system. 

- /etc/sysconfig/network-scripts: ifcfg-DEVICENAME has the network device config. We can change the BOOTPROTO to static to assign an ip to certain devices

- /etc/resolve.conf: Specifies the DNS server

## Network Commands

- ping
- ipconfig OR ip a
- ifup ifdown
- netstat: a tool for managing and monitoring the status of your system's interfaces, routes, and connections
- tcpdump: is a data-network packet analyzer program. It allows the user to display TCP/IP and other packets being transmitted or received over a network to which the computer is attached.


# [2025-10-19] Day 48: Network Components:

- IP means "Internet Protocol" and is a unique string of numbers seperated by periods that identifies each computer: "192.168.1.1" 

- Subnet Mask is "a 32-bit number that masks an IP Address and divides the IP into network and host addresses. The Subnet Mask is made by setting network bits to all "1"s and host bits to all "0"s

- Gateway tells the computer which route to pick to send traffic out and recieve traffic

- Static vs DHCP Static is an ip that is permanentely assign to a network device. DHCP is "Dynamic Host Configuration Protocol" and automatically manages IP address allocation on a network

- Interface: This is usually a Cat 5 ethernet port

- Interface MAC: The interface has a permanant MAC address. Example MAC Address: 3A-34-52-C4-69-B8 Organizationally Unique Identifier: 3A-34-52 Network Interface Controller: 69-B8 

# [2025-10-14] Day 47: Client & Server Relationship

The Client:

"The client is a computer or a program that relies on sending a request to another program or computer that accesses a service made available by a server"

- Web Browsers

- Mobile apps

- Other software/ apps

The Server:

"A Server is a piece of hardware or software that provides functionality for other programs or devices"

- Physical machine

- Services: Web hosting, database management, file sharing


There are a few ways for Servers and Clients to communicate but two notables ones are: HTTP or TCP/IP

# [2025-10-14] Day 46: Shell command history

history in the cli prints our entire command input history

We can make it more digestable by piping history into more:
```
history | more
```

We can find a line number of a command we wish to reuse and instead of copying and pasting the command we can do:
```
!LINENUMBER
```

If we want to search for a certain command we can pipe history into grep:
```
history | grep "command"
```

# [2025-10-12] Day 45pt 2: Aliases

Aliases are the more common way of doing what I did with the wwh script in shell-scripting-intro

An example of setting l to do ls -ltr would be:
```
alias l="ls -ltr"
```
So when we type l, we will instead get the ls -ltr command

If I were to redo the wwh command as an alias it would look like:
```
alias wwh="whoami; hostname; pwd"
```

A useful one to assign executable permissions to the shell scripts we have been doing would be:
```
alias perms="chmod a+x"
```

To remove an alias we do:
```
unalias NAME
```

## User or Global Aliases

Aliases that we created above will not remain between users, this is because there are 2 types of aliases.

There are User and Global Aliases.

Global aliases can be used by anyone on a system.

User Aliases are at: home/user/.bashrc
If we edit this file and add an alias here, it will persist through sessions.

Global Aliases are at: etc/bashrc


# [2025-10-12] Day 45: Checking Remote Server Connectivity

- script to check status of a remote host

```
ping -c1 192.168.12.1
        if [ $? -eq 0 ]

        then
        echo OK
        else
        echo NOT OK

        fi
```

No Output:
```
ping -c1 192.168.12.1 &> /dev/null
```

With variables:
```
hosts = 192.168.12.1

ping -c1 $hosts &> /dev/null
        if [ $? -eq 0 ]

        then
        echo $hosts is OK
        else
        echo $hosts is NOT OK

        fi
```

# [2025-09-07] Day 45: Scripting: CASE STATEMENTS

Case statements are similar to switch statements in other languages

An example of case statemnts would be:
```
case WORD in 
  PATTERN1)
    echo "This is pattern 1"
    ;; #signals the end of the block

  PATTERN2)
    echo "This is pattern 2"
    ;;

  *)
    echo "This is everything else
    ;;

  esac
```
")" after the pattern is a seperator of the pattern and the code in the block


# [2025-09-01] Day 44: Scripting: I/O, if-then, for loops, do-while, cast statements

To create a script that would take input from the user we can use:

- read 
Read takers user input and assigns it to the variable that we determine with the read command
So when we use read we always have to give a variable that the input is going to be assigned to:
```
read VARIABLENAME
```

For us to use a variable that runs a command we have to define it in a script like so:
```
a= `COMMAND`
```

## if-then scripts

If do this

else do this

An example of a shell script using an if statement:
```
#!/bin/bash
a=100
if[$a -eq 100]
then
  echo a is 100
else
  echo a is not 100
fi

```
We then have to make it executable with chmod a+x

## for loop scripts

For loops are loops that will keep running until a variable is met

An example script of a for loop is:
```
#!/bin/bash

for i in 1 2 3 4 5
do
  echo "Test $i times"
done
```
done is how we close the loop

As with all shell scripts we have to set the script to be executable with chmod a+x

## do-while scripts

The while statement continually executes a block while a determined condition is met

```
#!/bin/bash

count=0
num=10
while [ $count -lt 10]
do
  echo "$num"
  ((count++))
```

# [2025-08-31] Day 43: Linux Kernel, Shell, Shell scripting

The linux kernel is the interface between hardware and software

A shell is like a container, an interface between users and the kernel. The CLI is a shell.

We can find our shell in three ways:
```
echo $0
cat /etc/shells
/etc/passwd
```

The types of shells are:

- Gnome has a gui

- KDE has a gui

- sh is a CLI

- bash is a CLI, a modernised version of sh

- csh and tcsh are C based shells

- ksh 

## Shell Scripting

Returning to shell scripting we have:
What is a shell script?

A shell script is an executable file containing shell commands that are executed sequentially. Common things it will contain is:

- Shell (#!/bin/bash)

- Comments (# comment)

- Commands (echo, cp, grep)

- Statements (if, while, for)

The file needs to be given execute permissions in order to be touched

The file has to be called from an absolute path, can be called in current with ./script.bash


## Ideas for shell scripts

- Output to the terminal with echo

- Creating tasks 

- Telling your id

- Location

- Creating files/dirs

- System info

- Output to a file with ">"

- Filter/ Process text through things like cut, awk, grep, sort, uniq, wc

## Shell script variables

We can use and assign variables in our scripts by assing as normal:

a = something

We can then access this elsewhere like this:

echo "our variable of a is $a"




# [2025-08-23] Day 42pt2: tmux (Terminal Multiplex Command)

tmux is a modern and dynamic alternative to screen

Like screen, it is a terminal multiplexer for linux. It allows multiple terminal sessions to be accessed simultaneously in a single window. Mostly used for running more that one command line at a time

tmux is currently the most popular multiplexer and is the case for centOS and red hat

A major use for this is it's session persistance and the ability to disengage from it and reengage with processes still running

We have to install tmux with:
```
dnf install tmux -y
```

The bar at the bottom of the session tells us a few things

- [n] Tells us the current session number

- n:bash Tells us the window number

- * Tells us that this is the active window

To split the window vertically in tmux we do
```
CTRL + B
SHIFT + %
```

To split the window horizontally we do
```
CTRL + B
SHIFT + "
```

To move between terminals we do CTRL + B and then an arrowkey

To detach from a session we do
```
CTRL + B + D
```

To see all of our tmux sessions we do
```
tmux ls
```

To continue from where we were we do
```
tmux a
```

To create a new session with a name we do
```
tmux new -s NAME
```

To open a new window we do
```
CTRL + B + C
```

To switch between the newly created windows we do
```
CTRL + B + N
```

CTRL + D to close sessions

To rename a window we press CTRL + B + ,

To terminate sessions we type tmux kill-session -t SESSIONNAME




# [2025-08-23] Day 42: Screen command

- GNU Screen
Can be used to manage multiple terminal sessions

To use screen we have to install it with:
```
dnf install epel-release
dnf install screen
```

Using screen will open a new terminal with a unique number
```
CTRL + a
shift + | 
```
Will split the screen vertically

```
CTRL + a
shift + s
```
Will split horizontally 

```
CTRL + a 
tab
```
Will move between active windows

```
CTRL + a
c
```
This will activate the window we are in if it is not yet active



# [2025-08-22] Day 41pt4: Enviroment Variables

Enviroment variables are dynamic-named values that can affect the way running processes behave on a computer. They are a part of the enviroment in which a process runs

Another way to see them is: a set of defined rules and values that determine how to build an enviroment

We can see the enviroment variables on our machine by typing env
This will show us things such as:
```
root@localhost:~# env
SHELL=/bin/bash
HOME=/root
```
Our home here is /root because we are logged into root

We can check env variables by using printenv EXAMPLE

We can also use $USER to access variables, we can do things such as:
```
echo $USER
```
To print what the value is

## Creating env variables

To create an enviroment variable we use the export command:
```
export VAR=test
printenv VAR
```
And this will output test, showing that we have created a new TEMPORARY variable in env
We can also use export to edit enviroment variables for the session

To PERMANANTELY change an enviroment variable or create a persistant one we must first Vim .bashrc and then add:
```
export VARIABLENAME="VARIABLE"
:wq
```
We then have to source .bashrc and this will update the enviroment variables but only for this user.

## Global env variables

cd etc and then vim enviroment and we then add another export VARIABLE="VARIABLEDATA"
we then source enviroment and we can check with echo $VARIABLE

to remove a variable we use the unset command


# [2025-08-22] Day 41pt3: Root password recovery

- Restart the computer

- Edit grub
On certain versions of linux we have to edit grub from read only to read write or rw
```
rw init=/sysroot/bin/sh
CTRL-x
chroot /sysroot
passwd root
touch /.autorelabel
exit
reboot
```

On CentOS / Red Hat 9 we add, at the end of linux ($root), rd.break


# [2025-08-22] Day 41pt2: Terminal Commands (clear, exit, script)

- clear
clears the screen

- exit
exits out of the shell, terminal or user session

- script
The script commands stores terminal activities in a log file that can be named by a suer, when a name is not provided, the default name is typescript

# [2025-08-22] Day 41: Terminal Control Keys

The 4 most commonly used control keys on Linux are:


- CTRL-u - erases everything typed in the cli

- CTRL-c - stops/kills a running command

- CTRL-z - suspends a running command

- CTRL-d - exit from an interactive program (signals end of data)


# [2025-08-18] Day 40 pt5: SOS report

An SOS report collects and packages diagnostic and support data. The command to do it is sosreport

This is done in order to send a report to redhat and we need to contact them to get a case number

The file it creates can contain sensitive information so we must check before sending if this is the case or not


# [2025-08-18] Day 40 pt4: Finding system architecture (arch)

There are differences between 32-bit and 64-bit CPUS

64 bit programs cannot run on 32 bit so we may run into issues between the two

To find out processor information on linux we use the arch command

We can also use uname -a

# [2025-08-18] Day 40 pt3: Finding System Information (uname, dmidecode)

```
cat /etc/redhat-release

uname -a

dmidecode
```
This tells us what is underneath the operating system.

uname -a is more verbose and tells us hostname, kernel version etc

dmidecode | more will let us read it page by page 

hostname and uname -a are the most useful and commonly used of the commands.

# [2025-08-18] Day 40 pt2: Changing System Hostname (hostnamectl)
The code used is:
```
hostnamectl - set-hostname newhostname
```
We can also just edit /etc/hostname in Vi to change it too


In Linux V7 it is editing /etc/hostname

V6 edits /etc/sysconfig/network

# [2025-08-18] Day 40: System Maintenace (shutdown, init, reboot, halt)

- shutdown
Gentle shutdown that waits for things to finish closing

- init 0-6
Has different levels, 0 shutsdown, 6 reboots, 3 is multi-user mode

- reboot
Restarts

- halt
Immediate shutdown


# [2025-08-17] Day 39: Python packaging, venvs and required package sharing

So to break up the RHCSA, I wanted to have a look into if and how you can share python programs and good practices in creating and managing python stuff.

The first thing I've learned is: It's a good idea to create a venv in the working directory for each project and have them seperate.
This allows us to use different versions of things such as Click, where we may need a certain version for once project and a different version for another.
Having a seperate venv for each of our python projects allows us to keep things clean and avoid conflicts between requirements.

On the topic of requirements, I've looked into ways that python has built-in that allow us to share the things we write and have the people we are sharing it with, have the required packages.
This is done by us activating the venv for the project we wish to share using:
```
source .venv/bin/activate
```
We can deactivate by simply using:
```
deactivate
```

Once we are sure that the venv is active we can use freeze and direct the output to a txt file:
```
pip freeze > requirements.txt
```
This will create us a list of dependencies for our venv.

A problem people have been having can be solved by using the following:
```
pip list --formate=freeze > requirements.txt
```
This does the same as above but in a different way

We can then share this requirements.txt with the person we are sharing our project with and they can install it using:
```
pip install -r requirements.txt
```

# [2025-08-15] Day 38: System Logs Monitoring (/var/log)

- Log Directory = /var/log

- boot

- chronyd = NTP

- cron

- maillog

- secure

- messages

- httpd

# [2025-08-12] Day 37: System Monitoring (top, df, dmesg, iostat, netstat)

- top
Shows a dynamic, real-time view of running processes and kernal-managed tasks

- df
Stands for "disk free", is a utility that provides information on disk space utilization

- dmesg
Stands for "diagnostic messages" which prints the message buffer of the kernel

- iostat
Stands for "input/output statistics" Used for monitoring system input/output statistic for devices and partitions

- netstat - ip & ss
Stands for "network statistics" Used to check how the machine connects to the internet, and about the connections the machine is making.

- free
Displays the total amount of free space available along with the amount of memory used, swap memory and buffers used by the kernel

- cat /proc/cpuinfo
Contains the CPU information of every core and shows the processor type. Displays Vendor_id, CPU, family, model name, microcode, cache size.

- cat /proc/meminfo
Contains data on memory usage, provides current memory details 

- htop
Used to interactively monitor system-vital resources or processes in real-time. 

- glances
Is used for system monitoring in web server mode or through the web browser. It is an alternative to top and htop.
Is run in three modes:

- Standalone Mode: Single system, results in terminal

- Client/ Server Mode: Multiple systems

- Web-server Mode: Single system but remote


# [2025-08-11] Day 36 pt2: Process Management (bg, fg, nice)

- Background = CTRL + z, jobs and bg 
- Foreground = fg
- Run process even after exit = nohup process & OR nohup process > /dev/null 2>&1 &
- Kill a process by name = pkill
- Process priority = nice (e.g. nice -n 5 process) -20 to 19, lower is more priority.
- Process monitoring = top
- List process = ps


# [2025-08-11] Day 36: Additional cronjobs (hourly, daily, weekly, monthly)

By default there are 4 different types of cronjobs:
- Hourly
- Daily
- Weekly
- Monthly

All of the above are setup in:
- /etc/cron.hourly/daily/weekly/monthly

The timing for each are set in:
- /etc/anacrontab  EXCEPT HOURLY
- /etc/cron.d/0hourly.



# [2025-08-09] Day 35: crontab, at

## crontab

Is used to schedule tasks:

crontab -e - edit the crontab
crontab -l - lists the entries 
crontab -r - remove the crontab
crond - crontab daemon/ service that manages scheduling

systemctl status crond - manage the crond service

```
command to execute
│ │ │ │ │
│ │ │ │ └───── day of the week (0 - 6) (Sunday to Saturday; 7 is also Sunday on some systems)
│ │ │ └────────── month (1 - 12)
│ │ └─────────────── day of the month (1 - 31)
│ └──────────────────── hour (0 - 23)
└───────────────────────── minute (0 - 59)
```

An example use of this would be:
```
crontab -e
schedule, echo "This is my first crontab entry" > crontab-entry
```
This allows us to edit a crontab, which at the scheduled point echos into the crontab-entry file

## at command

at command is like crontab but its for scheduling something only once 

When we run at it will put us in interactive mode like vim, we can escape by pressing CTRL + D

Usage:
```
at HH::MM PM/AM
```

atq is used to list at entries

atrm # is used to remove a listing

atd is the daemon / service 

systemctl status atd manages the atd service

Usage:
```
at TIME
echo "First at entry > at-entry"
CTRL + D
```

An example format would be:
```
at 2:45 AM 101621
```
This would schedule at Oct 16th 2021

```
at 4PM + 4 days
```
Would schedule a job at 4pm 4 days from now

```
at now +5 hours
```

```
at 8:00 AM Sun
```

# [2025-07-18] Day 34: Processes, Scheduling and Jobs

A UNIT IS ANY RESOURCE THAT SYSTEMD CAN MANAGE
EVERY ONE HAS A CONFIG FILE ENDING WITH .unit-type

SERVICES ARE CALLED DAEMONS

## systemctl or service
systemctl is a command that we can use to control system services.
It is available in version 7 and later and it replaces the SERVICE command!

A usage example of this would be:
```
systemctl start|stop|status servicename.service
systemctl enable servicename.service
systemctl restart|reload servicename.service
systemctl list-units --all
```
The output of systemctl has these columns:
```
Unit - The systemd unit name

Load - Whether the unit's config has been parsed by systemd. The config of loaded units is kept in memory.

Active

Sub

Description
```


## ps
Stands for Process Status, displays all the currently running processes in the Linux system
ps shows the processes of the current shell, it displays:

PID - Unique process ID
TTY - Terminal type the user is logged into
TIME - Amount of CPU in minutes and seconds
CMD - Name of the command

ps -e - Shows all of the running processes
ps aux - Shows all of the running processes in BSD format
ps -ef - Same as above in full format listing (MOST COMMON)
ps -u username - Shows all by username

## top
top is used to show the Linux processes and it provides a real-time view of the system
It shows the summary information of the system and list of threads or processes which are managed by the Kernel

When executed it goes into interactive mode, similar to VI, but we exit out by hitting q-

Usage: top
```
PID - Shows task's unique process ID

USER - Username of the task owner

PR - The "PR" field shows the scheduling priority of the process from the perspective of the kernel

NI - Nice Value of a task, Negative is higher priority

VIRT - Total virtual memory used by the task

RES - Memory consumed by the process in RAM

SHR - Shared memory used

S - Process state in single-letter form

%CPU - CPU Usage

%MEM - Memory Usage

TIME+ - CPU time, the same as TIME but in hundreths of a second
```

top -u USERNAME
top then c - shows commands absolute path
top then k - this kills a process by process PID
top then M and P - sorts all running processes by memory usage


## kill
Used to terminate processes manually
It sends a signal that terminates a process or group of processes

Usage: kill OPTION PID

Option: Signal name or signal number / ID
PID: Process ID

kill -l gives a list of all signal names and numbers

The most commonly used kill commands are:
```
kill PID
kill -1 - restart
kill -2 - interrupt, similar to control + c
kill -9 - force kill a process
kill -15 gracefully kill a process
```

Similar commands are:
```
killall
pkill

```

## PROCESSES AND SIGNALS

Process:
Is a running program in a computer system. app, etc

Signals:
Short message that is sent to a Process. ctrl + c is a signal
Interrupt, Control or Communicate

Process Signals are sent to running processes to control behaviour.
They enable communication.

There are 2 types of process signals:
- Standard Signals
- Real-Time Signals

Standard signal types:
- SIGINT
  vim handles interrupt by going back into command mode.

- SIGTERM
  causes vim to close gracefully, causes a save then closes the process

- SIGKILL
  cannot be ignored by processes and forces a close on anything it is called against

- SIGSTOP
  pauses a process

- SIGCONT
  unpauses a process

- SIGRTMIN
  used to check is a process is actually alive

- SIGSEGV (segmentation fault)


# [2025-07-16] Day 33: System Utility Commands (DATE, UPTIME, HOSTNAME, UNAME, WHICH, CAL, BC)

- date - Time and date


- uptime - How long the machine has been up, how many users are logged on the machine and the load average for those users


- hostname - Tells you the name of the system 


- uname - Tells you if you are on linux or not, can use uname -a


- which - Tells you where the command is located (which COMMAND)


- cal - Gives us a calander, can view months with (cal MONTH YEAR)


- bc - Binary calculator



# [2025-07-15] Day 32 pt 2: Linux Account Authentication (LDAP, IDM, WinBIND, OpenLDAP)

Types of accounts:
- Local accounts
- Domain/ Directory accounts


Differences between:

- Active Directory - Microsoft

- IDM - Identity Manager
  Is Redhat's "centralized and unified way to manage identity stores, authentication, policies, and authorization policies in a Linux-based domain."

- WinBIND - Used in Linux to communicate with Windows (Samba)

- OpenLDAP (open source)

- IBM Directory Server

- JumpCloud

- LDAP - Lightweight Directory Access Protocol




# [2025-07-15] Day 32: Special Permissions (setuid, setgid and sticky bit)

Permissions on files and directories are referred to as bits:
- r w x   r w x    r w x
  user    groups   others 

There are 3 additional permissions in Linux:

- setuid: bit tells linux to run a program with effective user id of the owner instead of the executor
  An example of this is passwd, which runs as root as it has to update /etc/shadow

- setgid: bit tells linux to run a program with the effective group id of the owner instead
  An example of this is the locate or wall command, this is only present for files with executable perms

- sticky bit: is a bit set on files/ directories that allows only the owner or root to delete them

To assign perms at the user level we use chmod u+s filename to add

To assign perms at the group level we use chmod g+s filename to add 

To find all exectuables with setuid and setgid we can run:
```
find / -perm /6000 -type f
```

THESE BITS ONLY WORK ON C AND NOT BASH

Sticky bit:
It is assigned to the last bit of permissions: rwx rwx rw(t)
This is done with chmod:
```
chmod +t filename
```


# [2025-07-13] Day 31: Talking to Users (user, wall, write)

- users is used to see who is logged into the machine


- wall is used to broadcast a message to every user on a system
```
wall

MESSAGE HERE
```


- write is used to message individual users:
```
write USERNAME

MESSAGE
ctrl + D for EOF
```

# [2025-07-12] Day 30: User Monitoring (who, last, w, id)

- who tells you who is logged in, their user id. Tells you who the terminal is owned by and the time it was opened
  

- last shows a list of the last logged-in users, using the system log file (/var/log/wtmp).
This is done in chronological order.

We can use last -n to see a number of the most recent login entries.

You can display this while hiding the hostname by using last -R USERNAME.

You can use last -F to display login and logout times with corresponding dates.

To display the last column of the output we can use the -a option.

You can also filter the entries to a specific period using last -s FROM -t TO
We can use date and time formats for this:
```
YYYYMMDDhhmmss
YYYY-MM-DD hh:mm:ss
YYYY-MM-DD hh:mm      (seconds will be set to 00)
YYYY-MM-DD            (time will be set to 00:00:00)
hh:mm:ss              (date will be set to today)
hh:mm                 (date will be set to today, seconds to 00)
now
yesterday             (time is set to 00:00:00)
today                 (time is set to 00:00:00)
tomorrow              (time is set to 00:00:00)
+5min
-5days
```

You can use last -d to translate ip adresses into hostnames.
  
- w works similar to who, gives login time, idle time and what process are being run by the user

- finger / pinky on centOS 8 and above. Does not come default (# yum install finger -y)

- id , without giving a username it gives you information about your account, it tells you the groups that a user is a part of

# [2025-07-10] Day 29 pt2: Switch users and sudo Access:

To change user: su - username

sudo is how we run a command if we are not root

visudo edits the /etc/sudoers file that tells who is allowed to use the sudo command or not

The most efficent way to give a certain user sudo access by adding them to the group wheel:
```
usermod -aG wheel USER
```

# [2025-07-10] Day 29: Password Aging (chage):

The command used to change password aging is:
```
chage [-d LASTday] [-m MINdays] [-M MAXdays] [-d LASTday] [-I inactive] [-E EXPIREdate] [-W WARNdays] user
```

This is all stored in /etc/login.defs:
```
more /etc/login/login.defs
```

-d days since last changed

-m minimum number of days before the the user is allowed to change his password

-M maximum number of days that the password us valid before a forced change

-W number of days before the password is to expire, that the user is warned to change

-I number of days after the account expires that the account is disabled

-E number of days since Jan 1, 1970 that the account expires


# [2025-07-06] Day 28: User Account Management (useradd, groupadd, usermod, userdel, groupdel)

An example:
```
# sets the primary group
useradd -g GROUP

# creates the user's home directory
useradd -m -d /home/USERNAME

# sets the default shell for that user
useradd -s /bin/bash

# sets an expiration date for the user
useradd -e YYYY-MM-DD

# sets a comment on the user
useradd -c

# Do not create a home directory (useful for service or system accounts)
useradd -M
```

Multiple things can be done in one command:
```
useradd -g GROUP -s /bin/bash -c "comment" -m -d /home/USERNAME USERNAME
```

To remove a user and their home directory you would run:
```
userdel -r USER
```

To remove a group we do:
```
groupdel GROUPNAME
```

We can create a group with usermod -G group GROUPNAME
We can then confirm if it has been created with:
```
grep spiderman /etc/group
```

To change a user's group on ls -ltr you have to use:
```
chgrp -R GROUP USER
```

In the passwd the syntax of the lines is:
```
name:passwordencrypted:groupnum:usernum:description:homedir:shell
```

etc/shadow is for passwords of users we create and their expiration

You can change a password with:
```
passwd USER
```

# [2025-07-05] Day 27: "sed" command

"sed" replaces a string in a file with a newstring
It can also find and delete a line
It can remove empty lines
Remove the first line or n lines in a file
Replace tabs with spaces

```
sed 's/WORD/CHANGETO/g filename
```
g is globally, it replaces all instances

sed changes in the cli but not in the file
To make a change to the existing file you have to use

```
sed -i
```
To insert the changes to the given file

We can use sed to remove a given string by replacing CHANGETO to nothing

To find a delete we would use:
```
sed '/FIND/d' FILENAME
```
d is used to delete all of the found lines

To delete empty lines using sed we would use:
```
sed '/^$/d' FILENAME
```
^ anything that starts with nothing $ anything that ends with nothing

To remove the first line you would do:
```
sed '1d' FILENAME
```

We can use it to replace indents with spaces:
```
sed 's/\t/ /g' FILENAME
```

Show specific line numbers 
```
sed -n START,FINISHp FILENAME
```

To exclude the lines:
```
sed START, ENDd FILENAME
```

To add empty lines between lines:
```
sed G FILENAME
```

Replace with line exceptions:
```
sed '8!s/REPLACE/CHANGETO/g' FILENAME
```

To replace in vi:
```
:%s/STRING/CHANGETO/
```



# [2025-07-03] Day 26: Vi Editor

vi has commands for inserting and deleting text, replacing text, finding and substituting strings, cutting and pasting text.
Luckily it is not my first time using Vi so this is not completely new to me.

Some common inputs for vi are:

- i this puts you into insert mode and allows you to type normally

- Esc key lets you exit out of any mode, most commonly to exit insert into command mode

- r is for replace

- u is for undo

- x to remove a single character

- a will advance to the next word

- d is for delete, using this at the start of a line removes the whole line

- :q! is to quit without saying

- :wq is to write and quit

- /desiredword this will find and highlight your desired word

Resources to try out:

vimgenius
vim adventures

# [2025-07-02] Day 25 pt2: Windows vs Linux Commands

| Command Description                         | Windows     | Linux           |
|--------------------------------------------|-------------|------------------|
| Listing of a directory                     | `dir`       | `ls -l`          |
| Rename a file                              | `ren`       | `mv`             |
| Copy a file                                | `copy`      | `cp`             |
| Move file                                  | `move`      | `mv`             |
| Clear screen                               | `cls`       | `clear`          |
| Delete file                                | `del`       | `rm`             |
| Compare contents of files                  | `fc`        | `diff`           |
| Search for a word/string in a file         | `find`      | `grep`           |
| Display command help                       | `command /?`| `man command`    |
| Displays your location in the file system  | `chdir`     | `pwd`            |
| Displays the time                          | `time`      | `date`           |

# [2025-07-02] Day 25: Combining and Splitting Files.

Multiple files can be combined into one and one file can be split into multiple.
Split, if no input_file is given, reads from stdin

```
cat file1 file2 file3 > file4
```
This opens and outputs these files into file4

```
split -l 300 file.txt childfile
```
This would split file.txt into childfiles at 300 lines each

-b is also an option with --bytes=NUM to seperate files by byte size instead.


# [2025-07-01] Day 24: Truncate File Sizes

truncate is foten used to shrink or extend a file to the specified size, it doesn't compress it CUTS/CHOPS, it is destructive.

-s dictates that you want to shrink a file 

```
truncate -s size_in_bytes file_name
```

truncate can be used to extend or shrink:
```
truncate -s 10M

truncate -s +5K

truncate -s -2K
```
This would set the file to 10MB, add 5KB to the file and subtract 2KB from the file specified.

# [2025-06-29] Day 23: Compress and uncompress files (tar, gzip, gunzip)

## tar 
tar doesn't compress and reduce size it just groups them together.

```
tar cvf file_name.tar dir_to_tar
```

-c create a tar archive with no compression

-t lists and shows contents of the archive 

-z filters the archive through gzip

-f use the next argument as the filename

we replace the t with x if we want to extract:
```
tar -xzf mybackup.tar.gz
```

## gzip

gzip compresses a tar making the file size smaller

| Option    | Description                                                    |
|-----------|----------------------------------------------------------------|
| -d        | Decompress the compressed file (same as `--decompress`)        |
| -c        | Write output to standard output (stdout), keep original files  |
| -k        | Keep original files (do not delete after compression)          |
| -f        | Force compression or decompression even if errors occur        |
| -h        | Display help message and exit                                  |
| -l        | List information about compressed files                        |
| -n        | Do not save original file name and timestamp                   |
| -N        | Save original file name and timestamp (default)                |
| -q        | Quiet mode (suppress warnings)                                 |
| -r        | Recursively compress files in directories                      |
| -S suffix | Use specified suffix instead of `.gz` for compressed files     |
| -# (1-9)  | Compression level: 1 = fastest, 9 = best compression           |
| --fast    | Equivalent to `-1`, fastest compression                        |
| --best    | Equivalent to `-9`, best compression                           |
| --list    | Same as `-l`, list compressed file info                        |
| --no-name | Same as `-n`, do not save file name and timestamp              |
| --name    | Same as `-N`, save file name and timestamp                     |
| --version | Show version information                                       |

## gzip -d or gunzip

gzip -d or gunzip is used to uncompress a tar

# [2025-06-26] Day 20/21/22: Filters / Text Processing Commands (cut awk grep sort)

In efforts to centralise the working enviroment and minimise the effort required to begin learning, I decided to setup the CentOS Stream 9 machine on the main PC.

- Download oracleboxVM
- Grab a CentOS Stream 9 iso
- Remember that I have VT-x off in my bios and give up and go back to the laptop 

## CUT
- cut allows you to extract a desired field from a file or input and it writes the ouput to stdout.
```
cut -c1 file_name 
```
This would give us a list of all the First characters of each line.

cut also has:

-b select the bytes (-b 1-5) will get bytes 1 to 5

-c select characters (-c 3-7)

-f select fields (used with -d)

-d set the delimiter for fields (default is tab)

--complement inverts the selects (show everything expect the specified bytes, characters or fields)

-f and -d is very useful in combination, an example document could be:

```
egg,chicken,milk,toast
who,what,where,why
loose,goose,obtuse,truce
```

Using (cut -d ',' or cut -d,) this splits the lines into columns and if you add -f you can select which columns you want:
```
cut -d, -f 2 file.txt
```
This would give us the 2nd index of each line so it would return us:
```
chicken
what
goose
```

## AWK
- awk allows you to search through a text file by columns. It is somewhat similar to cut, but it is an implementation of a domain specific language designed for text processing and data extraction.

A very basic structure for using awk is:
```
awk 'pattern { action }' filename.txt
```

A use-case for this would be from piping in ls -l:
```
ls -l | awk '{print $1, $3}'
```

To get the last entry of a list, instead of $-1 we have to use $NF which stands for number of fields:
```
awk {print $NF} filename.txt
```

We can also search for a specific word using awk like so:
```
awk '/word/ {print}' filename.txt
```

Another use of awk, similar to cut:
```
awk -F '{print $1} /etc/passwd'
```
The $1 is the column that we cant from the list, this would display each of the first entries.

We can also use awk to replace words in fields:
```
echo "Hellowl World" | awk '$2="Sauce" ; print $0'
```

We can use awk to find entries logner than a given length:
```
awk 'length($0) > 15' filename.txt
```

Matching fields:
```
awk '{if($NF == "desired_name") print $0;}'
```

Number of fields:
```
awk '{print NF}'
```

## GREP EGREP
- grep and egrep (global regular expression print) allows you to search for specific text within files. Can find words, phrases, patterns in one or multiple files.
```
grep -- version
grep --help
```

Grep has a number of useful options:

-i Ignores case

-v Invert match

-r Recursive search

-n Show line numbers

-c Count matches

-l List filenames with matches

-e Use multiple patterns

A simple use of grep is:
```
grep desired_string file_name
```

An example of grep -c would be:
```
grep_test.txt contains:
a,b,c,d
a,b,c,d
a,b,c,d

grep -c b grep_test.txt
```
This would print 3

An example of grep -i would be:
```
grep_test.txt contains:
a,B,c,d
a,B,c,d
a,b,c,d

grep -i b grep_test.txt
```
This would print:
```
a,B,c,d
a,B,c,d
```

An example of grep -n would be:
```
grep_test.txt contains:
a,B,c,d
a,B,c,d
a,b,c,d

grep -n b grep_test.txt
```
This would print:
```
3:a,b,c,d
```

An example of grep -n would be:
```
grep_test.txt contains:
a,B,c,d
a,B,c,d
a,b,c,d

grep -v b grep_test.txt
```
This would print:
```
a,B,c,d
a,B,c,d
```

We can also combine grep with awk:
```
grep_test.txt contains:
a,B,c,d
a,B,c,d
a,b,c,d

grep b grep_test.txt | awk '{print $3}'
```
This would give us:
```
c
```

egrep is used when you want to find multiple keywords. The syntax for this is:
```
egrep keyword1|keyword2 file_name.txt
```

## SORT
- sort reorders a file numerically or alpabetically and prints the result to stdout.

```
sort --version
sort --help 
```

sort and it's options are:

-r reverse order

-n numeric sort

-u unique lines

-k sort by key (column)

-t set field delimiter

-h Readable numeric sort(1K 2M)

-o Write result to file (sort file.txt -o sorted_file.txt)

-C NUM shows context in the way of number of lines before and after

sort's primary use is:
```
example_text.txt:
b,c,d,j
z,c,d,e
e,f,y,t
a,b,c,d

sort example_text.txt
```
This would output:
```
a,b,c,d
b,c,d,j
e,f,y,t
z,c,d,e
```

## UNIQ
- uniq will not show duplicates, will print any single instances.

-c Prefix lines with the number of occurrences

-d Only print duplicate lines

-u Only print unique lines

-f N Ignore first N fields

-s N Ignore first N characters

-i Case-insensitive comparison


```
uniq_test.txt contains:
a,B,c,d
a,B,c,d
a,b,c,d

uniq uniq_test.txt
```
This would print:
```
a,b,c,d
```

## Comparing file (diff and cmp)

- diff is line by line
Diff compares line by line and hows how they differ, it shows the changes need to make the files match.
Has the options:

-u unified format
-y side by side comparision

- cmp is byte by byte
If the files are the same then there is no output other than a 0 exit code.
If they differ then it prints the first difference it finds.

## WC
- wc tells you how many words, letters or lines you have.

It has the options:

-l Count lines

-w Count Words

-c Count Bytes

-m Count Characters

-L Show longest line length


# [2025-06-25] Day 19 pt4: File Display Commands !!!SUPER USEFUL!!!

Here's a list of file display commands:

- cat concatenate and display the contents of a file

- more views the content of a file one page at a time

- less allows you to view text files one page at a time

- head allows you to see a few times at the front or top of a file

- tail allows you to see lines at the end or the bottom of a file

# [2025-06-25] Day 19 pt3: File Maintenance Commands !!!SUPER USEFUL!!!

Here's a list of file maintenence commands:

- cp copy

- rm remove
  
- mv move
  
- mkdir create directory
 
- rmdir or rm -r remove directory
  
- chgrp change group 
  
- chown change ownership



# [2025-06-25] Day 19 pt2: Executing Multiple Commands with ;

An example of using multiple commands with ; is:
```
ls ; ipconfig ; pwd
```
This would execute ls then ipconfig then pwd.
A very useful example of this would be done before executing any commands that need to be in a specific spot:
```
whoami ; pwd ; hostname ; ip a
```

The series of commands executes one by one so even if one fails the next will be executed.

A series of commands that I could have used in autofile if I decided to keep the code in bash instead of python would have been:
```
mkdir nameinlist ; cd nameinlist ; touch nameinlist 
```

# [2025-06-25] Day 19: Pipes (|)

A pipe is used by the shell to connect the input of another command
The symbol of pipe is | and the command syntax is:
```
command1 [arguments] | comannd2 [arguments]
```

```
ls -ltr | more
```
This will give us the output of ls-ltr one page at a time, spacebar lets us move down a page and q allows us to quit. ll can also be used as an alternative to ls-l

``
ls -l | tail -1
``
This would give us the last line from the output of  ls -l

# [2025-06-21] Day 18: Standard Output to a file (tee)

tee is used to store and view (simultaneously) the output of a command, it is named after the T-splitter in plumbing.
It breaks the output of a command so it can be displayed and saved, the result is copied in to specified files or varirables AND displayed.

```
command --> tee --> stdout
             |
            file
```

Before starting, some basic checks:--
```
whoami
pwd
hostname
```
To check we are who and where we want to be.

We can use > to output a command to a file such as:
```
echo "world, hello" > hereiam
cat hereiam # this would output "world,hello"
```
This would write the output of the command to hereiam but it wouldn't display anything in the cli.

If we instead did:
```
echo "world,hello" | tee hereiam
```
This would write to hereiam and would display the contents in the cli.

To use tee without overwriting the contents of the destination file we would have to use tee with -a:
```
echo "world,hello" | tee -a hereiam
```
This would append the output onto the end of the given file.

Another command we can use with these files is wc, wc stands for word count and can be used with a few flags:
```
wc -l # lines
wc -w # words
wc -m characters
wc -c bytes
```

tee can also be used with commands like ls -l:
```
ls -l | -a listdir
```
This will output in the cli but also create a file and append to it.

You can also tee to multiple files if the names are added with a space.




# [2025-06-18] Day 17: Input and Output Redirects

There are three (3) redirects in Linux:

- Standard input (stdin), it has the file descriptor number as 0

- Standard output (stdout), it has the file descriptor number as 1

- Standard error (stderr), it has the file descriptor number as 2

The output of a command can be routed to a file using > (stdout -1)
```
ls -l > listings
pwd > findpath
```
- A single > Overwrites, replaces and if it does not exist it will create the file.

- A double >> Appends to the end of the file, if it does not exist it will be created.

The input of a command can be routed by using "< (stdin -0)"
```
cat < file.txt
sort < file.txt
```

Double "<<" also exists and is used to feed multiple lines into a command such as sort, this is known as a heredoc:
```"
cat << EOF
this is line one
this is line two
EOF
```
This would print the two lines when used with cat, it can also be used with other commands.

You can also use variables inside the heredoc such as:
```
name = "OWL"

cat << END
Hello, $name!
END
```

This can be disabled by quoting the end marker:
```
cat << "END" 
```

If the command that is typed in produces and error it is then considered (stderr -2)

You can use these errors and redirect them to files by using >
```
ls -l /root 2> errorlog
telnet localhost 2> errorlog
```
This will redirect the errors into the determined file.

# [2025-06-11] Day 16: Basic Shell Scripting intro

## WHAT is shell scripting

Shell scripting is a list of commands that the systems runs one after the other.

The shell interprets commands and by chaining commands in a script we can do many useful things such as:

- Move multiple files from one dir to another.

- Find patterns in files.

- Monitor system resources such as CPU and Memory.

Scripting is useful for a few reasons:

- Automation: Backups, Software updates, Health checks at set intervals, Clean temporary files.

- Customization: Can be adjusted and tailored for specific sets of files, specific system setups. For example: A custom script that checks CPU load on a server and alerts you if it is exceeded.

- Efficiency: Allows you to integrate with powerful and common system tools such as: grep, awk, sed, find. For example: A script that pulls specific logs and generates a report with only a few lines of code.

- Portability: Highly transferable to other Unix and Unix-like systems if written in a common shell like Bash. Very useful to standardize operations across different environments.

- Powerful and Flexible: Allows the use of things such as grep to find and process. For example: program that extracts log entries from multiple files (servers, users) and generates a report.

## Types of shells:

- Bash (Bourne Again Shell), most popular, successor to the original Bourne Shell (sh).

- Zsh (Z Shell), has additional features like better tab completion, syntax highlighting.

- Fish (Friendly Interactive Shell) Modern, user friendly, auto suggestions, syntax highlighting.


## WRITING A SHELL SCRIPT:

First we have to create a file, we can do this in two ways:
```
touch script.sh
vim script.sh
```
We then have to tell the operating system what Shell we want to interperate using a shebang:
```
#!/bin/bash
```

#! tells the system should run using the interpreter stated after.

/bin/bash is the path to Bourne again.

Doing this allows us to state what shell we want to use even though our default might be different.

An example of a very basic script could be:
```
#!/bin/bash
echo "Helowl, World!"
```
:wq in VIM to write and quit

Execute permissions may need to be set using chmod [Covered here in day 12](https://lmno.lol/basowl/day-12-file-permissions)

```
chmod -x script.sh
```
OR
```
chmod 111 script.sh
```

# [2025-05-23] Day 15: !!! Help Commands & Tab completion !!!

## Help

There are 3 types of help commands:

- whatis
  This can only work if mandb has been run by root atleast once before.

- --help


- man


## Tab completetion

Hitting tab in the CLI auto complete things such as filenames:
```
chm # if you hit tab it will autofill to chmod

cd Des # if you hit tab it will autofill to cd Desktop
```
If you hit tab and there are multiple things that it could fill as, it will display all the options that you could have.

## Up Arrow

Hitting the UP arrow in the CLI will allow us to see the previous command(s) and if desired, repeated.

# [2025-05-22] Day 14: Access Control List

The ACCESS CONTROL LIST is an additional and more flexible permission system.<br>
It allows you to give permissions for any user or group to any disc resource.
Basically, an ACL is a flexible permission mechanism in Linux.

## The primary purpose of ACL is to give individual users permissions without giving ALL users access

- --------(---) setting the last group to have either read or right access would give it to ALL users, this is where ACL comes in.

The commands used to interact with ACL permissions are:
```
setfacl

getfacl
```

## getfacl
```
# file: name
# owner: root
# group: root
user::---
group::---
other::-----
```

## ADD

To add permissions for a user:
```
setfacl -m u:user:rwx /path/to/file
```

To add permissions for a group:
```
setfacl -m g:group:rw /path/to/file
```

To allow all files and directories to inherit ACL entries from the parent directory you use:
```
setfacl -Rm "entry" /path/to/dir
```

## REMOVE

To remove a specific entry for a specific user:
```
setfacl -x u:user /path/to/file
```

To remove all entries for all users:
```
setfacl -b path/to/file
```

If you assign ACL permissions to a file or directory it will affix a "+" to the permissions.
Setting write permissions with ACL does not allow file removal.




# [2025-05-20] Day 13: File Ownership

Files and directories have two owners. User and Group.

In order to change the ownership of a file we can use either:
```
chown

chgrp
```
- chown changes the ownership of a file 
- chgrp changes the group ownership.<br>
"-R" is to tell the changes to be recursive and change ownership of everything in a directory.



# [2025-05-17] Day 12: File Permissions (chmod)

Every file and directory in an account can be protected from or made accessible to other users by changing its permissions. 

Permissions for a file or directory may be restricted to one of theese three:

- r - read 

- w - write

- x - execute or run

Each permission (rwx) can be controlled by groups:

- u - user (yourself)

- g - group (a selection of people)

- o - other (everyone on the system)

- a all

This can be seen when using 
```
ls -l
```

And you end up with:
```
-rwxrwxrwx
```

This tells you the permissions for user group other in that order.

The command to change permissions is:
```
chmod

chmod g-w "name" # this would remove write permissions for the group

chmod a-x "name" # this would stop everyone accessing the directory
```

Permissions can also be changed using Numeric Mode:
```
chmod ugo+r "name" # can also be done as:

chmod 444 "name"
```

The order of permissions determined this way are: USER, GROUP, OTHER

## Numeric Mode Table:

| Number | Permission Type           | Symbol |
|--------|---------------------------|--------|
| 0      | No Permission             | ---    |
| 1      | Execute                   | --x    |
| 2      | Write                     | -w-    |
| 3      | Execute + Write           | -wx    |
| 4      | Read                      | r--    |
| 5      | Read + Execute            | r-x    |
| 6      | Read + Write              | rw-    |
| 7      | Read + Write + Execute    | rwx    |


# [2025-05-10] Day 11: Command syntax

Commands typically have the syntax: command option(s) argument(s).

These can be found through the CLI by typing:

```
man <command>
```

## Options

Options usually modify the way a command works. It usually consists of a hyphen followed by a single letter. The most common example being:

```
ls -l
```

Some commands accept multiple options which can be usually be grouped to gether using a single hyphen.

## Arguments

Most commands are used together with one or more arguments. However, some commands assume a default command if one isn't provided. Optional for some, required by others.




# [2025-05-07] Day 10: Soft and Hard links

Before going into links we need to know about Inode which is a pointer or number of a file on the disk. This is how the computer identifies a file.
```
my-hard-link   myfile.txt <----  my-soft-link
      \             |          
       \            v          
        \----->  inode 
```

## Soft Link

A soft link is a link that will be removed if the source file is removed or renamed.

The command to create a soft link is:
```
ln -s
```

To test this out practically I go to my linux installation and create a file using "touch linktest"
I then used "cd /tmp" and "ln -s /home/a/linktest" and "ls -ltr" to check if it is successful.
We get a link and it beings with l to confirm it is a link.

We can use "ls -ltri" to see the inodes of files and we can do this to see that linktest and the link TO linktest have different inodes but are able to access the same file. It is similar to a shortcut in windows.

## Hard Link

A hard link is a link that will not be removed if the source file is deleted or renamed.
It is essentially a copy of the original file.
Preferably not to be used as it can lead to various issues that don't exist with softs.

The command used to create a hard link is:
```
ln
```

# [2025-05-05] Day 9: Wildcards. !!!SUPER USEFUL!!!

## Wildcards

Wildcards are "characters that can be used as a substitute for any class of characters in a search" they are:

- "*" represents zero or more characters

- "?" represents a single character

- "[]" represents the characters inside the brackets, can also be a range such as 1-5

- "{}" represents a determined range

- "\" is an escape character

- "^" indicates the beginning of the line

- "$" indicates the end of the line

These can be used in various ways and one example is with remove or rm:

```
rm abc*
```
This will remove anything that starts with abc and anything after it.

Another example of the use of wildcards is creating multiple numbered files:

```
touch abc{1..9}
```

This will create 9 files with the name abc1 to abc9.

The most common use of wildcards are when using find or locate in order to find multiple of a type of file. Such as .txt or for a certain user / block.

Say we have created 5 files with touch abc{1..5} and another 5 with xyz{1..5}. This gives us a folder of 10 files. If we wish to see the details of a certain side, such as xyz:
```
ls -l xyz*
```

This will display all the files that start with xyz.

# [2025-05-04] Day 8: File location and finding

There are two useful ways to find files in linux:

## Find

```
find <location> -name "fileName" 
```

"." can be used to determine that we want the search to start in the current working directory. This only works for files and it will output where the file is if found: ./testFolder/test

Find iterates over the filesystem to find what we are searching for, this means it is more accurate but much slower than locate.

## Locate

```
locate <fileName>
```

This outputs the location of the file: /home/a/testFolder/test

Locate uses a prebuilt database, which should be updated regularily. Locate is faster due to this but unless it is updated it can be incorrect. To update locate we use:
```
updatedb
```
This should be done as root to make sure the correct database is being updated and not the temporary one.

# [2025-05-02] Day 7: File creation / File copying

## File creation

- touch. This creates an empty file with nothing in it.

- cp. copies a source file to a destination. 
```
cp sourceFile destination
```
```
cp -R <sourceDir> <destinationDir>
```
We use -R to tell copy that we are copying a directory and not a file.

- vi or vim. 
```
vi fileName
```

To escape vim we have to make sure that we are not in insert mode, if we are we can get out of it by pressing ESC, we can then close the file by typing :WQ this stands for write and quit.


## Directory creation

- mkdir. To make a directory
 
# [2025-05-01] Day 6: Root, passwords, absolute / relative paths.

Day 6 and temperatures outside are getting to the mid 30s, this makes it much harder to stay at the computer, but learning must be done.

## What is root?

- Root account is an account or username that has access and permissions for all commands and files.

- Root directory is /: which is the first directory in linux. cd /.

- Root home directory which is the highest folder for a user. Located in /root

## Changing Passwords

```
passwd userid
```
This asks us the old password and then asks us for a new password, we then have to retype the new password to confirm the change.
Only root can define a user to change.

## File paths

There are two types of paths to files.

- Absolute. An absolute path always begins with "/". An example being:
```
cd /var/log/samba
```

- Relative. This does not begin with a "/" and it identifies a location based on your current position. An example being:
```
cd /var
cd log
cd samba
```



# [2025-04-30] Day 5: File Navigation and File / Directory properties.

Day 5 is about interacting and moving around using the CLI.

Starting off with probably the most used and most important command:

- cd. It stands for change directory, this allows us to move around our folders and get to where and what we need. 

- cd .. is used to go up a level .. goes to the parent of the directory we are currently in.

The next command is:

- pwd. This stands for print working directory. This tells us where we currently are.

A very useful command is:

- cat. This stands for concatenate and it displays a file and it's contents.

The last and my personal favourite is:

- ls. This stands for list and it displays all files and folders in the current directory, excluding hidden files. ls -l is used for better formatting.

ls -l gives us something in the same format as the following:
```
| Type         | # of Links | Owner | Group  | Size | Month  | Day | Time   | Name     |
|--------------|------------|-------|--------|------|--------|-----|--------|----------|
| drwxr-xr-x.  | 21         | root  | root   | 4096 | Feb    | 27  | 13:33  | var      |
| lrwxrwxrwx.  | 1          | root  | root   | 7    | Feb    | 27  | 13:15  | bin      | 
| -rw-r--r--   | 1          | root  | root   | 0    | Mar    | 2   | 11:15  | testfile | 
```
d at the start of the type tells up that this is a directory.
l tells us that this is a link to another file.
- at the start tells us that this is just a regular file.

The number of links that something has. For a directory it is the number of subdirectories it has including it's parent and itself.

Additional symbols and their meanings are below:
```
| File Symbol | Meaning                     | Examples                     
|-------------|-----------------------------|---------------------------------------------------------------------|
| -           | Regular file                | Text files, exes, images, videos                                    |
| d           | Directory                   | Folders                                                             |
| l           | Link                        | Points to other files, soft links, hard links.                      |
| c           | Special file or device file | Hardware devices such as cpu, mouse, keyboard.                      |
| s           | Socket                      | Network communication, allows data exchange.                        |
| p           | Named pipe                  | FIFO first in first out, allows data communication.                 |
| b           | Block device                | Hard drives and USBs, storage devices.                              |
```

Knowlege check:

- The File that starts with c?

- What is the purpose of that file?

- Identification letter of device file?


# [2025-04-28] Day 4: Remote access and File systems.

Day 4 begins with a brief recap of a some important things to remember about linux.

- ROOT is a super-user that has unrestricted access and ability to manipulate files.

- Linux is case sensitive, a1 and A1 are not the same.

- Linux is mostly used through the CLI

## DAY 4 proper:

There are TWO types of access to a linux machine:

- Local: Console. In person, hands on keyboard.
- Non-local: Remote. Can be done without being in person, over the network, usually over SSH. 

Remote connection is done with SSH ipaddress in a CLI as follows:
```
ssh -l USER xxx.xxx.x.x
```

To get the ip address of the machine we want to connect to, we can use this command locally:
```
ip addr
```

Once we are successfully connected we can tell if we are logged in a user or root depending on the username and prompt symbol after the hostname. The default command symbol for root is: # and for users it is: $.

- whoami can also be used to display who you are logged in as.

- rootname can be used to display the name of the host too.

- CTRL + C is used to escape out of any commands we might be "trapped" in such as cat.

Next is filesystems. Filesystems are systems used by operating systems to determine how files are stored.

The primary files systems used by linux are:

- [ext3](https://en.wikipedia.org/wiki/Ext3)

- [ext4](https://en.wikipedia.org/wiki/Ext4)

- [xfs](https://en.wikipedia.org/wiki/XFS)

The file system structure used is:

- /boot which contains the config for the bootloader, the commonly used one is [GRUB.](https://en.wikipedia.org/wiki/GNU_GRUB)

- /root this is the root user home directory.

- /dev which is the folder for system devices (disk, keyboard, mouse, etc).

- /etc is configuration files for applications.

- /usr/bin is everyday user commands.

- /usr/sbin is system and filesystem specific commands.

- /opt is optional applications that are not part of the OS.

- /proc is running processes but only exists in memory.

- /usr/lib is C library files.

- /tmp is for temporary files.

- /home is the user directory, all non-root is in here.

- /var is the system logs, useful for debugging.

- /mnt is to mount external file systems such as NFS.

- /media is for cdrom mounting.


# [2025-04-27] Day 3: Snapshots, resource allocation, keys.

Day 3 begins with creating a snapshot of the CentOS installation on the OracleVB machine. This was done incase I make a mistake down the line, as it allows me to return a clean working point without having to reinstall the entire OS.

Resource allocation on OracleVB is done by clicking the VM instance of choice, clicking settings and then changing the desired resource. The most common examples of this would be Memory (RAM) or CPUs. Other things can be changed here such as network devices, encryption and USB device functionality.

The next thing I went over was common keyboard keys actually used in Linux:

## KEYS:

- ESC being the inital and most self-explanatory, is used to get out of states. A prime example being when you are in "INSERT" mode in VIM and return or ESCAPE back into command mode.

- Tilde ~ is another massively useful key as it allows us to save time and effort while navigating to home:
```
cd ~
```

- Hash # is immensely useful as it allows us to add comments to our code in linux which allows us to keep notes and ideas and more importantly, allows us to let other people who may have to view our code to have a better understanding of what it does/ intends to do. This is especially useful in a team setting.

- Tab is used to indent lines of code and it is also used to autocomplete when using the CLI, this can be used for autofilling commands, filenames, etc.


- { open and } closed curly braces.

- [ open and ] closed square brackets.

- : colon. Used primarily in VIM to preceed a command such as:
```
:wq # wq is used to save(write) and close(quit) the open file.
```

- | is the pipe key, ctrl + alt + shift \ on this current keyboard. This is used to combine commands together or PIPE the output from one command to the next.

- < input > output. < is the output from. >> is appending, > is overwriting.

- Right CTRL. This is used to break out mouse and or keyboard control out of OracleVB and the running VM.

Day 3 ends with the creation of a new VM in OracleVB and a fresh installation of CentOS to keep the process fresh in my mind through repetition.

# [2025-04-26] Day 2: Intro to CentOS, RHEL, OracleVB, and AWS.

Day 1 was spent learning the history and the reasons behind Linux. Linux is open-source, free to use, highly customizable, and adaptable to specific purposes. I felt this didn't need its own entry but included it here as a reminder.

Day 2 was spent installing CentOS on a machine using Oracle VirtualBox Manager. I set the processing power to 2048MB and the storage size to 20GB. CentOS was installed and runs successfully:

[Imgur Link Here (Image embedding isn't available at the current time)](https://imgur.com/a/day-2-centos-running-7RwAmoZ)

The next step after the successful hardware installation was using Amazon Web Services (AWS) to install a Linux VM and connect.

This took a couple of attempts before succeeding. I signed up to [WhizLabs](https://www.whizlabs.com/) to take advantage of their AWS sandbox, reducing concerns about potential costly mistakes.

With the EC2 instance live and the Key Pair set, I used WSL and Ubuntu on my machine to SSH into the AWS machine. This required editing my `.ssh` config on Ubuntu and adding the new cloud machine as a host:

```
Host aws
  HostName xxx.xx.xx.xxx
  IdentityFile ~/.ssh/xxxx.pem
  User ec2-user
```

The `.pem` file's permissions needed to be changed to be used with the command: `ssh-keygen -lf xxxx.pem`.

I used `chmod 0600 xxxx.pem` to change the permissions, making the key file only viewable by root.

This allowed me to use `ssh-keygen -lf xxxx.pem` to verify the file's validity.

After completing these steps, I connected using `ssh aws`, which was successful:

[Imgur Link Here](https://imgur.com/a/TqAJVu6)

---

# [2025-04-25] Goals, Intentions, and Motivations.

**Goals:**

Update this blog to track my learning progress.

Learn about modern computing and how operating systems, installed both on hardware and in cloud environments, are used in business settings.

Manually install and run VMs on both platforms and automate the installation and setup.

Gain an understanding of Linux and its roles and applications in modern business.

**Intentions / Motivations:**

I have always had an interest in the technology used in modern business, especially how computing operates on a larger scale in the real world.

I've discovered resources that make the learning experience more streamlined and engaging.

The idea of working in a field that is entirely remote, as is often the case in cloud computing, system administration, and DevOps, aligns with my career aspirations.

A certain duck who brings immense light into my days.

As thanks to **R** for an unlimited amount of patience.

# secret


<img alt="Avatar" src="https://github.com/BasOwl/blog/blob/main/oh%20hi.jpg?raw=true">

# python - autofile

The goal is to create a program that:

- Takes a given list of names.
- Creates either Directories or Files with each of the listed names.
- Has good readability and can be understood by a 3rd party in order to increase my ability to work with a team.
- Has good documentation for the same reason as above.

This is to speed up the process of file and directory creation and to replace the need to type out each name in a touch or mkdir command.

The reason(s) I chose to use python are:

- It is the language I am most familiar with.
- To increase my familiarity and renew my knowlege of python and file handling.
- To attempt to find a way to distribute Python projects in an easier and cleaner way.

I am intending to use vim to further familisarise myself with coding on a CLI which is in line with the rest of the blog.

## auto_file.py

[Link to the github is here. <--](https://github.com/BasOwl/auto_file)

```
# The goal of this program is to:
# - Open a file.
# - Read and import the file, line by line.
# - Create a file per name or a directory per name.

import os

def import_file() -> list[str]:
    """Open a given file and return a list of the contents with blank lines removed"""
    imported_list = []

    with open("names.txt", "r") as file: # can change this to be a variable given to the function to define other files.
        for line in file:
            if line.strip() != "": # checks for blank entries and excludes them.
                imported_list.append(line.strip())
    
    return imported_list


def create_parent_dir() -> None:
    """Creates a parent directory, can also be used to create multiple"""
    PATH = "names" # this can be changed to make the function take an argument to create multiple dirs if needed. 

    if not os.path.exists(PATH): # if the given dir name doesn't exist the next line creates it.
        os.makedirs(PATH)


def create_dir(import_file :list[str], parent_dir :str) -> None:
    """creates a directory for each line of import_file in the directory given in parent_dir"""
    for line in import_file:

        if not os.path.exists(line):
            os.makedirs("names/" + line, exist_ok=True)


def create_file(import_file :list[str]) -> None:
    """Creates a file with the name on each line of the given list, does not return anything"""
    for line in import_file: # repeats for each name in imported_list

        file_path = os.path.join("names", line) # this returns a file path using the correct seperator. 
        open(file_path, "x") # this creates a file in the folder above.


def user_choice(parent_dir :str) -> None:
    """gets user input to determine the creation of directories or files."""
    choice = input("1 for Directories only, 2 for files only: ").strip().lower()

    if choice == "1":
        create_dir(import_file() , parent_dir)        

    elif choice == "2":
        create_file(import_file())
    
    else:
        print("choice is invalid. 1 or 2 are valid choices.")



def main() -> None:
    print("Main is now running:")

    create_parent_dir()
    user_choice("names")
    

    print("Main has finished running.")

if __name__ == "__main__":
    main()

```


# shell-scripting-intro

SHELL SCRIPTING WOO

The main goal is to familiarise myself with what I can do with shell scripting and to create something somewhat useful.

The intial thing I came up with is a script to reduce the number lines I have to type when I want to use pwd, whoami and hostname:
```
#!/usr/bin/env bash

echo "I am: $(whoami)"
echo "I am at: $(pwd)"
echo "I am on: $(hostname)"

```
This prints all three commands.

I then moved this into /.local/bin and renamed it to "wwh" so that whereever I am on the user I can type "wwh" and it will run.

Another very simple script I made to test out variables was:
```
#!/usr/bin/env bash

echo "Hello $1"
```
If we then run this with:
```
./variable_test.sh OWL
```
We get the output: "Hello OWL"

# virtual-env-check-script

So this is an attempt to automate/ play around with shell scripting and the python packaging stuff found in [Day 39](https://lmno.lol/basowl/day-39-python-packaging-venvs-and-required-package-sharing)

A basic way for us to use bash to check if we are in a virtual env or not:
```
#!env/usr/bin/env bash

if [[-n $VIRTUAL_ENV_PROMPT]]; then
  echo "inside venv"

else
  echo "not inside env"

fi
  ```

A way we can use python to check if we are in a venv is to compare sys.prefix and sys.base_prefix. If they are equal then we are NOT in a venv:

def venvcheck():
  return sys.prefix != sys.base_prefix

# markdown-to-html-converter

I decided to do this in python so save time and to keep it as simple as possible.

The things I need to program to do are:

- Open a file

- Establish what needs converting to what

- Attempt to convert to html

I've been advised that it may be beneficial to first look into a "Recursive Decent Parser"

"A recursive descent parser is a top-down parser that processes input based on a set of recursive functions, where each function corresponds to a grammar rule." - https://www.geeksforgeeks.org/compiler-design/recursive-descent-parser/

I am also in need of a Dictionary: https://www.w3schools.com/python/python_dictionaries.asp



# current-project

A Markov Chain is "stochastic" (having a random probability distribution or pattern that may be analysed statistically but may not be predicted precisely)
Stochastic being a word than is still not within my complete grasp but I think I will get the hang of it as I take a crack at making at the Markov Chain Generator

A Markov Chain is a "process that transitions between states such that the probability of moving to the next state depends solely on the current state"

In the simplest form I understand it is: A process where the future depends ONLY on the PRESENT and there are probabilites of the move that will happen NEXT.

The goal is to begin the project, first using C to try and familiarise myself with the grandfather of coding languages.

Firstly, we need a C compiler on the Linux machine. We're on CentOS 9 so we use: 
```
dnf install gcc
```
[gcc](https://en.wikipedia.org/wiki/GNU_Compiler_Collection) is a COLLECTION of compilers from the GNU Project.
This being MASSIVELY useful for a whole number of programming languages but it initially started as a C compiler.

```
#include <stdio>

int main(){

  printf("C go brr \n");

  return 0;
}

```

So to test if our compiler is working we have to actually call gcc and give our filename, and what we want the output exe to be called:

```
gcc FILENAME -o OUTPUTNAME
```

Now we have checked that we can actually run anything we write

In an effort to save some sanity before diving further into C, I decided to create a rough draft of the project in Python:
```
import random

def open_file(filename: str) -> list:
    with open(filename) as file:
        contents = file.read() #contents now gives us access to the file
        #print(contents)
        file.close()

    return contents

def create_list(filename: str) -> list:
    res = list()

    res.append(open_file(filename).splitlines())

    return res

def gen_length() -> int:
    res = input("How many words?: ")

    return int(res)

def gen_sentence(filename) -> str:

    res = list()
    names = create_list(filename) # list ((entry1, entry2)) list inside of a list with 1 index of 0
    index = names[0][-1] #look into flatten
    last_index = names[0].index(index)

    for _ in range(gen_length()):

        rand_index = random.randrange(0, last_index -1)
        res.append(names[0][rand_index])

    return res


def run():
    print("run: \n")

    print(gen_sentence("test.txt"))

def main():
    print("Begin main: ")

    run()

if __name__ == '__main__':
    main()
```

## Project Start

So to start off, the idea is to try and split the project into the components I would need.

- First, I need a way to import a text file into the project. This is to be able to feed samples into the program:
  ```
  char *open_text_file(const char *filename)
  ```

const char * const means “the function promises not to modify the string data”. char * points to the first character of a string and reads until it hits a "\0"
"\0" is a special character that is called the NULL character or NULL terminator




# blog-text-art-banner
                           █  █ 
██████   █████  ███████   ██████   ██     ██ ██      
██   ██ ██   ██ ██       ██    ██  ██     ██ ██      
██████  ███████ ███████ ██  █ █ ██ ██  █  ██ ██      
██   ██ ██   ██      ██  ██  █ ██  ██ ███ ██ ██      
██████  ██   ██ ███████   ██████    ███ ███  ███████ 
                                                  
                                                   
# blog-about

I'm [@basowl](/basowl).🦉 Within is a journey of learning, frustration and eventual success.<br> 

Contact me by mail: [mail@basowl.com](mailto:mail@basowl.com)

Check out my Github at: https://github.com/BasOwl

My latest project is [here](/basowl/Current-Project)
