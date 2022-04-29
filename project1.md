
# DEVOPS TOOLING WEBSITE SOLUTION
## Step 1 - Prepare NFS Server

 Stage 1 -  I created 4 instances using red hat namely: NFS, web-1,web-2,web-3 respectively and also another instance using ubuntu, named as database. The screenshot is displayed below

![image001](https://user-images.githubusercontent.com/43627963/165929968-92e3ece9-01cd-4b82-903d-0f16d6f773da.png)

Stage 2- I created another instance using ubuntu which is named database

Stage 3 - I created a volume named nfs-volume and attached it to NFS instance
![image003](https://user-images.githubusercontent.com/43627963/165930287-25392d82-4dd1-497f-9058-d7df2ebe2576.png)


Stage 4 - connect to nfs instance using mobaxterm
![image005](https://user-images.githubusercontent.com/43627963/165931429-a46c7bc8-aa14-4f58-a74a-f9aec84e0ec4.png)


Stage 5 - using lsblk
![image007](https://user-images.githubusercontent.com/43627963/165931784-76bfc4bb-8b22-46af-b260-9b3b9424504c.png)


Stage 6 - Partitioning the disk
sudo gdisk /dev/xvdf


Stage 7 - run lsblk to see the partitioned disk
![image009](https://user-images.githubusercontent.com/43627963/165931838-f2b86f7c-9ea2-4600-abf2-3d747baececf.png)


Stage 8 - Creation of physical an logical volumes

First install 
sudo yum install lvm2 -y

, then

sudo pvcreate /dev/xvdf1

![image013](https://user-images.githubusercontent.com/43627963/165932095-f83a33fc-9a8a-4bb4-8876-4e41fbf85fd7.png)

![image011](https://user-images.githubusercontent.com/43627963/165932001-3e5ef500-badc-4aa9-8568-f187b110361b.png)

Stage 9 - create logical volumes

sudo lvcreate -n lv-apps -L 5G vg-webdata
sudo lvcreate -n lv-logs -L 5G vg-webdata
sudo lvcreate -n lv-opt -l 100%FREE vg-webdata


![image015](https://user-images.githubusercontent.com/43627963/165932198-f007cf54-ec8c-46fc-b715-188505134e74.png)
![image017](https://user-images.githubusercontent.com/43627963/165932225-eb54f4a8-563d-4991-9d67-3e3fd214e432.png)

Stage 10 - creation of mount points

sudo mkdir /mnt/apps
sudo mkdir /mnt/logs
sudo mkdir /mnt/opt

![image019](https://user-images.githubusercontent.com/43627963/165932270-71611f3b-432a-42fb-815b-153076e34f25.png)

Stage 11 - 
![image021](https://user-images.githubusercontent.com/43627963/165932286-3860ce62-9105-4bcf-a24d-71630be50d9d.png)

Stage 12 - all mounted
![image023](https://user-images.githubusercontent.com/43627963/165932292-12c6bf4d-681e-4a88-8fab-6b84386c983c.png)

Stage 13 - update fstab
![image025](https://user-images.githubusercontent.com/43627963/165932319-b8561781-3305-4a39-beff-63dfe4e21995.png)
![image027](https://user-images.githubusercontent.com/43627963/165932332-7319aed1-634c-4b6c-9aa7-b97cde9e117c.png)

Stage 14 - running the following commands for update

sudo yum update -y

sudo yum install nfs-utils -y

![image029](https://user-images.githubusercontent.com/43627963/165932349-07f3d608-b88a-48d7-a8fb-678f9b46af7e.png)
![image031](https://user-images.githubusercontent.com/43627963/165932365-a1420afa-e372-4a59-94e2-406a66e765ca.png)


Stage 15 - chown, chmod and exports as shown in the screen shots attached below

![image033](https://user-images.githubusercontent.com/43627963/165932380-27ab84ea-fa0d-442d-9c48-0887d3050971.png)

Export using the command below:

sudo exportfs -arv

![image035](https://user-images.githubusercontent.com/43627963/165932394-f0f26b9a-863b-46c7-bc2c-c7168967cbc1.png)


### Step 2 - Configuration of the database server

Stage 1 - running of sudo apt update

![image037](https://user-images.githubusercontent.com/43627963/165932405-246b887f-ad60-4b22-943d-2d20a8c4b558.png)

Stage 2  install mysql - server ,then start mysql

Stage 3 - run the command s below

sudo apt install mysql-server

sudo  systemctl start mysql

sudo  systemctl enable mysql

sudo  systemctl status  mysql

sudo mysql_secure_installation


![image039](https://user-images.githubusercontent.com/43627963/165932425-75bae68b-61ba-4983-a35d-35c8319f4f9d.png)

Stage 4 - Create database tooling

![image041](https://user-images.githubusercontent.com/43627963/165932443-3dfe32ce-3984-45e6-97a7-adbeadb8f9b2.png)

Stage 5-  Next is to run the following commands


![image043](https://user-images.githubusercontent.com/43627963/165932448-c723ee2f-2231-43f5-a6b2-56dd5a178323.png)

Then, next is to open bind address for updation

### Step 3 - Preparation of the web servers

Stage 1 -  install the command on it

sudo yum install nfs-utils nfs4-acl-tools -y


![image045](https://user-images.githubusercontent.com/43627963/165932592-b9c905a0-7cae-4ad2-b713-4df5ccf04ba0.png)

Then restart the nfs server using the commands below

sudo systemctl start nfs-server

Then creation of directory using 

sudo  mkdir /var/www

Then we mount using the command below

sudo mount -t nfs -o rw,nosuid 172.31.39.14:/mnt/apps /var/www

![image047](https://user-images.githubusercontent.com/43627963/165932601-a9a3e662-be2a-480a-844b-940d1be03382.png)


I also Verified that NFS was mounted successfully by running df -h. 
I also made necessary changes here:       sudo vi /etc/fstab
172.31.39.14:/mnt/apps /var/www nfs defaults 0 0

Stage 2 -  installation of  Apache using 
sudo yum install httpd -y


![image049](https://user-images.githubusercontent.com/43627963/165932610-71fb9d44-7dc2-4d60-932d-f8039c99737d.png)


Stage 3 - Trying to backup files before mounting

Sudo mv /var/log/httpd /var/log/httpd.bak

Stage 4 -  sudo yum install git -y

 git clone https://github.com/darey-io/tooling.git 



Stage 5 - 

sudo  vi functions.php

![image051](https://user-images.githubusercontent.com/43627963/165932623-bb440dee-0360-4c55-b5e4-ed44d6a75ca8.png)


![image053](https://user-images.githubusercontent.com/43627963/165932634-a6556365-68d7-4d08-b7d6-e1c72f4aa25f.png)

![image055](https://user-images.githubusercontent.com/43627963/165932652-daa23728-e0d5-45ce-9004-126ce07de209.png)

![image057](https://user-images.githubusercontent.com/43627963/165932661-a1d8eea8-8c46-4e46-8960-0609777676d9.png)

![image059](https://user-images.githubusercontent.com/43627963/165932673-528ffdc5-1e8d-490b-8012-47218aed8a1c.png)

