# Building A Fault Tolerant Wordpress Site
[Source](https://www.udemy.com/course/aws-certified-solutions-architect-associate/learn/lecture/13887862#content) 

## System Network Diagram

![System Digram](https://drive.google.com/uc?id=1Na2V52jyVPOcDGTzs2zQ7_azECPmly3D)

Users browsing the internet with domain names (for example: www.aws.amazon.com) is going to hit Route 53, Amazon Domain Name Service(DNS), that is going to connec to the Elastic Load Balancer (ELB). ELB is going to pass traffic to the EC2 instances behind the Auto Scaling Group(ASG) located in separate Availability Zones(AZs) within a public subnet. The Database instances are usually located in private subnets. The S3 buckets are internet facing services. In this site, we will be using two S3 buckets, one for the code and another one for the media assets. The media assets from S3 can be served from CloudFront service to accelerate the accesses to all the customers around the globe. This configuration can handle failures in any one of the AZs and the website still functions without any interruption to the service.

![VPC with public and private subnets and route tables](https://drive.google.com/uc?id=1M5sdCpHLuyBhpj5Vi6Obz4zQWgr0kPcL)





