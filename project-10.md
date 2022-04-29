
# Load Balancer Solution With Nginx and SSL/TLS

## STEP 1
 
TO Configure Nginx As A Load Balancer  
I Created an EC2 VM based on Ubuntu Server 20.04 and named it “ Nginx LB” 
Then I updated /etc/hosts Then the Installation of  Nginx is done with the following commands
sudo apt update
sudo apt install nginx
sudo vi /etc/nginx/nginx.conf
 

I also got a free domain from freenom.com

![image001](https://user-images.githubusercontent.com/43627963/165947054-f6aab2dc-d94d-46d4-872e-0031898710af.png)


![image003](https://user-images.githubusercontent.com/43627963/165947087-3239d75c-1809-4cf9-adb3-29fe9c01a982.png)


![image005](https://user-images.githubusercontent.com/43627963/165947099-244ec698-da5e-4bcf-b6b9-3e1e20ca7eba.png)


![image007](https://user-images.githubusercontent.com/43627963/165947102-a1865e9c-53d9-4cb9-9133-f4e0869a5733.png)


![image009](https://user-images.githubusercontent.com/43627963/165947105-17743370-307f-4ab3-8e22-c8a4f8311c2f.png)


![image011](https://user-images.githubusercontent.com/43627963/165947109-42310a24-e4f6-45f8-8753-8c871724be33.png)

The next is to check the status of nginx
I updated the server using the commands below:
sudo apt update && sudo apt install nginx –y
sudo systemctl enable nginx && sudo systemctl start nginx
sudo systemctl status nginx

![image013](https://user-images.githubusercontent.com/43627963/165947114-d2c73d98-5486-4229-9b89-7965816e5eba.png)


![image015](https://user-images.githubusercontent.com/43627963/165947125-08bf66d5-639c-4f21-b720-1fb42ad85a9c.png)


![image017](https://user-images.githubusercontent.com/43627963/165947131-e5f776f6-b585-4fb9-9a18-74f6b8ffecb5.png)


![image019](https://user-images.githubusercontent.com/43627963/165947133-42b92b27-9cdc-4703-a31c-ce759d7dfbf1.png)

I also secured the webpage using certbot
sudo apt install certbot –y
sudo apt install python3-certbot-nginx –y
sudo nginx t && sudo nginx –s reload
sudo certbot –nginx –d joshuaseg.tk  –d  www.joshuaseg.tk

![image021](https://user-images.githubusercontent.com/43627963/165947136-510fc24c-f38b-4528-93e2-801b07f6740f.png)


![image023](https://user-images.githubusercontent.com/43627963/165947139-2a5090e3-7318-446a-bf5f-289af6e5ae3a.png)


![image025](https://user-images.githubusercontent.com/43627963/165947144-514d1b60-95bb-4865-a7cc-977cbf85515f.png)
