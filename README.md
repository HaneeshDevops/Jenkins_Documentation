# Jenkins_Documentation 
### METHOD 1
### Downloading and installing Jenkins on Amzon Linux Machine
(sudo date -s "2023-06-12 03:28:00")
## Switch to root user
```sh
sudo su -
 ```

## Ensure that your software packages are up to date on your instance by using the following command to perform a quick software update:
```sh
yum update –y
 ```
## Add the Jenkins repo using the following command:
```sh
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
 ```
## Import a key file from Jenkins-CI to enable installation from the package: 
```sh
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
 
 ```
## Upgrade 
 ```sh
yum upgrade -y
 ```
## Install Java (Amazon Linux 2):
```sh
amazon-linux-extras install java-openjdk11 -y
 ```

## Install maven ,Docker & docker-compose
```sh
yum install maven -y
wget http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.2.5/binaries/apache-maven-3.2.5-bin.tar.gz
tar xzf apache-maven-3.2.5-bin.tar.gz
sudo mv apache-maven-3.2.5 /opt/
export PATH=/opt/apache-maven-3.2.5/bin:$PATH
source ~/.bashrc
yum install -y docker
systemctl start docker 
systemctl enable docker
curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
yum install git -y
```

## Install Jenkins:
```sh
yum install jenkins -y
```

## Enable the Jenkins service to start at boot:
```sh
systemctl enable jenkins
```
## Add the Jenkins user to the docker group:
```sh
sudo usermod -aG docker jenkins
```
## Sometimes we may need to execute these commands to give docker permisiions
```
sudo chown jenkins:docker /var/run/docker.sock
sudo chmod 660 /var/run/docker.sock
```
## Start Jenkins as a service:
```sh
systemctl start jenkins
```
## You can check the status of the Jenkins service using the command:
```sh
systemctl status jenkins
```
## To check Admin password
```sh
cat /var/lib/jenkins/secrets/initialAdminPassword
```
# <><>><> THAT'S IT YOU CAN START USING JENKINS RIGHT AWAY <><><><>

# (For Remote Machine) Configure Docker to listen on a specific IP address and port:

## open the Docker configuration file 
```sh
sudo vi /etc/sysconfig/docker
```
## Find the line that starts with OPTIONS= and add the following flags to the end of the line:
```sh
OPTIONS="-H tcp://172.31.93.144:2375 -H unix:///var/run/docker.sock"
```
## restarts the Docker service
```sh
sudo service docker restart
```
## open the .bashrc file
```sh
vi ~/.bashrc
```
## Add the following line at the end of the file:
```sh
export DOCKER_HOST='tcp://172.31.93.144:2375'
```

## apply the changes made to the .bashrc file
```sh
source ~/.bashrc
```

## All Commands at once:
```sh
sudo su -
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

# LET'S START JENKINS: 
```sh
Manage jenkins : Global Tool configuration:
add maven & docker
Add dokcer & docker-compose plugins
New Job:
Name: jenkins_devops_microservice_pipeline
select pipeline option from down


select Poll SCM:* * * * *
go to Pipeline : Pipeline script from SCM
SCM:git & Repository URL : https://github.com/Haneesh55/SpringBootEcommerceApplication.git
BUILD NOW : click on link & openconsole #
```

# <><><<><><<>><><><><><><>><><><><><><><><>>

# METHOD 2
# Downloading and running Jenkins in Docker(as a container):

## Switch to root user & Install Docker:
```sh
sudo su -
yum install -y docker
systemctl start docker 
systemctl enable docker
```
## Create a bridge network in Docker using the following docker network create command:
```sh
docker network create jenkins
```
## In order to execute Docker commands inside Jenkins nodes, download and run the docker:dind Docker image using the following docker run command:
```sh
docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind --storage-driver overlay2
```
```
docker run --name docker_dind_container --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  haneeshdevops/docker:dind --storage-driver overlay2

```
## Customise official Jenkins Docker image, by executing below two steps:

## a) Create "Dockerfile" with the following content:
```sh
FROM jenkins/jenkins:2.401.1
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
```
## b) Build a new docker image from this Dockerfile and assign the image a meaningful name, e.g. "myjenkins-blueocean:2.401.1-1":
```sh
docker build -t myjenkins-blueocean:2.401.1-1 .
```
## Run your own myjenkins-blueocean:2.401.1-1 image as a container in Docker using the following docker run command:
```sh
docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.401.1-1
```
```
docker run --name jenkins_dind_container --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  haneeshdevops/jenkins:dind

```
## All Docker images
```sh
docker images
```
## All running Docker Containers  
```sh
docker ps
```
# Wait for 30seconds to 1Minute
## Execute the following command to get the initial administrator password:
```sh
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword

```

# Step 3 
```
sudo su -
yum install -y docker
systemctl start docker 
systemctl enable docker
docker network create jenkins

```
```
docker run --name docker_dind_container --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  haneeshdevops/docker:dind --storage-driver overlay2

```
```
docker run --name jenkins_dind_container --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  haneeshdevops/jenkins:dind

```
```
docker exec jenkins_dind_container  cat /var/jenkins_home/secrets/initialAdminPassword

```


