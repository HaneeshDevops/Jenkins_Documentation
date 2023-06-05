# Jenkins_Documentation 

###Downloading and installing Jenkins

## Ensure that your software packages are up to date on your instance by using the following command to perform a quick software update:
```sh
sudo yum update –y
 ```
## Add the Jenkins repo using the following command:
```sh
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
 ```
## Import a key file from Jenkins-CI to enable installation from the package: 
```sh
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo yum upgrade
 ```
## Install Java (Amazon Linux 2):
```sh
sudo amazon-linux-extras install java-openjdk11 -y
 ```
## Install Jenkins:
```sh
sudo yum install jenkins -y
```
## Enable the Jenkins service to start at boot:
```sh
sudo systemctl enable jenkins
```
## Start Jenkins as a service:
```sh
sudo systemctl start jenkins
```
## You can check the status of the Jenkins service using the command:
```sh
sudo systemctl status jenkins
```
## To check Admin password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

## All Commands at once:
```sh
yum update -y
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum upgrade -y
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
systemctl status jenkins
```
