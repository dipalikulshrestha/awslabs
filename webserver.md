![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/16a31fcb-af8f-4a17-bb55-0b736868fafd)


![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/ecf60528-5885-434b-8f01-a0a4144644da)

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/6afed5a1-740d-47eb-a510-2767f3646467)

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/72bccc1f-3f48-4002-921e-13f35eb52de8)

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/34ba7033-88d8-4afb-88dc-68b7880fa922)



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
