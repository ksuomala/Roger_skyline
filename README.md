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
For the network on your VM, here are the steps to achieve:
• You must create a non-root user to connect to the machine and work.
  This was done during Debian installation

• Use sudo, with this user, to be able to perform operation requiring special rights.
  Install sudo with ```apt-get install sudo```.
  Add the user account to the group sudo with ```/sbin/adduser username sudo```. Where username is your user account.
  To test that it works we can log out, log back in and run ```sudo echo "I'm a sudo user"```.
  
Before setting the static ip I installed net-tools so I can use sudo ifconfig ```$ sudo apt-get install net-tools```
  
• We don’t want you to use the DHCP service of your machine. You’ve got to
configure it to have a static IP and a Netmask in \30.
  Enable static ip:
  Go to VirtualBox settings - Network. Change NAT to Bridged Adapter.
  

Create a new network interface configuration. I chose the next ip address from my host:

```
# cat /etc/network/interfaces.d/eth0
iface enp0s3 inet static
      address your_static_ip
      netmask 255.255.255.252
      gateway 10.11.254.254
```   

and modify:

```
# cat /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp0s3
```
  
  
```
  sudo service networking restart
  ip a
```  
  
  if if enp0s3 is down run ```ip link set enp0s3 up```
  
  --------------------------------
• You have to change the default port of the SSH service by the one of your choice.

  Modify the sshd configuration file: ```/etc/ssh/sshd_config```
  
  Change Port 22 to a port of your choise. Use an appropriate port, also making sure that it's not being currently
  used by the system. The Well Known Ports are those from 0 through 1023 and SHOULD NOT be used. Registered Ports are those from 1024 through 49151 should   also be avoided. Dynamic and/or Private Ports are those from 49152 through 65535 and can be used.  I used 50683. (according to IANA).
  
  Switch over to the new port by restarting ssh: ```sudo /etc/init.d/ssh restart```
 
  You can check your ssh service status by with: ```sudo systemctl status ssh```
  
  Now we can connect to the server via ssh using ```ssh username@hostname.com -p 50683```

  You can get your ip with: ```hostname -I```
  
• SSH access HAS TO be done with publickeys. SSH root access SHOULD NOT
  vbe allowed directly, but with a user who can be root.

  You can check your ssh service status  with: ```sudo systemctl status ssh```

  Generate rsa keypair: ```ssh-keygen -t rsa```
  
  Install the key to your server: ``` ssh-copy-id -p 50683 -i ~/.ssh/id_rsa.pub user@hostname```
  
  To disable root SSH login, edit /etc/ssh/sshd_config. Change the line  ```# PermitRootLogin yes``` to ```PermitRootLogin no```
  
  Restart the SSH daemon: ```sudo service sshd restart.```
  
• You have to set the rules of your firewall on your server only with the services used
outside the VM.

```
$ sudo apt install ufw
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
$ sudo ufw allow 443
$ sudo ufw allow 80/tcp
$ sudo ufw allow 50683/tcp
```


• You have to set a DOS (Denial Of Service Attack) protection on your open ports
of your VM.

  For testing this we need apache: ```$ sudo apt install apache2```
  
  I used these instructions next: https://www.garron.me/en/go2linux/fail2ban-protect-web-server-http-dos-attack.html

  ```$ sudo apt-get install fail2ban```
  

  Modify fail2ban.local:
  
  ```
  $ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
  $ sudo vim /etc/fail2ban/fail2ban.local
  ```
  ```
  #
  # JAILS
  #
  
  # 
  # SSH servers
  #
  
  [sshd]
  
  # To use more aggressive sshd modes set filter parameter "mode" in jail.local:
  # normal (default), ddos, extra or aggressive (combines all).
  # See "tests/files/logs/sshd" or "filtesr.d/sshd.conf" for usage example and details.
  #mode   = normal
  enable  = true
  port    = ssh
  logpath = %(sshd_log)s
  backend = %(sshd_backend)s
  maxentry = 3
  bantime = 600
  ```
  Add this to the end of the file:
  
  ![http-get-dos](https://github.com/ksuomala/Roger_skyline/blob/main/img/Screenshot%202022-04-09%20at%2016.07.06.png)
  
Now we need to create a filter. 
```$ sudo nano /etc/fail2ban/filter.d/http-get-dos.conf```
```
[Definition]

failregex = ^ -.GET
ignoreregex =
```

```
$ sudo ufw reload
$ sudo service fail2ban restart
```


• You have to set a protection against scans on your VM’s open ports.
• Stop the services you don’t need for this project.
• Create a script that updates all the sources of package, then your packages and
which logs the whole in a file named /var/log/update_script.log. Create a scheduled
task for this script once a week at 4AM and every time the machine reboots.
• Make a script to monitor changes of the /etc/crontab file and sends an email to
root if it has been modified. Create a scheduled script task every day at midnight.







