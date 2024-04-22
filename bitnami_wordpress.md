# How to use Bitnami Wordpress Marketplace AMIs
This tutorial is to use Bitnami Wordpress Application available at Amazon Marketplace.

This is a blogging application and has web and database server running on same machine. As part of this exercise we will do the following.

Part-1: Run the application as-is and post comments and view the data flow via database client.

Part-2: Migrate the database from traditional MySQL to Amazon RDS and change the configurations to redirect the web application to use newly created RDS.

Lets get in action…

## Part-1: Run application as-is

Step 1: Go to EC2 dashboard and Create a new keypair – training

Step 2: Create ec2 instance using Marketplace AMI for bitnami wordpress “WordPress Certified by Bitnami and Automattic”
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/08aa9961-03cc-4095-b6ba-433c47ca41f6)

Check for usage instructions:
Few important things to note here:

a) The default server administrator is ‘user’

b) Documentation Link to get password: https://docs.bitnami.com/aws/faq/get-started/find-credentials/

c) You can also access your instance via SSH using the username ‘bitnami’ and your Amazon private key

Hit Continue and complete the instance creation process using key pair created in step-1
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/d460f908-a1ec-40e7-b8d7-eed423c5045b)

Step 3: ssh to your instance, grab the public DNS and follow instructions below:

Open putty and save the public DNS:

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/4260150f-da0f-4670-9ae4-fcb3c4707bec)

Make following configurations in putty:

a) Select keypair under SSH=>Auth
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/89f050c8-e628-4d97-935b-317700fb3796)

b) open a tunnel to access db client phpmyadmin available on the same server: Go to SSH=>Tunnels
source port: 8888

Destination: localhost:80
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/f6416656-2420-4e50-b337-a438aee2bec7)

Click on Add
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/e8df57b2-b3b5-4341-b377-16f24de98aa4)

c) Go to Connection => data
Data: Auto-login username = bitnami
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/2041528c-2747-4738-8bb8-05f20bf8fbe8)

Make sure all the three configurations you just made are still there and then click on open. It will launch the server as below:
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/cc54da1b-92d0-498a-915b-98494f2d8d9c)

Step 4: Now, once you ssh-ed to this instance, next step to get the password for running application. to get that run following command
```
cat ./bitnami_credentials
```
Note the username ‘user’ and its password
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/c847e270-771c-451b-b10f-33003b82bbf9)

Step 5: Now check the front end application by using public dns via browser:

you should be able to see something like:
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/ca62efe7-39ca-4dc4-9aed-acda81c01f66)

Now, post or reply to some blog. e.g.

Go to Recent Posts, Click on Hello World and reply like:
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/7a2a80ee-5e20-4c6d-aab6-1b226c7c2147)
Click on post comment button

Step 6: Now log in to the DB Client and identify the comment posted in step-5

(phpadmin is already installed at this server, for which have the settings while connecting via putty). Access following via web browser:
http://localhost:8888/phpmyadmin

Since, we are trying to access database, we will use username as ‘root’ and password as per step 4 via “cat ./bitnami_credentials”

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/86b16201-223e-40c9-a2d6-08c5e496a298)
You are now logged on to the database whwre database name is “bitnami_wordpress”

Expand and check wp_comments table.
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/f0311a77-2379-443b-9b07-1293b98cb702)

We can see our comment at row#2.

So, till here we are able to access the web application and database and can also see the data flow.

## Part 2: Migrate the DB to RDS

Now, in 2nd part we will migrate this Mysql database to Amazon RDS. For that export this ‘bitnami_wordpress’ database and later we will import this into RDS so web application has exactly same backend.

Step 1: Export the database ‘bitnami_wordpress’

Click on Database, then click on Export and export it with SQL format.

it will download binami_worpress.sql file to your downloads.

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/d794c4c0-cd13-43d5-9202-59b9ecfd7d6a)

Step 2: Create RDS instance (via AWS Management console)

Go to Databases => Create Database

In Engine options => choose MySQL

Leave the default version. And, choose Free tier Template

in Settings => DB Instance identifier define as: wordpress2

Credentials: Master username: admin , Master Password: Welcome1 (confirm as well)

DB instance size, storage, Availability: leave default

