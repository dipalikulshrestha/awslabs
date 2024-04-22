This tutorial is to use Bitnami Wordpress Application available at Amazon Marketplace.

This is a blogging application and has web and database server running on same machine. As part of this exercise we will do the following.

Part-1: Run the application as-is and post comments and view the data flow via database client.

Part-2: Migrate the database from traditional MySQL to Amazon RDS and change the configurations to redirect the web application to use newly created RDS.

Lets get in action…

Part-1: Run application as-is

Step 1: Go to EC2 dashboard and Create a new keypair – training

Step 2: Create ec2 instance using Marketplace AMI for bitnami wordpress “WordPress Certified by Bitnami and Automattic”

