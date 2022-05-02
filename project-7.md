
# DEVOPS TOOLING WEBSITE SOLUTION

## Step 1 - Prepare NFS Server

Stage 1 -  I created 4 instances using red hat namely: NFS, web-1,web-2,web-3 respectively and also another instance using ubuntu, named as database. 
The screenshot is displayed below

![image001](https://user-images.githubusercontent.com/43627963/166310751-81f2b8f0-8584-4c8c-ad35-cb7ba106295c.png)



Stage 2- I created another instance using ubuntu which is named database

Stage 3 - I created a volume named nfs-volume and attached it to NFS instance

![image003](https://user-images.githubusercontent.com/43627963/166310756-c1f15961-7eab-447f-9dd7-74dbad5e530b.png)




Stage 4 - connect to nfs instance using mobaxterm


Stage 5 - using lsblk

![image007](https://user-images.githubusercontent.com/43627963/166310760-97d4aeab-596b-4adc-a372-87d689f01a2a.png)
Stage 6 - Partitioning the disk
sudo gdisk /dev/xvdf

![image009](https://user-images.githubusercontent.com/43627963/166310769-ae0309b5-0172-45d9-bdcd-3e4349021c80.png)
Stage 7 - run lsblk to see the partitioned disk

![image011](https://user-images.githubusercontent.com/43627963/166310783-6ebf666d-d210-48c4-925b-d47b74574cbc.png)




Stage 8 - Creation of physical an logical volumes

First install 
sudo yum install lvm2 -y

, then

sudo pvcreate /dev/xvdf1


sudo vgcreate vg-webdata /dev/xvdf1


![image013](https://user-images.githubusercontent.com/43627963/166310788-f4ca38f2-5a7f-46e9-b580-be1eb37a30f5.png)


Stage 9 - create logical volumes

sudo lvcreate -n lv-apps -L 5G vg-webdata
sudo lvcreate -n lv-logs -L 5G vg-webdata
sudo lvcreate -n lv-opt -l 100%FREE vg-webdata

![image017](https://user-images.githubusercontent.com/43627963/166310794-47e150bc-38d7-4188-8b85-1c94630e142d.png)


Stage 10 - creation of mount points

sudo mkdir /mnt/apps
sudo mkdir /mnt/logs
sudo mkdir /mnt/opt

![image019](https://user-images.githubusercontent.com/43627963/166310795-11de19fe-4b40-46b9-a875-23ca5f9ec9a5.png)


Stage 11 - 

![image021](https://user-images.githubusercontent.com/43627963/166310807-b73bb96d-9d07-4c43-aaf7-eb72e410ed3c.png)


Stage 12 - all mounted

![image023](https://user-images.githubusercontent.com/43627963/166310809-94dda663-c4b7-4bc7-8651-52f0db8275c1.png)


  
Stage 13 - update fstab

![image025](https://user-images.githubusercontent.com/43627963/166310815-ab8571d1-3c80-4d95-8d84-fa3bf4768bcf.png)
![image027](https://user-images.githubusercontent.com/43627963/166310817-f1e961eb-eb64-4168-8bbd-cc20f0c825ba.png)




Stage 14 - running the following commands for update

sudo yum update -y

sudo yum install nfs-utils -y


![image029](https://user-images.githubusercontent.com/43627963/166310819-3a919d86-4ab8-447e-a72b-25ea41031805.png)


Stage 15 - chown, chmod and exports as shown in the screen shots attached below



![image031](https://user-images.githubusercontent.com/43627963/166310834-a334c454-bca4-4f8d-88c5-3dad86b29c2d.png)



Export using the command below:

sudo exportfs -arv
![image035](https://user-images.githubusercontent.com/43627963/166310839-8810768f-3467-465e-8b4b-6e8f675d3ec4.png)



## Step 2 - Configuration of the database server

Stage 1 - running of sudo apt update
![image037](https://user-images.githubusercontent.com/43627963/166310865-564cf27f-f2dc-4c23-bf8b-302ebb5ffe96.png)


Stage 2  install mysql - server ,then start mysql

Stage 3 - run the command s below

sudo apt install mysql-server

sudo  systemctl start mysql

sudo  systemctl enable mysql

sudo  systemctl status  mysql

sudo mysql_secure_installation

![image039](https://user-images.githubusercontent.com/43627963/166310875-4f4e915e-e314-431b-885b-799c4a260bdc.png)


Stage 4 - Create database tooling

![image041](https://user-images.githubusercontent.com/43627963/166310913-35ab5a31-af0b-460c-8fa2-3fc2e2388ca0.png)


Stage 5-  Next is to run the following commands
![image043](https://user-images.githubusercontent.com/43627963/166310941-3402975f-76d8-47bb-a63a-b75bddbb642e.png)




Then, next is to open bind address for updation

## Step 3 - Preparation of the web servers

Stage 1 -  install the command on it

sudo yum install nfs-utils nfs4-acl-tools -y



Then restart the nfs server using the commands below

sudo systemctl start nfs-server

Then creation of directory using 

sudo  mkdir /var/www

Then we mount using the command below

sudo mount -t nfs -o rw,nosuid 172.31.39.14:/mnt/apps /var/www


 
I also Verified that NFS was mounted successfully by running df -h. 
I also made necessary changes here:       sudo vi /etc/fstab
172.31.39.14:/mnt/apps /var/www nfs defaults 0 0

Stage 2 -  installation of  Apache using 
sudo yum install httpd -y


Stage 3 - Trying to backup files before mounting

Sudo mv /var/log/httpd /var/log/httpd.bak

Stage 4 -  sudo yum install git -y

 git clone https://github.com/darey-io/tooling.git 



Stage 5 - 

sudo  vi functions.php



![image045](https://user-images.githubusercontent.com/43627963/166310945-739da4db-58dc-46b9-ab3d-2d17595ed5de.png)
![image047](https://user-images.githubusercontent.com/43627963/166310952-ecbd9e7b-4993-4035-9697-dcd6b81326b4.png)
![image049](https://user-images.githubusercontent.com/43627963/166310959-a3171d39-ec55-44d6-87b4-19bf03ed0302.png)
![image051](https://user-images.githubusercontent.com/43627963/166310964-7cf74039-2907-4725-accd-886e3940377e.png)
![image053](https://user-images.githubusercontent.com/43627963/166310968-2f93ee1b-e098-45b1-803c-4fa891004bac.png)
![image055](https://user-images.githubusercontent.com/43627963/166310976-7bed6324-cf31-419d-a449-2cd6028f7f06.png)
![image057](https://user-images.githubusercontent.com/43627963/166310986-b797bf69-c26c-4d6b-b048-bc426f215699.png)
![image059](https://user-images.githubusercontent.com/43627963/166310990-5504c673-1404-4646-9104-f4a86be7f8b7.png)


   






v