Connectivity: Default VPC

Expand, Additional connectivity configuration: choose Public access as ‘Yes’

Choose ‘default’ security group and DB port will be 3306

Go to Additional Configurations

provide initial database name as: wordpress2

Rest leave default and click on ‘Create Database’

it normally takes 10–15 min.

Once the status is available, check the details

Connectivity & security: Note the Database endpoint

In new tab, go to the EC2 dashboard and note Public and Private IPs of the EC2 instance.

Come back on RDS database details and Click on the security group to open in new tab.

Go to inbound rules => Edit inbound rules

Add following three rules:

Type:MySQL/Aurora, Port range: 3306, Custom, 0.0.0.0/0
Type:MySQL/Aurora, Port range: 3306, Custom, <private ip/32>
Type:MySQL/Aurora, Port range: 3306, Custom, <public ip/32>
![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/fe7c616a-63a1-4f27-9885-46a4ca7035e2)

Save rules.

Step 3: Now, Change the phpMyAdmin configuration to point to the RDS database in step-2. Go to EC2 instance and perform following steps

```
sudo su
cd /opt/bitnami/phpmyadmin/
chmod 777 config.inc.php
```
Open this config.inc.php with your preferred editor e.g. vi or nano

```
vi config.inc.php

Go to the end of file (in vi, you can use shift+G), press ‘o’ to add a new line in the end.

copy-paste following in the end of file. Make sure to replace database endpoint at line 3. To paste you can use Shift+Insert or Shift+rightclick

```
$i++;
$cfg['Servers'][$i]['verbose'] = 'Amazon RDS';
$cfg['Servers'][$i]['host'] = 'wordpress2.cdwwbxbm9nrk.us-east-1.rds.amazonaws.com';
$cfg['Servers'][$i]['port'] = '3306';
$cfg['Servers'][$i]['socket'] = '';
$cfg['Servers'][$i]['connect_type'] = 'tcp';
$cfg['Servers'][$i]['extension'] = 'mysqli';
$cfg['Servers'][$i]['auth_type'] = 'cookie';
$cfg['Servers'][$i]['AllowNoPassword'] = false;
```
Now to save file. Press Esc, then :wq (write & quit)

Change the file permissions back
```
chmod 644 config.inc.php

Now, go to phpMyAdmin client via browser: http://localhost:8888/phpmyadmin/index.php

At login screen, provide the RDS credentials ie

user: admin, Password: Welcome1, Server Choice: Amazon RDS

At this stage, you are connected to DB client which is pointing to Amazon RDS.

Step 4: Now let’s import the database which we exported in Step1

in phpMyAdmin, click on your database name ‘wordpress2’, and then click on “import”, choose file bitnami_wordpress.sql from your downloads

![image](https://github.com/dipalikulshrestha/awslabs/assets/38211382/f543a451-6249-4f72-930e-f53187f7be97)

And, click on Go. It will import all tables & data as exported from original database.

Step 5: So far we have fixed the db client myphpadmin to point to new DB, Now, we also need to point our web application to talk to new RDS instance.

To do that, perform following steps:

From EC2 command prompt
```
cd /opt/bitnami/wordpress/
chmod 777 wp-config.php
vi wp-config.php
```
in first section of this file update the DB details [DBName, User, Password, Host]:

```
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress2' );
/** MySQL database username */
define( 'DB_USER', 'admin' );
/** MySQL database password */
define( 'DB_PASSWORD', 'Welcome1' );
/** MySQL hostname */
define( 'DB_HOST', 'wordpress2.cdwwbxbm9nrk.us-east-1.rds.amazonaws.com' );
/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );
/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );
```
Save the file and change the file permissions back.

```
chmod 644 wp-config.php
```
Try to access application via Public DNS.

Post new comment and check in DB via phpMyAdmin.

Ref command from CLI

```
mysqldump -u root -p JxAAaQTHAKc2 - databases bitnami_wordpress - default-character-set=utf8mb4 - complete-insert - opt - result-file=target.sql
```

Now we are done with this excercise. Make sure to delete RDS instance and EC2 instance to avoid un-necessary charges.

###PS: Comment if it improvements and Applaud if useful.









