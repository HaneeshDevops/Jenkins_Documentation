# Jenkins_Documentation
1) sudo yum update –y
2)sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
3)sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
4)sudo yum upgrade
5)sudo amazon-linux-extras install java-openjdk11 -y
6)sudo yum install jenkins -y
7)sudo systemctl enable jenkins
8)sudo systemctl start jenkins
9)sudo systemctl status jenkins
10)sudo cat /var/lib/jenkins/secrets/initialAdminPassword
