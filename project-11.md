# ANSIBLE – AUTOMATE PROJECT 7 TO 10
## STEP 1 - INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

Using the same server used for Jenkins as our ansible server

Created a new repo called ansible-config-mgt.

Installed Ansible

sudo apt update

sudo apt install ansible

![1](https://user-images.githubusercontent.com/43627963/167319683-6e7c4161-26d4-4cbf-b5b5-93fce310f121.png)
Setup Jenkins project to automatically build the ansible-config-mgt repo once there is an update to master branch

![2](https://user-images.githubusercontent.com/43627963/167319684-a2a15773-c373-4550-9eb7-47a226bac41f.png)

STEP 2 - SETUP PROJECT ON VISUAL STUDIO CODE
I have visual code already installed and project folder opened for

STEP 3 - BEGIN ANSIBLE DEVELOPMENT
Create a new feature branch to develop the ansible playbook

Checkout the feature branch

Create a playbook directory - holds the ansible tasks and operations to be performed.

Create an inventory directory - holds the hosts file.

Create a common.yml file inside the playbook directory. This contains some of tasks to be run on all the hosts.

Create hosts file(dev.yml,staging.yml,uat.yml and prod.yml) inside the inventory directory. These files contains the list of hosts to be managed.

STEP 4 – SETUP ANSIBLE INVENTORY
Update the inventory/dev.yml file with the following content
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
We need to import our .pem file into the ansible server in order to execute the anisble playbook.
We can acheive this by running the following command to add the .pem file to SSH Agent

eval `ssh-agent -s` #starts the ssh agent if its not running.
ssh-add <path-to-private-key> # adds the .pem to the ssh Agent
ssh-add -l # lists the keys in the ssh agent
  
  
![3](https://user-images.githubusercontent.com/43627963/167319685-df33479c-a069-4631-9438-b80109df0e17.png)

  3. SSH into the jenkins server;

ssh -A ubuntu@public-ip
STEP 5 – CREATE ANSIBLE PLAYBOOK
Update the playbook/common.yml file with the following content:
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

    - name: create new directory
      file:
        path: /home/ec2-user/wales
        state: directory    

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

    - name: create new directory
      file:
        path: /home/ubuntu/wales
        state: directory     
Save the project and push the branch to GitHub. Also create a PR to merge the branch to master.
![4](https://user-images.githubusercontent.com/43627963/167319687-354ca9fa-5608-4050-9288-e56a9553555a.png)

Automatic build is triggered on jenkins and the files are copied to the jenkins server at ansible-playbook -i /var/lib/jenkins/jobs/ansible-config-mgt/builds//archive

Run the ansible playbook using the following command:

ansible-playbook -i /var/lib/jenkins/jobs/ansible-config-mgt/builds/<build nu
![5](https://user-images.githubusercontent.com/43627963/167319688-b05cf087-91b5-425d-80c1-5d7e00d10125.png)

                                                                           
Check the server to confirm the changes are reflected. Check if wireshark is installed and the directory is created.
wireshark --version
![6](https://user-images.githubusercontent.com/43627963/167319689-c91b3b15-6982-4f09-8c19-c2f65ed7ae4a.png)
