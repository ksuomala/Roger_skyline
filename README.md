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

Now you can log into your Debian operating system.






