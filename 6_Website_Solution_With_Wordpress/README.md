# Implementing Wordpress-Web-Solution

## 1. Preparing Web Server

* Create an EC2 instance server on AWS

* On the EBS console, create 3 storage volumes for the instance. These serves as additional external storage to our EC2 server.

![](./img/Word1.png)

* Attach the created volumes to the EC2 instance

![](./img/Word2.png)

* SSH into the instance, then view the disks attached using `lsblk` command

![](./img/Word3.png)

* View all mounts and free spaces on the server using `df -h`

![](./img/Word4.png)

* Create a partition on each volume using `gdisk`

![](./img/Word5.png)

![](./img/Word6.png)

* Install LVM2 package to create logical volumes on the server

![](./img/Word7.png)

* Create Physical Volumes on the partitioned disk volumes
`sudo pvcreate <partition_path>`

![](./img/Word8.png)

* Then, add up each physical volumes into a volume group
`sudo vgcreate <grp_name> <ppv_path1>...<pv_path1000>`

![](./img/Word9.png)