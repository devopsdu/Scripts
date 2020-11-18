


# 1/ Ansible Installation
### Become Root user
`sudo su -`

### Update Yum

### Install Python
`yum install python38`

### Check the Python version
`python3 --version`

### Install PIP
`yum install python3-pip`

### Install Ansible
`pip3 install ansible`

### Check Ansible version
`ansible --version`

### Create directory
`mkdir /etc/ansible`

### Create User
`useradd ansadmin`

### Set password
`passwd ansadmin`

### Sudo access to ansadmin user
`echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers`

# 2/ Docker Installation

### Install Yum-Config-Manager
`yum install -y yum-utils`

### Add the Docker repo
`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

### Install Docker
`yum install docker-ce docker-ce-cli containerd.io`

### Start Docker 
`service docker start`

### Check Docker status
`service docker status`

### Add Ansible user as part of docker group
`usermod -aG docker ansadmin`

# 3/ Set Password Authentication

### Edit sshd config file.  
`vi /etc/ssh/sshd_config` [search for /password. Change the PasswordAuthentication yes]

EC2 uses keys for remote access

PasswordAuthentication yes

### Reload sshd service
`service sshd reload`

# 4/ Generate SSH Keys

### Become ansadmin user
`su - ansadmin`

### Generate ssh keys
`ssh-keygen` [No need to pass anything, keep hitting “Enter”]

### Go to .ssh folder
`ls -la`

`cd .ssh`

`cat id_rsa.pub`

`exit`

# 5/ Create user and set privileges on target host
### Login to Dev Instance
`sudo su -`

### Add ansible user and set password
`adduser ansadmin`

### Grant Ansible User Sudo Privileges
`echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers`

### Add Ansible user as part of docker group
`sudo usermod -aG docker ansadmin`

### To check the group details
`id ansadmin`


# 6/ Copy the ssh keys

### Login to ansible instance
`su - ansadmin`

### Go to ansible directory
`cd /etc/ansible`

### Create host file and add Dev instance IP
`sudo vi hosts` (Add the target host IP & localhost)

### Copy ssh key to target instance
`ssh-copy-id ansadmin@<targethostipaddress>`

### Copy ssh key to localhost
`ssh-copy-id ansadmin@localhost`

### Ping the target instance
`ansible -m ping all`

---
# 1/ Create target directory
### Login to ansible instance
`su - ansadmin`

### Change directory and follow the steps
`cd /opt`

`sudo mkdir docker`

`sudo chown -R ansadmin:ansadmin /opt/docker`

`ls -l /opt`

`cd docker`

`ls –l`

# 2/ Create Playbooks and Dockerfile under /opt/docker folder

### Login to Docker hub once so when you run playbook the images can be pushed
`docker login -u <dockerid>`

### Create a dockerfile
`wget https://raw.githubusercontent.com/duorg/Scripts/master/Dockerfile` (Refer script folder)

### Write a playbook to create a docker image and push to docker hub
`wget https://raw.githubusercontent.com/duorg/Scripts/master/docker-create-push-webapp.yml` (Refer script folder)

### Write a playbook to pull docker image and run a container
`wget https://raw.githubusercontent.com/duorg/Scripts/master/docker-pull-run-webapp.yml` (Refer script folder)

### Create hosts file
`cp /etc/ansible/hosts .`

*******************

#  3/ Jenkins "Exec Command"

`ansible-playbook -i /opt/docker/hosts /opt/docker/docker-create-push-webapp.yml --limit localhost`

`ansible-playbook -i /opt/docker/hosts /opt/docker/docker-pull-run-webapp.yml --limit <targethostipaddress>`


