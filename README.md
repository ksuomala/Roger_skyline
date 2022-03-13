# Roger_skyline
Configuring a web server on a virtual machine. Hive Helsinki project.

Tutorial:

part I

Subject:

"You have to run a Virtual Machine (VM) with the Linux OS of your choice (Debian
Jessie, CentOS 7...) in the hypervisor of your choice (VMWare Fusion, VirtualBox...).
• A disk size of 8 GB.
• Have at least one 4.2 GB partition.
• It will also have to be up to date as well as the whole packages installed to meet
the demands of this subject."

I chose Debian for the operating system. I downloaded debian-11.2.0-amd64-netinst.iso from https://www.debian.org.


![operating system](https://github.com/ksuomala/Roger_skyline/blob/main/img/Screenshot%202022-03-13%20at%2013.52.14.png)

We create a virtual hard disk for 8,00 GB

Next we click start and choose the Debian image that we downloaded

![choose_image](https://github.com/ksuomala/Roger_skyline/blob/main/img/choose_image.png)

Choose install and configure locale settings.

The install is pretty straightforward, but pay attention to the partitioning.
Choose manual. There should be one partition on the list. Choose that one and continue.

![choose_partition](https://github.com/ksuomala/Roger_skyline/blob/main/img/choose_partition.png)
![partition](https://github.com/ksuomala/Roger_skyline/blob/main/img/partition.png)
Choose a partition size of 4.2 GB. Type will be primary and it will be at the beginning.
Mount point for this partition will be at the root ```/```.

Then we create another logical partition with the mount point at ```/home``` for the remaining 4,40 GB.

![partition](https://github.com/ksuomala/Roger_skyline/blob/main/img/second_partition.png)

Click "Finish partitioning and write changes to disk"

Now you can log into your Debian operating system. Desktop environment does not need to be installed.

Then we make sure that our Debian and packages are up to date as root:
```
su
apt-get update
apt-get upgrade
```
Part II:
Subject: 
"For the network on your VM, here are the steps to achieve:
• You must create a non-root user to connect to the machine and work.
• Use sudo, with this user, to be able to perform operation requiring special rights.
• We don’t want you to use the DHCP service of your machine. You’ve got to
configure it to have a static IP and a Netmask in \30.
• You have to change the default port of the SSH service by the one of your choice.
SSH access HAS TO be done with publickeys. SSH root access SHOULD NOT
be allowed directly, but with a user who can be root.
• You have to set the rules of your firewall on your server only with the services used
outside the VM.
• You have to set a DOS (Denial Of Service Attack) protection on your open ports
of your VM.
• You have to set a protection against scans on your VM’s open ports.
• Stop the services you don’t need for this project.
• Create a script that updates all the sources of package, then your packages and
which logs the whole in a file named /var/log/update_script.log. Create a scheduled
task for this script once a week at 4AM and every time the machine reboots.
• Make a script to monitor changes of the /etc/crontab file and sends an email to
root if it has been modified. Create a scheduled script task every day at midnight."





