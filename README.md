# Building A Fault Tolerant Wordpress Site
[Source](https://www.udemy.com/course/aws-certified-solutions-architect-associate/learn/lecture/13887862#content) 

## System Network Diagram

![System Digram](https://drive.google.com/uc?id=1Na2V52jyVPOcDGTzs2zQ7_azECPmly3D)

Users browsing the internet with domain names (for example: www.aws.amazon.com) is going to hit Route 53, Amazon Domain Name Service(DNS), that is going to connec to the Elastic Load Balancer (ELB). ELB is going to pass traffic to the EC2 instances behind the Auto Scaling Group(ASG) located in separate Availability Zones(AZs) within a public subnet. The Database instances are usually located in private subnets. The S3 buckets are internet facing services. In this site, we will be using two S3 buckets, one for the code and another one for the media assets. The media assets from S3 can be served from CloudFront service to accelerate the accesses to all the customers around the globe. This configuration can handle failures in any one of the AZs and the website still functions without any interruption to the service.

![VPC with public and private subnets and route tables](https://drive.google.com/uc?id=1M5sdCpHLuyBhpj5Vi6Obz4zQWgr0kPcL)

## AWS resources setup for the WordPress site

Follow these Steps from AWS console:
1. Go to S3 service and create two buckets with unique names ending with code and media.
2. Got to CloudFront and create Distribution Web based (we are not choosing the RTMP) with Restrict Bucket Access set to No.

![](https://drive.google.com/uc?id=1iinWDITOuNdbsYlUihh1rusKd9KR5I2k)

3. Go to VPC > Security Groups >  Create security group. Create a public subnet security group with inbound rules to allow SSH and HTTP.
![](https://drive.google.com/uc?id=1Z6JkoNkvuVJcldtXm9XOcBORzA-4lbin)

4. Similarly create a private subnet security group with inbound rule that allows traffic to MYSQL/Aurora port 3306 from Source public security group name.

5. Provision the RDS Database instance by going to RDS > Create database > MySQL > Select Dev/Test template with Burstable classes db.t2.micro with Multi-AZ. Select the database security group in the Additional  connectivity configuration. Provide the DB name inside Additional configuration and disable Enable Enhanced Monitoring. With all other set to default click on create databse.

6. Create a S3forWordPress role to talk to S3 from IAM > Roles > Create Role > AWS Service > EC2 > Attach AmazonS3FullAccess.

7. Provision EC2 instances. EC2 > Launch Instance > with IAM role S3forWordPress with Boot strap script. Provide a tag with name MyGoldenImage with WebDMZ security group. 
```
#!/bin/bash
yum update -y
yum install httpd php php-mysql -y
cd /var/www/html
echo "healthy" > healthy.html
wget https://wordpress.org/wordpress-5.1.1.tar.gz
tar -xzf wordpress-5.1.1.tar.gz
cp -r wordpress/* /var/www/html/
rm -rf wordpress
rm -rf wordpress-5.1.1.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
wget https://s3.amazonaws.com/bucketforwordpresslab-donotdelete/htaccess.txt
mv htaccess.txt .htaccess
chkconfig httpd on
service httpd start
```








