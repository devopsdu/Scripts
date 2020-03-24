


# Become Root user
sudo su –

# Install Python
yum install python

# Check the Python version
python --version

# Install PIP
yum install python-pip

# Install Ansible
pip install ansible

# Check Ansible version
ansible --version

# Create directory
mkdir /etc/ansible

# Create User
useradd ansadmin

# Set password
passwd ansadmin

# Sudo access to ansadmin user
echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Install Docker
yum install docker

# Start Docker 
service docker start

# Check Docker status
service docker status

# Add Ansible user as part of docker group
usermod -aG docker ansadmin

# Edit sshd config file.  
vi /etc/ssh/sshd_config [search for /password. Change the PasswordAuthentication yes]
# EC2 uses keys for remote access
PasswordAuthentication yes

# Reload sshd service
service sshd reload

# Become ansadmin user
su - ansadmin

# Generate ssh keys
ssh-keygen [No need to pass anything, keep hitting “Enter”]

# Go to .ssh folder
ls -la
cd .ssh
cat id_rsa.pub
exit

# Login to Dev Instance
sudo su -

# Add ansible user and set password
adduser ansadmin

# Grant Ansible User Sudo Privileges
usermod -aG sudo ansadmin
id ansadmin
sudo usermod -aG docker ansadmin
id ansadmin


# Login to ansible instance
su – ansadmin

# Go to ansible directory
cd /etc/ansible

# Create host file and add Dev instance IP
sudo vi hosts
52.170.29.77
localhost

# Copy ssh key to dev instance
ssh-copy-id ansadmin@52.170.29.77
ssh-copy-id ansadmin@localhost

# You can now ssh from ansible to dev instance
ssh ansadmin@52.170.29.77

# Ping the target instance
ansible -m ping all

&&&&&&&&&&&&&&&&&&&&&&&&&&&&

# Login to ansible instance
# su - ansadmin

# Change directory and follow the steps
cd /opt
sudo mkdir docker
sudo chown -R ansadmin:ansadmin /opt/docker
ls -l /opt
cd docker
ls –l



# Login to ansible instance
su - ansadmin

# Change directory and follow the steps
cd /opt/docker

# Create a dockerfile
vi Dockerfile (Refer script folder)

# Pull tomcat latest image from dockerhub 
From tomcat:latest

# copy war file on to container 
COPY ./webapp.war /usr/local/tomcat/webapps

# Login to Docker hub so when you run playbook the images can be pushed
docker login -u dockerid


# Write a playbook to create a docker image and push to docker hub
vi docker-create-push-webapp.yml (Refer script folder)

Create hosts file
vi hosts
localhost
52.170.29.77

# Write a playbook to pull docker image and run a container
vi docker-pull-run-webapp.yml (Refer script folder)

*******************

Jenkins "Exec Command"

ansible-playbook -i /opt/docker/hosts /opt/docker/docker-create-push-webapp.yml --limit localhost

ansible-playbook -i /opt/docker/hosts /opt/docker/docker-pull-run-webapp.yml --limit 104.211.21.71


