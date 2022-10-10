![ansible-1200x385](https://user-images.githubusercontent.com/57935226/191761218-a5623f9a-4632-4345-8d15-3f5b287137d5.jpg)
# AWS Inventory example 
```
Ec2 ansible_host=10.10.10.3
```



### Necessary packages  
```
yum –y install python-boto python-boto3 
yum –y install awscli 
```
### testing connecting with PING
```
Ansible ec2 –m ping –u cloud_user
```
# Inventory file Configuration Examples
```
### Linux Servers  
sql_db1 ansible_host=sql01.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass  
sql_db2 ansible_host=sql02.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass  
   
### Windows Servers  
web_node1 ansible_host=web01.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass  
web_node2 ansible_host=web02.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass  
web_node3 ansible_host=web03.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass  
   
[db_nodes]  
sql_db1  
sql_db2  
   
[web_nodes]  
web_node1  
web_node2  
web_node3  
   
[boston_nodes]  
sql_db1  
web_node1  
   
[dallas_nodes]  
sql_db2  
web_node2  
web_node3  
   
[us_nodes:children]  
dallas_nodes  
boston_nodes
```

### Prefixes
```
-m yum – run module "yum"  
-b – become root   
-I inventory.txt - locate customer inventory   
-a "df –h" - run df –h command  
-m copy – copy module
```

### Examples of Ad-hoc
```
# These commands do a variety of tasks: 
ansible test -b -a "/usr/bin/hostnamectl"  - run hostnamectl command on test group servers -b(Become root)  -a(run command)   
ansible all -m ping - check connectivity with all machines with ping   
ansible LAB -i inventory.txt -s -m shell  -a "dmidecode | \ grep UUID" - check UUID
ansible localhost -b -m file -a 'state=touch name=/opt/test.sh mode=0755 owner=ansible'
ansible -b localhost -m lineinfile -a 'line="#!/bin/sh\necho hi there" path=/opt/test.sh'
```
### Check connectivity to the servers 
```
ansible all -m ping  
```
### Uses the setup module to pull information about the server, then filter it to only the ansible_default_ipv4 section  
```
ansible local -m setup -a 'filter=ansible_default_ipv4'  
```

### Installs the latest Apache webserver on all servers in the centos group  
```
 ansible centos -b -m yum -a 'name=httpd state=latest' 
```
 
### Installs elinks onto hosts in the webhosts group that are in the myhosts inventory file 
```
 ansible webhosts -i myhosts -b -m yum -a "name=elinks state=latest"  
```
### user creation
```
ansible dbsystems -b -m user -a "name=consultant"
```
### Place Key Files in the Correct Location, /home/$USER/.ssh/authorized_keys, on Hosts in dbsystems

```
[ansible@control1]$ ansible dbsystems -b -m file -a "path=/home/consultant/.ssh state=directory owner=consultant group=consultant mode=0755" 
[ansible@control1]$ ansible dbsystems -b -m copy -a "src=/home/ansible/keys/consultant/authorized_keys dest=/home/consultant/.ssh/authorized_keys mode=0600 owner=consultant group=consultant" 
[ansible@control1]$ ansible dbsystems -b -m file -a "path=/home/supervisor/.ssh state=directory owner=supervisor group=supervisor mode=0755" 
[ansible@control1]$ ansible dbsystems -b -m copy -a "src=/home/ansible/keys/supervisor/authorized_keys dest=/home/supervisor/.ssh/authorized_keys mode=0600 owner=supervisor group=supervisor"
```
### Ensure auditd Is Enabled and Running on All Hosts
```
[ansible@control1]$ ansible all -b -m service -a "name=auditd state=started enabled=yes"
```
### Create the user john on labservers
```
ansible labservers -b -m user -a "name=john"
```
### Copy the file demo to /home/ansible/demo on node1
```
ansible node1 -m copy -a "src=/home/ansible/files/demo dest=/home/ansible/demo"
```
### Install elinks on labservers
```
ansible labservers -b -m yum -a "name=elinks state=latest"
```



