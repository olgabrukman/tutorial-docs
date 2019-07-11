# Overview

* **Elastic Cloud Compute (EC2)** is a service that provides instances. Instance is the same as a computer. Instances can increase/decrease as defined.
* **AMI (Amazon Machine Image)** = OS + software.
* **Simple Storage Service (S3)** Collection of buckets, a bucket stores objects. Capabilities: 
  * Trigger events when an object is added/modified/deleted.
  * Preserve old versions of objects.
  * Replicate objects across regions.
  ![](https://github.com/olgabrukman/tutorial-docs/blob/master/aws7.png)
* **Elastic Block Storage (EBS)** service: Filesystem for EC2 instances, attached to EC2 instances.  
* **Relational DB (RDB)** service: Solution for DB needs. Provides the following capabilities:
  * DB backup      
  * Redundancy 
    * In both cases that means taking DB snapshots and allowing changing HW on the fly
  * Software patches
  * Easy configuration
  * Read replicas
  * Supported DBs are:MySQL, POsreSQL, SQLServer, MariaDB, Oracle, Amazon Aurora
* **Route53 (DNS service)** Makes health checks to make sure an address is reachable.
* **ElasticBeanstack (EB)**: Solution for your application needs. Provides:
1. Configuration
2. Code deployment
3. CLI
4. Scaling
5. (Basic) Monitoring
6. Application versions are stored in S3
![](https://github.com/olgabrukman/tutorial-docs/blob/master/aws6.png)
* **DynamoDB: NoSQL document DB service**
1. Unlimited elastic storage
2. No hardware choices
3. Pay only for usage
4. Read/write by 4KB blocks
5. Pricing: provisioned throughput (# of read/writes per sec)+ amount of stored data
* **RedShift: data warehouse managed service** Cluster of Nodes (VMs), each Node can be different. 
 *RedShift security: 
   1. VPC protection
   2. Data warehouse encryption
   3. No public IP
* RS Node Categories:
    1. Dense Storage Node (more storage, expensive)
    2. Dense Compute Node (less storage, more CPU, cheap)
![](https://github.com/olgabrukman/tutorial-docs/blob/master/aws5.png)    
* **Virtual Private Cloud (VPC) : networking needs service** Security groups  allow securing  a single instance, while VPC  allows to secure groups of instances.
![](https://github.com/olgabrukman/tutorial-docs/blob/master/aws4.png)
![](https://github.com/olgabrukman/tutorial-docs/blob/master/aws3.png)
* **Cloud Watch: monitoring service** Monitoring resources/metrics and acting on alerts. Metric + alarm => action
Monitor and aggregate logs: one needs top install awslogs agent on EC2 instance. The agent will send relevant entries to CloudWatch.
* **CloudFront: solution for CDN (content delivery network)** {original content (bucket in S3) --> distribution} => CF URL
Configuration options for an entry, e.g. allowed HTTP methods, edge locations, SSL certificate.
![](https://github.com/olgabrukman/tutorial-docs/blob/master/aws2.png)
* **AWS SDK (aws-sdk artifact)** SDK operation has a Web Console entry and AWS CLI command Operation examples:
Modify CloudWatch, invalidate CloudFront distribution, read/write to RDS
![aws supported SDK languages](https://github.com/olgabrukman/tutorial-docs/blob/master/aws1.png)
