# order of creation
- create security group first
- create certificate
- create ALB
- create Launch template
- create Austocaling

# Bastion ami installation
 yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm 
 yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm 
- yum install wget vim python3 telnet htop git mysql net-tools chrony -y 
- systemctl start chronyd 
- systemctl enable chronyd

# Nginx ami installation
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

yum install wget vim python3 telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd

# configure selinux policies for the webservers and nginx servers
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1

# this section will instll amazon efs utils for mounting the target on the Elastic file system
git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm

# git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm

# seting up self-signed certificate for the nginx instance
sudo mkdir /etc/ssl/private

sudo chmod 700 /etc/ssl/private

openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ACS.key -out /etc/ssl/certs/ACS.crt

sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048

# webserver ami installation
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

yum install wget vim python3 telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd

# configure selinux policies for the webservers and nginx servers
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1

# setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1

# this section will instll amazon efs utils for mounting the target on the Elastic file system
git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm

# seting up self-signed certificate for the apache webserver instance
yum install -y mod_ssl

openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/ACS.key -x509 -days 365 -out /etc/pki/tls/certs/ACS.crt

vi /etc/httpd/conf.d/ssl.conf

# Login into the RDS instnace and create database for wordpress and tooling wordpress and tooling database
mysql -h acs-database.cdqpbjkethv0.us-east-1.rds.amazonaws.com -u ACSadmin -p

CREATE DATABASE toolingdb; CREATE DATABASE wordpressdb;


# References Read
IP ranges

ssh-gent forwarding on mobaxterm

Nginx reverse proxy server

Underdstanding ec2 user data

Manually installing the Amazon EFS client

creating target groups for AWS Loadbalancer

Self-Signed SSL Certificate for Apache

Create a Self-Signed SSL Certificate for Nginx