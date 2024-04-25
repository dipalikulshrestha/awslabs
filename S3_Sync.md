# EC2 to S3 access

## Method1: via Access Key and Secret Key

check if aws cli is available at ec2: aws s3 ls
if needed : pip install awscli

aws configure

AWSAccessKeyId=<>
AWSSecretKey=<>
region: us-east-1

## Method2: via IAM role

create a new role: EC2toS3

attach: AmazonS3fullaccess policy

attach this role to EC2 (security)

## perform CLI operations:

```
--Create new bucket
aws s3 mb s3://<bucket-name>
--list objects of a bucket
aws s3 ls s3://bucket-name
--remove a bucket
aws s3 rb s3://bucket-name
--sync content to s3 from local/ec2
aws s3 sync . s3://<bucket-name>
--sync from s3 to ec2
aws s3 sync s3://<bucket-name> .

```