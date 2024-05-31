# Use this script for troubleshooting
# if any of the webservers target group(tooling or wordpress) is unhealthy
# connect to your bastion server via ssh -A ec2-user@<public ip address>
# copy this script and save it as db.sh on the bastion server 
# run it with sudo ./db.sh
# if there are error with database access error
# ensure you get your RDS end poin correctly. check wordpress-userdata instruction for guildline on how to get the RDS  endpoint
#  connect to the database from the bastion via the RDS endpoint something like this ("acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com")...  like line 29
# check if the database were created successfully, if not
# create the database and the user using the SQL statement from line 30, leaving all the part satrting from -e backward
# spin up a new ec2 instance( just for testing) for any of the webservers target group that is not working (wordpress or tooling)
# download git on it with sudo yum git install
# copy the corresponding user-data and save it as webserver.sh on it
# run the script with sudo ./webserver.sh
# if successfully, take the public ip of the instance and put it on a browser. the webserver should be working on it


#!/bin/bash
DATABASE_PASS='admin12345'
#sudo yum update -y
#sudo yum install mysql -y
#sudo yum install git zip unzip -y
# starting & enabling mysql-server
#sudo systemctl start mysqld
#sudo systemctl enable mysqld
#cd /tmp/
#git clone -b main https://github.com/hkhcoder/vprofile-project.git
#restore the dump file for the application
#sudo mysqlACSadmin -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin password "$DATABASE_PASS"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1')"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User=''"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%'"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "create database tooling"
#sudo mysql -h tooling-db.cn0qacy4c7da.eu-north-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "create user'ehi'@'%' identified by 'admin123'"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "CREATE USER 'webaccess'@'%' IDENTIFIED BY 'admin123'"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "grant all privileges on tooling.* TO 'webaccess'@'%'"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "create database wordpressdb"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "CREATE USER 'smile'@'%' IDENTIFIED BY 'admin123'"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "grant all privileges on wordpressdb.* TO 'smile'@'%'"
sudo mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com -u ACSadmin -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
git clone https://github.com/nbomasi/tooling-1.git
git clone https://github.com/dareyio/tooling.git
mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com  -u webaccess -p"$DATABASE_PASS" toolingdb < tooling-1/tooling-db.sql
#mysql -h acs-database.cxgqmm0me4bb.us-east-1.rds.amazonaws.com  -u boma -p"$DATABASE_PASS" toolingdb < tooling/html/tooling_db_schema.sql