Webserver can be seen in action even without log-in to your EC2 instance by providing bootstrap script in to user data section of EC2.
```
#!/bin/sh
sudo yum update -y
sudo yum install httpd -y
sudo service httpd start 
chkconfig httpd on 
cd /var/www/html
cat >> index.html << EOF
<html><body> this is first ec2 instance in az-a </body></html>
EOF
```

incase, you missed to add user data execute following commands at your linux prompt [one-by-one]
```
sudo su
yum update -y
yum install httpd -y
service httpd start 
chkconfig httpd on 
cd /var/www/html
-- Create file with vi
vi index.html 
<html><body> this is first ec2 instance in az-a </body></html>
```
