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

* Create logical volumes for the volume group
`sudo lvcreate -n <lv_name> -L <lv_size> <vg_name>`

![](./img/Word10.png)

* Our logical volumes are to be used as filesystems for storing application and log data.
* Creating filesystems on the both logical volumes

![](./img/Word11.png)

* Web contents to be used by the apache webserver are stored in the `/var/www/html` folder. A directory for collecting log data was also created

![](./img/Word12.png)

* The filesystem was mounted on the apache directory `/var/www/html`. Also the logs filesystem was mounted on the log directory `/var/log`

![](./img/Word13.png)

* Mount logs logical volume to var logs

![](./img/Word14.png)

* Restoring back var logs data into var logs 

![](./img/Word15.png)

### Persisting Mount Points

* To ensure that all our points are not erased on restarting the server, we persist the mount points by configuring the `/etc/fstab` directory

* `sudo blkid` to get UUID of each mount points

![](./img/Word16.png)

* `sudo vi /etc/fstab` to edit the file

![](./img/Word17.png)

* Testing mount point persistence

![](./img/Word18.png)

## 2. Preparing Database Server

* Repeated all the steps taken to configure the webserver on the dbserver. Changed `apps-lv` logical volume to `db-lv`, also mounted the database on `/var/db`

![](./img/Word19.png)

## 3. Configuring Web Server

* run update and install httpd on web server
```
sudo yum install -y update
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```

* Start web server

![](./img/word20.png)

* Installing php and its dependencies
```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

* Restarting Apache `sudo systemctl restart httpd`

* Downloading wordpress and moving it into the web content delivery
```
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```