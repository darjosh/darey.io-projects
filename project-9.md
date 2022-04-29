
# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

## Step 1 - Install Jenkins server
I created an AWS EC2 server based on Ubuntu Server 20.04 and named it “Jenkins”

![image001](https://user-images.githubusercontent.com/43627963/165943928-cf28aa7b-77c5-453c-a850-603df9dac809.png)

I Installed JDK which is a Java-based application using the commands below
sudo apt update
sudo apt install default-jdk-headless

![image003](https://user-images.githubusercontent.com/43627963/165943947-3aaabbc1-5edd-4ace-9b38-3d497201e704.png)


Next is to Install Jenkins using the commands below
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
 
 
To check if Jenkins is up and running, I used the command below
sudo systemctl status jenkins

![image005](https://user-images.githubusercontent.com/43627963/165943952-9f2a31f4-1001-4c60-936d-72de763f6a84.png)


Another step is to change custom port to 8080
Then the next is to disable the universal firewall
Sudo ufw disable
Then from my browser i accessed the jenkins page using
http://54.165.102.138:8080


![image007](https://user-images.githubusercontent.com/43627963/165943959-d8aefcbd-3bc1-43c2-bcc5-fd6b0481d6ef.png)


I ran the command below to get the default password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Then i logged in ith the password, below is the output
 
![image009](https://user-images.githubusercontent.com/43627963/165943963-89955669-6287-46ff-bba8-4752af658c86.png)


Then i clicked on install suggested plugins

![image011](https://user-images.githubusercontent.com/43627963/165943968-9bb6471c-8e60-4ef0-bbbf-6ed2159846b4.png)

![image013](https://user-images.githubusercontent.com/43627963/165943973-8dc4a7dc-d164-444b-8d36-900580ef0672.png)

![image015](https://user-images.githubusercontent.com/43627963/165943976-4f26e4cc-6f34-46b6-a956-be0ce6624dcd.png)

Click on start using jenkins

![image017](https://user-images.githubusercontent.com/43627963/165943982-0789f370-2f89-4d9a-8b4e-99e3c86f18f1.png)


## Step 2 - I Configured Jenkins to retrieve source codes from GitHub using Webhooks

![image019](https://user-images.githubusercontent.com/43627963/165943987-c131679f-3dd8-4efc-b825-2ad4661ceac2.png)

![image021](https://user-images.githubusercontent.com/43627963/165943992-adfa55fe-363a-44a4-b1f0-ea13add3ad3f.png)

![image023](https://user-images.githubusercontent.com/43627963/165943998-b2481022-7d13-4106-9ac7-b97b40cf640b.png)

![image025](https://user-images.githubusercontent.com/43627963/165944002-3a3e97d4-739e-40db-8f14-c7e8c339ec28.png)

![image027](https://user-images.githubusercontent.com/43627963/165944006-d281dba1-3d4c-4b34-b017-0eda3ac03eea.png)

![image029](https://user-images.githubusercontent.com/43627963/165944014-1697108f-56c7-4f11-9860-e767892c9c72.png)

![image031](https://user-images.githubusercontent.com/43627963/165944019-31dd55f3-2c95-492c-8fd0-b1c98a7d0656.png)

## Step 3 - Configure Jenkins to copy files to NFS server via SSH
![image033](https://user-images.githubusercontent.com/43627963/165944022-bc13afd6-100b-48ad-8cfc-ec513375bb9e.png)

![image035](https://user-images.githubusercontent.com/43627963/165944026-569282c5-af70-43e8-b577-313a0a3d8207.png)

![image037](https://user-images.githubusercontent.com/43627963/165944029-15386c79-b40a-44dd-9b27-118596526cb6.png)

![image039](https://user-images.githubusercontent.com/43627963/165944033-6a0e9219-5445-4341-b98b-da46b9ef3e6c.png)

![image041](https://user-images.githubusercontent.com/43627963/165944040-4d523b1c-cd9d-4f57-b8fd-a466028b96cc.png)
