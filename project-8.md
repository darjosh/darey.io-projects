
# Load Balancer Solution With Apache
## STEP 1- 
To Make use of the servers installed in project 7(two webservers and one mysql DB server) 
and also to create a new ubuntu server named “Project-8-apache-lb”. The screen shot is seen below

![image001](https://user-images.githubusercontent.com/43627963/165940277-53b4538f-574f-44ed-9ba7-c9f4d15204e0.png)


The following commands were ran on the apache load balancer
#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

#Enable following modules:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic



Then the command below was ran to restart the apache

#Restart apache2 service
sudo systemctl restart apache

![image003](https://user-images.githubusercontent.com/43627963/165940282-ab96faed-b16a-48d3-b3d8-165274b686bd.png)

Next step is to Configure load balancing using the following command
sudo vi /etc/apache2/sites-available/000-default.conf


![image005](https://user-images.githubusercontent.com/43627963/165940286-d6ee6da5-f9da-43d5-a299-409ea350f98f.png)

Then , the next is to verify that our configuration works using the command below
The load balancer public ip address
http://34.229.55.61/index.php


![image007](https://user-images.githubusercontent.com/43627963/165940291-c6398f1c-258c-475c-a608-aa1111d5d346.png)


![image009](https://user-images.githubusercontent.com/43627963/165940292-7e2ee8a2-6bc2-42d7-aca4-d3a569a27d6c.png)


