# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY
BACKSTORY:
Built a secure infrastructure inside AWS VPC (Virtual Private Cloud) network for a company that uses WordPress CMS for its main business website, and a Tooling Website for their DevOps team. As part of the company’s desire for improved security and performance, a decision was made to use a reverse proxy technology from NGINX to achieve this.

The infrastructure for both websites, WordPress and Tooling, is resilient to Web Server’s failures, can accomodate to increased traffic and, at the same time, has reasonable cost.



STEP 1: SET UP ORGANISATION UNIT (OU) and a SUB-ACCOUNT
Properly configure your AWS account and Organization Unit (https://youtu.be/9PQYCc_20-Q)
Created an AWS Master account. (Also known as Root Account)

Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)

Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there)

Move the DevOps account into the Dev OU.

Login to the newly created AWS account using the new email address.

Created a free domain name for company at Freenom domain registrar here (https://www.freenom.com/).

Created a hosted zone in AWS, and map it to your free domain from Freenom. Check how to do this here (https://youtu.be/IjcHp94Hq8A).

STEP 2: CREATE AN AWS VPC
1. Set Up a Virtual Private Network (VPC)
2. Created a VPC

3. Created subnets as shown in the architecture

![createSubnet](https://user-images.githubusercontent.com/43627963/170472347-7bbe686b-25ca-4e99-b61c-20d2a61edefe.png)

![subnets](https://user-images.githubusercontent.com/43627963/170472356-ed69642f-89ad-419a-8514-28806147cd8a.png)

Created a route table and associate it with public subnets

![associateSubnet](https://user-images.githubusercontent.com/43627963/170472646-6b83ae82-83fc-4a2e-803c-1e8ca85ac3f7.png)

4. Created a route table and associate it with private subnets
![private-route-subnet](https://user-images.githubusercontent.com/43627963/170472659-1c6ed753-d2c8-4696-9012-bc8a14a5365c.png)

5. Created an Internet Gateway

6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)


![elasticIPs](https://user-images.githubusercontent.com/43627963/170473523-47bf81cc-3517-4cde-b600-7ff00d92cca0.png)

7. Created 3 Elastic IPs
![natGateway](https://user-images.githubusercontent.com/43627963/170473533-a24395c0-5e8e-4d26-b47b-3f3297d550fd.png)
8. Created a Nat Gateway and assigned one of the Elastic IPs (*The other 2 was used by Bastion hosts)

![publicSubnetGateway](https://user-images.githubusercontent.com/43627963/170473534-08bd76f8-c16b-4786-9f5c-01b6316f62e9.png)

9. Created security groups for different services in the architecture

Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a load balancer, therefore we will update the rules later. For now, just create it and put some dummy records as a place holder.

Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. (This is the IP address that you will use to SSH into the bastion servers). **On inbound rules, pick TCP (22) and choose my IP (just to prevent an attempt from a hacker to SSH into the bastion servers). **

Application Load Balancer: ALB will be available from the Internet

Webservers: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a place holder, we will update it later.

Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.

![securityGroups](https://user-images.githubusercontent.com/43627963/170474843-f84d856e-60ba-4864-a69e-7f649eb2bbbe.png)

Set Up Compute Resources for Nginx
Provision EC2 Instances for Nginx
Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region close to the target users. Use EC2 instance of T2 family (e.g. t2.micro or similar)

Ensure that it has the following software installed: python, ntp,net-tools,vim,wget,telnet,epel-release,htop.

Create an AMI out of this instance

Prepare Launch Template For Nginx (One Per Subnet)
Make use of the AMI to set up a launch template 

Ensure the Instances are launched into a public subnet (public subnet 1 or 2)

![instanceTemplate](https://user-images.githubusercontent.com/43627963/170475084-dc471488-4457-4081-ba63-7811ffe923fd.png)

3. Assign appropriate security group (NGINX SG)

4. Configure Userdata to update yum package repository and install nginx

choose the instance name and stop it
click on actions >> instance settings >> edit userdata to create a userdata script
click on the userdata script to edit it
Content-Type: multipart/mixed; boundary="//"
 MIME-Version: 1.0

 --//
 Content-Type: text/cloud-config; charset="us-ascii"
 MIME-Version: 1.0
 Content-Transfer-Encoding: 7bit
 Content-Disposition: attachment; filename="cloud-config.txt"

 #cloud-config
 cloud_final_modules:
 - [scripts-user, always]

 --//
 Content-Type: text/x-shellscript; charset="us-ascii"
 MIME-Version: 1.0
 Content-Transfer-Encoding: 7bit
 Content-Disposition: attachment; filename="userdata.txt"

 #!/bin/bash
 yum update -y
 yum install nginx -y
 --//--
click on the save button
start the instance.
Configure Target Groups
Go to Target Groups section and create a new target group.

Select Instances as the target type
Ensure the protocol HTTPS on secure TLS port 443
Ensure that the health check path is /healthstatus
Register Nginx Instances as targets
Ensure that health check passes for the target group

![targetGroup](https://user-images.githubusercontent.com/43627963/170475365-bc690edd-e2de-45c7-bb19-ee6c6c7fdd07.png)

Configure Autoscaling For Nginx
1. Select the right launch template

![autoscale1](https://user-images.githubusercontent.com/43627963/170475525-13356436-97b2-409c-a8a4-533cc616e280.png)
2. Select the VPC

![autoscale2](https://user-images.githubusercontent.com/43627963/170475709-d48b60b7-3380-4487-87e5-0faabeb65e4a.png)

3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG)

![autoscale3](https://user-images.githubusercontent.com/43627963/170475828-65479674-e506-4815-a3cc-61f7efcd0020.png)

5. Select the target group you created before
6. Ensure that you have health checks for both EC2 and ALB

![autoscale4](https://user-images.githubusercontent.com/43627963/170476218-bb0566af-3fd0-4795-80db-5160f68ca015.png)
 ![autoscale4](https://user-images.githubusercontent.com/43627963/170476218-bb0566af-3fd0-4795-80db-5160f68ca015.png)
 
7. The desired capacity is 2
8. Minimum capacity is 2
9. Maximum capacity is 4
10. Set scale out if CPU utilization reaches 90%
11. Ensure there is an SNS topic to send scaling notifications

![autoscale6](https://user-images.githubusercontent.com/43627963/170478052-2a65901d-2316-4e9d-be2f-aff9c6beb3fc.PNG)

Set Up Compute Resources for Bastion
Provision the EC2 Instances for Bastion
Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ where you created Nginx server
Ensure that it has the following software installed: python, ntp,net-tools,vim,wget,telnet,epel-release,htop.
Associate an Elastic IP with each of the Bastion EC2 Instances
Create an AMI out of the EC2 instance
Prepare Launch Template for Bastion (One Per Subnet)
Make use of the AMI to set up a launch template

Ensure the Instances are launched into a private subnet (private subnet 1 or 2)

Assign appropriate security group (Bastion SG)

Configure Userdata to update yum package repository and install ansible and git

choose the instance name and stop it
click on actions >> instance settings >> edit userdata to create a userdata script
click on the userdata script to edit it
Content-Type: multipart/mixed; boundary="//"
 MIME-Version: 1.0

 --//
 Content-Type: text/cloud-config; charset="us-ascii"
 MIME-Version: 1.0
 Content-Transfer-Encoding: 7bit
 Content-Disposition: attachment; filename="cloud-config.txt"

 #cloud-config
 cloud_final_modules:
 - [scripts-user, always]

 --//
 Content-Type: text/x-shellscript; charset="us-ascii"
 MIME-Version: 1.0
 Content-Transfer-Encoding: 7bit
 Content-Disposition: attachment; filename="userdata.txt"

 #!/bin/bash
 yum update -y
 yum install ansible git  -y
 --//--
click on the save button
start the instance.
Configure Target Groups
Go to Target Groups section and create a new target group.

Select Instances as the target type
Ensure the protocol HTTPS on secure TLS port 443
Ensure that the health check path is /healthstatus
Register Bastion Instances as targets
Ensure that health check passes for the target group
Configure Autoscaling For Bastion
Select the right launch template
Select the VPC
Select both public subnets
Enable Application Load Balancer for the AutoScalingGroup (ASG)
Select the target group you created before
Ensure that you have health checks for both EC2 and ALB
The desired capacity is 2
Minimum capacity is 2
Maximum capacity is 4
Set scale out if CPU utilization reaches 90%
Ensure there is an SNS topic to send scaling notifications
Set Up Compute Resources for Webservers
Provision the EC2 Instances for Webservers
create 2 separate launch templates for both the WordPress and Tooling websites

Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).
N.B: I used userdata to install the apps in step 2 below:

At the **Configure Instance stage ** in creating an instance, scroll down to userdata session and insert the following:
Content-Type: multipart/mixed; boundary="//"
MIME-Version: 1.0

--//
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-config.txt"

#cloud-config
cloud_final_modules:
- [scripts-user, always]

--//
Content-Type: text/x-shellscript; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="userdata.txt"

#!/bin/bash
yum update -y
yum install ntp-y
yum install net-tools -y
yum install vim -y
yum install wget -y
yum install telnet -y
yum install epel-release -y
yum install htop -y
yum install php -y
--//--
You wii tweak the script above to add yum install wordpress -y to the userdata script to install WordPress (for the wordpress instance ). 2. Ensure that it has the following software installed: python, ntp,net-tools,vim,wget,telnet,epel-release,htop,php.

Create an AMI out of the EC2 instance
Prepare Launch Template For Webservers (One per subnet)
Make use of the AMI to set up a launch template
Ensure the Instances are launched into a private subnet (private subnet 1 or 2)
Assign appropriate security group (Webservers SG)
STEP 3: TLS Certificates From Amazon Certificate Manager (ACM)
You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB).

1. Navigate to AWS ACM
2. Request a public wildcard certificate for the domain name you registered in Freenom
3. Use DNS to validate the domain name.
4. Tag the resource
5. Read the documentation here to learn how to validate DNS with Route 53

![demoCertificate](https://user-images.githubusercontent.com/43627963/170478146-c34c46c9-cd06-4ab6-86ea-5de94b9295e6.png)

STEP 4: CONFIGURE APPLICATION LOAD BALANCER (ALB)
Application Load Balancer To Route Traffic To NGINX
Nginx EC2 Instances will have configurations that accepts incoming traffic only from Load Balancers. This will allow us to offload SSL/TLS certificates on the ALB instead of Nginx. Therefore, Nginx will be able to perform faster since it will not require extra compute resources to valifate certificates for every request.

1. Create an Internet facing ALB

2. Ensure that it listens on HTTPS protocol (TCP port 443)

3. Ensure the ALB is created within the appropriate VPC | AZ | Subnets

![alb1](https://user-images.githubusercontent.com/43627963/170478407-5510af08-de50-4787-96c8-5098c832c97f.png)

4. Choose the Certificate from ACM

![alb2](https://user-images.githubusercontent.com/43627963/170478667-78f13bc0-a68f-4061-99e2-2882436674a4.png)

5. Select Security Group

![alb3](https://user-images.githubusercontent.com/43627963/170478791-15a3724a-931c-4a17-a4b0-40f8c391c03b.png)

6. Select Nginx Instances as the target group

![alb4](https://user-images.githubusercontent.com/43627963/170478952-0f2192d5-c126-44eb-842c-c5dd695688eb.png)

Application Load Balancer To Route Traffic To Web Servers (this would be repeated for Wordpress webserver and tooling webservers)
Because of autoscaling, Nginx will not know about the new IP addresses, or the ones that get removed when there is a scale-out of the instances. Hence, Nginx will not know where to direct the traffic.

To solve this problem, we must use a load balancer. But this time, it will be an internal load balancer since the webservers are within a private subnet, and we do not want direct access to them.

Create an Internal ALB
Ensure that it listens on HTTPS protocol (TCP port 443)
Ensure the ALB is created within the appropriate VPC | AZ | Subnets
Choose the Certificate from ACM
Select Security Group
Select webserver Instances as the target group
Ensure that health check passes for the target group
STEP 5:Setup EFS
Create an EFS filesystem
Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer

![EFS1](https://user-images.githubusercontent.com/43627963/170480017-7290d596-4c7b-49ca-bf7e-741b398d88ec.png)

3. Associate the Security groups created earlier for data layer.
4. Create an EFS access point. (Give it a name and leave all other settings as default)

![EFS2](https://user-images.githubusercontent.com/43627963/170480632-bc369dc9-a888-4033-87fc-fa91270a7009.png)

STEP 6: Setup RDS
We need to create a KMS to encrypt our database as a security measure.

To ensure that our databases are highly available and also have failover support in case one availability zone fails, we will configure a multi-AZ set up of RDS MySQL database instance.

1. Create a subnet group and add 2 private subnets (data Layer)

![subnetGroup](https://user-images.githubusercontent.com/43627963/170480779-b26de4f8-be26-4b07-9fb9-abb190a1d0df.png)

2. Create an RDS Instance for mysql 8..
![RDS1](https://user-images.githubusercontent.com/43627963/170480790-9abe4257-78c4-4dc3-b7ca-5c6446b474c7.png)


3. To satisfy our architectural diagram, select either Dev/Test or Production Sample Template. But to minimize AWS cost, selected the Do not create a standby instance option under Availability & durability sample template (The production template will enable Multi-AZ deployment)

![RDS2](https://user-images.githubusercontent.com/43627963/170480968-ccb8142c-fe30-42c5-9e26-5730068954b3.png)

4. Configure other settings accordingly (For test purposes, most of the default settings are good to go).
5. Configure VPC and security (ensure the database is not available from the Internet)
6. Configure backups and retention
7. Encrypt the database using the KMS key created earlier
8. Enable CloudWatch monitoring and export Error and Slow Query logs (for production, also include Audit).

![RDS3](https://user-images.githubusercontent.com/43627963/170481088-784e2388-1cd5-4b5a-911e-1d6643145bb7.png)
STEP 6: Configuring DNS with Route53
You can use either CNAME or alias records to achieve the same thing. But alias record has better functionality because it is a faster to resolve DNS record, and can coexist with other records on that name.

Create an alias record for the root domain and direct its traffic to the ALB DNS name.

![alias1](https://user-images.githubusercontent.com/43627963/170481251-9fc7e541-c98d-488a-aea9-33ed774fb9ea.png)

![alias2](https://user-images.githubusercontent.com/43627963/170481784-a2c5489f-3b41-4fe2-89b4-5b717e77c6d1.png)


