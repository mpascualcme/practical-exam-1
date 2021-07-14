# practical-exam-1
July 2021 - Linux Essentials - Practical Exam

Please follow the given instructions carefully, otherwise you risk your answers being marked as wrong. Use the "exam.ova" image under "student workspace/images" in Google Drive.  Make good use of VirtualBox snapshots to protect your progress.

## system configuration - *2 points*
 
- change the server's hosname to "<yourfirstname>.superfly.acme" ensure that this change is persistent.
- ensure that the following message is displayed everytime you ssh into the server - change the sshd banner 

```
********************************************************************
*                                                                  *
* This system is for the use of authorized users only.  Usage of   *
* this system may be monitored and recorded by system personnel.   *
*                                                                  *
* Anyone using this system expressly consents to such monitoring   *
* and is advised that if such monitoring reveals possible          *
* evidence of criminal activity, system personnel may provide the  *
* evidence from such monitoring to law enforcement officials.      *
*                                                                  *
********************************************************************
```

## configure server-class networking - *5 points*

- stop and disable NetworkManager 
- install network-scripts, and set the ip addresses statically for both network cards.
- configure your current ip address, netmask, and gateway in /etc/sysconfig/network-scripts/ifcfg-enpXsY 
- enp0s3 is your primary network card and should be set to NAT from Virtualbox. this is the interface where your default gateway should also be configured. enp0s8 is your secondary network card and should be set to 'host-only networking' from Virtualbox. This is where you SSH to (using putty) 
- ensure that these settings are persistent across reboots 

## user and group management - *5 points*

- create your first user <your first name> and a second user <your last name>. create a group called 'restricted' with a group ID of 1337. (2 points)
- for the first user, ensure that the password will expire every after 5 days, shell is set to /bin/bash, uid is set to 3435. (2 points)
- for the second user, ensure that the password is locked, shell is set to /bin/false, and that the user's primary group is set to 'restricted', and an auxilliary member of the first user's group (3 points)
- configure the first user's sudo rule to be able to run the 'mount' command and read (cat) the file /var/log/messages (1 point)

## LVM and filesystems - *8 points*
- create persistent volumes out /dev/sdb and /dev/sdc block devices
- create a volume group called "VG<firstname>" from all available PV's
- create a logical volume called "LV<firstname>" from VG<firstname>. size is 150MB
- create a filesystem off of this new logical volume. filesystem type is xfs, LABEL with FS<firstname>
- create a directory called /mnt/newhome. Mount the newly-created filesystem there. Ensure that the mount persists across reboots
- create a new user called 'guest'. ensure that this new users home directory is set to /mnt/newhome/guest

## install and configure bind - *10 points*
- ensure that bind is running on the ip address of enp0s8 ONLY
- disable dnssec-validation, and configure a forwarder for 8.8.8.8
- configure a forward and reverse lookup zone with the following information:

```
domain: superfly.acme
for A records,
  <yourfirstname>.superfly.acme pointing to the ip address of enp0s8
  ns pointing to the ip address of enp0s8. this is your authoritative nameserver for superfly.acme
  www.superfly.acme pointing to the ip address of enp0s8
  mail.superfly.acme pointing to the ip address of enp0s8
CNAME record of api.superfly.acme pointing to www.superfly.acme
TTL of 10 minutes
ensure that you have an MX entry poiting to mail.superfly.acme
```
    
- ensure that reverse records are created for all names

## install and configure postfix - *5 points*
    
- ensure that all mails are coming out as <user>@superfly.acme
- only support IPv4
- as root, send an email to guest@superfly.acme with the subject "Italy" and a message body of "We are the champions!"
- as root, send an email to admin@superfly.acme which is an email alias to <yourusername>@superfly.acme. mails to admin must be received by <yourusername>

## install and configure apache web server - *8 points*
    
- install httpd
- ensure that the web service is accessible from your laptop
- display the following content in http://www.superfly.acme/<yourfirstname>.html

```
<!DOCTYPE html>
<html>
<body>
<h1>Hello, World!</h1>
<p>How are you doing?</p>
</body>
</html>
```
    
- create the directory /var/www/websites/fileserver. copy the files below to this directory
  - /etc/logrotate.conf
  - /etc/rsyslog.conf
  - /etc/kdump.conf

- ensure that I can see and download the files when I browse http://www.superfly.acme/fileserver

## SELinux - *3 points*
- configure apache to listen to the ip address of enp0s8 port 5678, in addition to the default port 80. ensure that enp0s3 and localhost is not listening on port 5678.
- ensure that I can see and download the files when I browse http://www.superfly.acme:5678/fileserver

## Docker - *9 points*
- create the directory /root/docker and /root/docker/public-html. Copy /var/www/html/<yourfirstname>.html to /root/docker/public-html/index.html, and change the contents to say
    
```
<!DOCTYPE html>
<html>
<body>
<h1>Hello from Docker!</h1>
<p>How are you doing?</p>
</body>
</html>
```
    
- inside /root/docker, create a docker file. in the Dockerfie, use httpd:2.4 as the base image and serve the contents of public-html
- build a docker image, tag it as 'myapp'. select "docker.io/library/httpd:2.4" when prompted during build
- run an instance of the 'myapp' image. expose the service to port 5566. Ensure that this service is accessible from your laptop browser. write the 'docker run' command that you used to /root/docker/commands.txt

## scripting and vim - *5 points*
- write a script that will download http://www.superfly.acme/fileserver/kdump.conf 10 times, at 30 seconds interval each, saving the file each time to /dev/null
- copy /usr/share/doc/dmidecode/README to /root/dmidecode.README. using vim, replace all the instances of the word "can" with "may". At the end of the file, type in the command that you used to perform this change.

## finally
- shut down your VM by running 'poweroff'. Ensure that your configurations are persistent across reboots.
- export the virtual machine by clicking 'File' > Export Appliance in VirtualBox. Choose "open virtualization format 2.0" and name the file as <yourfirstname.ova>. Upload to the folder where you originally downloaded "exam.ova"

NOTES:

- if you are missing any commands, you need to install the corresponding package that contains those commands. Example:

```
[root@localhost ~]# wget
-bash: wget: command not found

[root@localhost ~]# yum provides *bin/wget <------------------------------------------- pay attention to the pattern here. *bin/<command>
Last metadata expiration check: 0:23:25 ago on Tue 13 Jul 2021 09:07:28 PM PST.
wget-1.19.5-10.el8.x86_64 : A utility for retrieving files using the HTTP or FTP protocols <----  this is the package name. "wget-1.19.5...". you can strip out the versions and just install "wget"
Repo        : appstream
Matched from:
Other       : *bin/wget

[root@localhost ~]# yum -y install wget
Last metadata expiration check: 0:21:25 ago on Tue 13 Jul 2021 09:07:28 PM PST.
Dependencies resolved.
============================================================================================================================================
 Package                                    Architecture         Version                                         Repository            Size
============================================================================================================================================
Installing:
 wget
...
```
                                                                                                 
                                                                                              
                                                                                                 
                                                                                                 - 
                                                                                                 
