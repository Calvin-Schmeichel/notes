# notes

---
##### 2. QUESTION
```
A company hosted a web application in an Auto Scaling group of EC2 instances. The IT manager is concerned about the over-provisioning of the resources that can cause higher operating costs. A Solutions Architect has been instructed to create a cost-effective solution without affecting the performance of the application.

Which dynamic scaling policy should be used to satisfy this requirement?
```

Auto Scaling group
- Collection of EC2s
- Auto scales depending on load of application

4 Types of scaling:
1. ==Use target tracking scaling== (Maintain a certain utilization)
	- You base scaling based on a target value
	- Prevents over spending
	- Example: Min Size: One EC2, Desired: Two EC2, Max: Four EC2, Scale out: One - three EC2s to scale out to.
2. Use simple scaling (Keep utilization under a certain percentage)
	- Uses cloud watch alarms to measure CPU Utilization and once it reaches a certain threshold you increment EC2 counts programmatically.
3. Use scheduled scaling (Scale based on time of day based on historical utilization)
	- mainly used for predictable traffic patterns.
4. Use suspend and resume scaling
	- Temporarily pause workloads
	- Good for when you want to:
		- Troubleshoot configuration
		- Config changes
---

##### 4. QUESTION
```
A company uses an Application Load Balancer (ALB) for its public-facing multi-tier web applications. The security team has recently reported that there has been a surge of SQL injection attacks lately, which causes critical data discrepancy issues. The same issue is also encountered by its other web applications in other AWS accounts that are behind an ALB. An immediate solution is required to prevent the remote injection of unauthorized SQL queries and protect their applications hosted across multiple accounts.

As a Solutions Architect, what solution would you recommend?
```

AWS WAF
Web application firewall that monitors HTTPS requests that are forwarded to:
- CloudFront (CDN) Distribution
	- protect CDN assets
- Amazon API Gateway (REST API)
	- Rate limit requests
- Application Load Balancer (ALB)
	- For all of your origin servers
- AWS AppSync (GraphQL APIs Maker for backend)
	- Rate limit requests

Web ACLs (Access control lists)
- Used to protect a set of AWS Resources
- Rules define the criteria when looking at HTTPS traffic/web requests and then the rules define what action should be taken.
	- ACL rules can:
		- Block
		- count
		- Test with a CAPTCHA
	- These rules can be put in a **Rules Group**
		- This is a collection of rules that is re-useable for other WAFs
		- Rule groups can also be bought, sold and managed through AWS Marketplace sellers as well.
	- One premade rule is called `AWSManagedRulesSQLiRuleSet`:
		- This rule is an SQL Rule Group that is designed to block common request patterns associated with SQL database attacks such as SQL injection.

AWS WAF allows you to centrally define rules to protect all of your web applications

==Use AWS WAF and set up a managed rule to block request patterns associated with the exploitation of SQL databases, like SQL injection attacks. Associate it with the Application Load Balancer. Integrate AWS WAF with AWS Firewall Manager to reuse the rules across all the AWS accounts.==

AWS Network firewall is a managed service used for network protections for all VPCs in your environment.
- VPC level not the application level (Such as load balancers)
- So in this case AWS WAF would be a better choice
---

##### 5. QUESTION
```
A business has recently migrated its applications to AWS. The audit team must be able to assess whether the services the company is using meet common security and regulatory standards. A solutions architect needs to provide the team with a report of all compliance-related documents for their account.

 Which action should a solutions architect consider?
```

AWS Artifact
central resource for all of AWS's compliance related information for its resources.
> AWS's SOC2 Report

So if you need one of these AWS compliance artifacts for an upcoming audit this is where you grab the information you need.

==Use AWS Artifact to view the security reports as well as other AWS compliance-related information.==

AWS security hub is used for security alerts within your environment.

---

##### 6. QUESTION
```
A financial application consists of an Auto Scaling group of Amazon EC2 instances, an Application Load Balancer, and a MySQL RDS instance set up in a Multi-AZ Deployment configuration. To protect customers’ confidential data, it must be ensured that the Amazon RDS database is only accessible using an authentication token specific to the profile credentials of EC2 instances.

Which of the following actions should be taken to meet this requirement?
```

IAM database authentication
- Works with MySQL and PostgreSQL instances
- Instead of using a password you would use an authentication token to connect to the database instance.

(RDS) Authentication token
Unique string of chars Amazon RDS generates to auth into a database instead of a password. (No need to store creds on DB since IAM manages the auth for you externally)
- Each token has a lifetime of 15 minutes

This is great since you
- Don't need to manage a password
- Centrally manage Auth from IAM
- Auth handshake is secure via SSL

==Enable the IAM DB Authentication==

creating an IAM role to access the instance running the DB would give you access to the instance but you still need to auth with the RDS DB. In this case via  IAM DB Authentication

---

##### 7. QUESTION
```
A tech company that you are working for has undertaken a Total Cost Of Ownership (TCO) analysis evaluating the use of Amazon S3 versus acquiring more storage hardware. The result was that all 1200 employees would be granted access to use Amazon S3 for the storage of their personal documents. 

Which of the following will you need to consider so you can set up a solution that incorporates a single sign-on feature from your corporate AD or LDAP directory and also restricts access for each individual user to a designated user folder in an S3 bucket? (Select TWO.)
```

There are two main asks:
1. You need to set up SSO
2. Configure access to the bucket and restrict per user folder

Enterprise identity federation
Allows you to Auth to your orgs network and each resources
- in this case its MS-AD
- This is also called SSO
- This also makes it so your users use their existing domain joined account (No need to create new AWS accounts for auth)

You would then create an  IAM role and an IAM Policy that would associate each AD user with a unique folder within the S3 Bucket.

==– Setup a Federation proxy or an Identity provider, and use AWS Security Token Service to generate temporary tokens== 

==– Configure an IAM role and an IAM Policy to access the bucket.== 

Only one IAM user and role is needed to define the restrictions (1200 for each user would be the same but copied unnecessarily.)

---

##### 8. QUESTION

```
A Forex trading platform, which frequently processes and stores global financial data every minute, is hosted in an on-premises data center and uses an Oracle database. Due to a recent cooling problem in its data center, the company urgently needs to migrate its infrastructure to AWS to improve the performance of its applications. As the Solutions Architect, the responsibility is to ensure that the database is properly migrated and remains available in case of database server failure in the future.

Which combination of actions would meet the requirement? (Select TWO.)
```

You first want to create a new RDS database in AWS using the Oracle engine.

You want to then migrate your database data to AWS
- This can be done with AWS Database Migration Service.

==– Create an Oracle database in Amazon RDS with Multi-AZ deployments.==

==– Migrate the Oracle database to AWS using the AWS Database Migration Service.==

RMAN is not supported within RDS and you have to create your DB before you can launch it.

---
##### 10. QUESTION
```
A newly hired Solutions Architect is assigned to manage a set of CloudFormation templates that are used in the company’s cloud architecture in AWS. The Architect accessed the templates and tried to analyze the configured IAM policy for an S3 bucket.

{ 
 "Version": "2012-10-17", 
 "Statement": [ 
  { 
   "Effect": "Allow", 
   "Action": [ 
    "s3:Get*", 
    "s3:List*" 
  ], 
  "Resource": "*" 
 }, 
 { 
   "Effect": "Allow", 
   "Action": "s3:PutObject", 
   "Resource": "arn:aws:s3:::boracay/*" 
  } 
 ] 
}

What does the above IAM policy allow? (Select THREE.)
```



==– An IAM user with this IAM policy is allowed to read objects from all S3 buckets owned by the account.==

==– An IAM user with this IAM policy is allowed to write objects into the boracay S3 bucket.==

==– An IAM user with this IAM policy is allowed to read objects from the boracay S3 bucket.==

Read the question better, it says pick three...

---
##### 11. QUESTION
```
An online shopping platform is hosted on an Auto Scaling group of Amazon EC2 Spot instances and utilizes Amazon Aurora PostgreSQL as its database. It is required to optimize database workloads in the cluster by directing the production traffic to high-capacity instances and routing the reporting queries from the internal staff to the low-capacity instances.

Which is the most suitable configuration for the application as well as the Aurora database cluster to achieve this requirement?
```

Amazon Aurora
Typically is a cluster of Aurora database instances (Instead of one).
- Each database request connection is handed by only one of the DB instances.
- The hostname and port/address you point to in order to connect to a data. Is called an *endpoint*.
- This endpoint is the handler that manages incoming requests and picks the best DB instance to send the traffic to (Not busy/currently unavailable)
- Aurora automatically performing load-balancing among all the Aurora DB instances (Aurora Replicas).
- You can specify what type of performance instances Aurora uses.
- You can use high performance instances for production and cheaper instances for internal, testing and ad hoc search queries.
- You can then create multiple endpoints to route traffic to production and internal instances accordingly.

==Create a custom endpoint in Aurora based on the specified criteria for the production traffic and another custom endpoint to handle the reporting queries.==

Note: Aurora does not do this be default and needs to be configured to do so.

---

##### 15. QUESTION
```
A company plans to host a web application in an Auto Scaling group of Amazon EC2 instances. The application will be used globally by users to upload and store several types of files. Based on user trends, files that are older than 2 years must be stored in a different storage class. The Solutions Architect of the company needs to create a cost-effective and scalable solution to store the old files yet still provide durability and high availability.

Which of the following approach can be used to fulfill this requirement? (Select TWO.)
```

Amazon S3
Stores data within buckets, this data is called objects which is any file and metadata that describes it.

You can control the data and manage access to it.

S3 has lifecycle configuration rules to allow you to configure the bucket to trasnsiron objects after 2 years to another S3 bucket type (In this case glacier and/or Standard-IA)

S3 Standard-IA stands for "*Amazon S3 Standard-Infrequent Access*"

==– Use Amazon S3 and create a lifecycle policy that will move the objects to Amazon S3 Glacier after 2 years.==

==– Use Amazon S3 and create a lifecycle policy that will move the objects to Amazon S3 Standard-IA after 2 years.==

EFS lifecycle policies is only up to 365 days.

---
##### 16. QUESTION
```
An AI-powered Forex trading application consumes thousands of data sets to train its machine learning model. The application’s workload requires a high-performance, parallel hot storage to process the training datasets concurrently. It also needs cost-effective cold storage to archive those datasets that yield low profit.

Which of the following Amazon storage services should the developer use?
```

Hot storage
Storage that is accessed frequently

Warm storage
Data that is accessed slightly less frequently

Cold storage
Data that is rarely accessed

Amazon FSx For Lustre
High performance file system made for fast processing.
- Open source
- Parallel file system
	- Stores data across multiple network file shares for better speed.

Amazon FSx for Windows File Server
Fully managed MS file system which can share files via SMB, NTFS and AD.

Amazon Elastic File System
Fully managed file system for Linux

In this case Amazon FSx For Lustre should be used for its high performance, parallel file system and hot data support.

==Use Amazon FSx For Lustre and Amazon S3 for hot and cold storage respectively.==

EBS is mostly used for hot storage only and its cold options are more expensive then S3.

---
##### 18. QUESTION
```
A telecommunications company is planning to grant AWS Console access to its developers. Company policy requires the use of identity federation and role-based access control. Currently, the roles are already assigned via groups in the corporate Active Directory. 

In this scenario, what combination of the following services can provide developers access to the AWS console? (Select TWO.)
```

AWS Directory Service
Allows you to connect your MS AD and Amazon IAM together.

In this case since the company already has AD groups and used that for management we need to connect our on prem AD to AWS IAM and map those AD users to IAM roles that specify access to the AWS management console.

==–  AWS Directory Service AD Connector.==

==–  IAM Roles.==

We need to only use IAM roles with AD groups, not IAM groups.

---
##### 20. QUESTION
```
A company is using a combination of API Gateway and AWS Lambda for the web services of an online web portal that is accessed by hundreds of thousands of clients each day. The company will be announcing a new revolutionary product, and it is expected that the web portal will receive a massive number of visitors from all around the globe.

How can the back-end systems and applications be protected from traffic spikes?
```

Amazon API Gateway
Provides throttling features at many levels of the API, such as globally or per service call.

Throttling limits can be set for standard rates and bursts

burst is like a buffer before you enable the throttle even though it is over your rate limit

Rate limit: 1,000 requests per second (RPS)
Burst limit: 2,000 RPS

you can go over 1000 (up to 2000) for a few seconds before you actually. start getting throttled.

You would want to enable this to protect your backend

==Use throttling limits in API Gateway.==

API gateway does no do this by default and even with auto scale you would want to enable some rate limit rules.

---
##### 21. QUESTION
```
A global IT company with offices around the world has multiple AWS accounts. To improve efficiency and drive costs down, the Chief Information Officer (CIO) wants to set up a solution that centrally manages their AWS resources. This will allow them to procure AWS resources centrally and share resources such as AWS Transit Gateways, AWS License Manager configurations, or Amazon Route 53 Resolver rules across their various accounts.

As the Solutions Architect, which combination of options should you implement in this scenario? (Select TWO.)
```

AWS Resource Access Manager (RAM)
A service that allows you to securely share your AWS resources with any AWS account you want or within an AWS org.

This helps reduce the duplication of resources while maintaining the benefits of multiple accounts (Isolated billing, etc).

AWS Organizations
Account management service that allows you to consolidate multiple accounts into one org. This accounts can be put into groups for centralized management.

 ==– Consolidate all of the company’s accounts using AWS Organizations.==

 ==– Use the AWS Resource Access Manager (RAM) service to easily and securely share your resources with your AWS accounts.==

You could use IAM for this directly but it can be "extremely tedious and entails a lot of operational overhead since you have to manually set up cross-account access to each and every AWS account of the company." a better solution is to use RAM and orgs.

---
##### 22. QUESTION
```
A company has recently migrated its microservices-based application to Amazon Elastic Kubernetes Service (Amazon EKS). As part of the migration, the company must ensure that all sensitive configuration data and credentials, such as database passwords and API keys, are stored securely and encrypted within the Amazon EKS cluster’s etcd key-value store.

What is the most suitable solution to meet the company’s requirements?
```

It is a good practice to enable secret encryption with a new KMS Key with an EKS cluster to keep data secure.

By default the secrets EKS has are not encrypted which can lead to security threats.

this can be done with integrating KMS with EKS.

==Enable secret encryption with a new AWS KMS key on an existing Amazon EKS cluster to encrypt sensitive data stored in the EKS cluster’s etcd key-value store.==

KMS is for encryption keys
AWS Secrets Manager is for all other secrets like Passwords and API keys

---
##### 25. QUESTION
```
A travel photo-sharing website is using Amazon S3 to serve high-quality photos to visitors. After a few days, it was discovered that other travel websites are linking to and using these photos. This has resulted in financial losses for the business.

What is the MOST effective method to mitigate this issue?
```

Since the question mentioned linking that is the keyword within S3

S3 objects are private by default, unless you share them temporally with a pre-signed URL. Which can be time based (Sorta like an unlisted YouTube video).

==Configure your S3 bucket to remove public read access and use pre-signed URLs with expiry dates.==

CloudFront is mostly only for CDN

---
##### 27. QUESTION
```
The company that you are working for has a highly available architecture consisting of an elastic load balancer and several EC2 instances configured with auto-scaling in three Availability Zones. You want to monitor your EC2 instances based on a particular metric, which is not readily available in CloudWatch.

Which of the following is a custom metric in CloudWatch which you have to manually set up?
```

Cloudwatch
By default it can monitor on EC2:
- Disk Reads
- Network packets out
- CPU Utilization

> Think CDN (***C***PU***D***isk***N***etwork)

Cloudwatch can monitor these with a manual configuration (Custom Perl script or an installed agent on the EC2):
- Memory utilization
- Disk swap utilization
- Disk space utilization
- Page file utilization
- Log collection

==Memory Utilization of an EC2 instance==

Disk reads is a default metric supported.

---
##### 31. QUESTION
```
A company has a web application that uses Amazon CloudFront to distribute its images, videos, and other static content stored in its Amazon S3 bucket to users around the world. The company has recently introduced a new member-only access feature for some of its high-quality media files. There is a requirement to provide access to multiple private media files only to paying subscribers without having to change the current URLs.

Which of the following is the most suitable solution to implement to satisfy this requirement?
```

CloudFront
Many companies have public data on the internet but want to restrict access to only certain uses (Such as paying customers).

This can be done by using CloudFront by:
- Using pre-signed URL's
	- Cloudfront URLS are not necessary technically (Could use basic S3 URLs) but using them can help prevent users from circumventing access restrictions.
	- Use if you:
		- are using RTMP (Real-Time Messaging Protocol) used for streaming video. (Like youtube streaming).
		- Restrict access to individual files (Like an app installer only)
		- A custom web/browser client that does not support cookies
	- Access is embedded in the (URL Changed)
- Signed cookies
	- Use if you:
		- What to provide restricted access to multiple files
		- You dont want to change the current files URLs
	- Access is embedded in cookies (URL Un-Changed)

==Use Signed Cookies to control who can access the private files in your CloudFront distribution by modifying your application to determine whether a user should have access to your content. For members, send the required Set-Cookie headers to the viewer which will unlock the content only to them.==

Match Viewer protocol is mostly only used for the CloudFront handshake to see if origin is using HTTP or HTTPS

---
##### 32. QUESTION
```
A Docker application, which is running on an Amazon ECS cluster behind a load balancer, is heavily using Amazon DynamoDB. The application requires improved database performance by distributing the workload evenly and utilizing the provisioned throughput efficiently.

Which of the following should be implemented for the DynamoDB table?
```

A partition key portion of a tables primary key determines the logical partitions of how the data is stored.

partition key with unique values is better for performance.

***Cardinality*** refers to the number of unique values in a data attribute.
- High cardinality: Many unique values (User IDs, Transaction IDs, etc)
- Low cardinality: Few distinct values (Active, inactive, etc)

For DynamoDB partition keys with High-Cardinality (Many value types) is what you want

==Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item.==

Did not know what a partition key or Cardinality was.

---
##### 42. QUESTION
```
An online cryptocurrency exchange platform is hosted in AWS which uses ECS Cluster and RDS in Multi-AZ Deployments configuration. The application is heavily using the RDS instance to process complex read and write database operations. To maintain the reliability, availability, and performance of your systems, you have to closely monitor how the different processes or threads on a DB instance use the CPU, including the percentage of the CPU bandwidth and total memory consumed by each process.

Which of the following is the most suitable solution to properly monitor your database?
```

Amazon RDS
Provides metrics in real time for the OS that is running your DB instance.

You can view these metrics inside of console or the JSON console output in the CloudWatch Logs for monitoring

By default the enhance monitoring within Cloud watch is only saved for 30 days. You can change the `RDSOSMetrics` value to extend this time.

CloudWatch vs Enhanced Monitoring Metrics
CloudWatch gets data such as CPU utilization from the EC2 instance

since the hypervisor that runs on the instance takes up a bit of resources you will get slightly off metrics before you get to the DB instance.

Therefor 

==enabling Enhanced Monitoring in RDS==

is the best option if you want to get most accurate metrics on the database itself.

since Cloudwatch alarms would be slightly off do to the hypervisor

---
##### 44. QUESTION
```
A company has a cloud architecture composed of Linux and Windows Amazon EC2 instances that process high volumes of financial data 24 hours a day, 7 days a week. To ensure high availability of the systems, the Solutions Architect must create a solution that enables monitoring of memory and disk utilization metrics for all instances.

Which of the following is the most suitable monitoring solution to implement?
```

CloudWatch can scan EC2 metrics for monitoring CPU utilization, Network utilization, Disk performance, and Disk Reads/Writes.

You can also use Perl or other shell script, as there are no ready to use metrics for:

1. Memory utilization
2. Disk swap utilization
3. Disk space utilization
4. Page file utilization
5. Log collection

More info: [[Timed Mode Set 1 – AWS Certified Solutions Architect Associate Notes - tutorialsdojo#27 QUESTION]]

The Cloudwatch agent can be installed on both Linux and Windows systems.

The agent can collect both system metric and log files of the EC2 instance.

memory and disk utilization metrics can be achieved with a Cloudwatch agent.

==Install the Amazon CloudWatch agent to all the EC2 instances that gather the memory and disk utilization data. View the custom metrics in the CloudWatch console.==

by default, CloudWatch does not automatically provide memory and disk utilization metrics of your instances. You would have to write a Perl script or what is most recommended install the CloudWatch agent.

---
##### 45. QUESTION
```
An application that records weather data every minute is deployed in a fleet of Amazon EC2 Spot instances and uses a MySQL RDS database instance. Currently, there is only one Amazon RDS instance running in one Availability Zone. The database needs to be improved to ensure high availability by enabling synchronous data replication to another RDS instance.

Which of the following performs synchronous data replication in RDS?
```

Amazon RDS
When you configure an RDS instance to deploy in a Multi-AZ deployment Amazon RDS automatically provisions and maintains a synced standby replica in a different Availability Zone.

Any updates to your Amazon RDS DB us synced across all replicas.

==RDS DB instance running as a Multi-AZ deployment==

RDS Read Replicas are asynchronous replication

---
##### 46. QUESTION
```
A company needs to deploy at least two Amazon EC2 instances to support the normal workloads of its application and automatically scale up to six EC2 instances to handle the peak load. The architecture must be highly available and fault-tolerant as it is processing mission-critical workloads.

As a Solutions Architect, what should be done to meet this requirement?
```

The company needs at least 2 EC2's for their normal work load but also requires the environment to be highly available and fault-tolerant (Which means at least 2 AZ's in case one went down).

This also means at least 2 EC2s in each AZ in case the other AZ goes down.

So 4 EC2's is the required minimum to be highly available and fault-tolerant

==Create an Auto Scaling group of EC2 instances and set the minimum capacity to 4 and the maximum capacity to 6. Deploy 2 instances in Availability Zone A and another 2 instances in Availability Zone B.==

2 EC2s is the minimum they need for workloads but not enough to be highly available and fault-tolerant

---
##### 48. QUESTION
```
A government agency plans to store confidential tax documents on AWS. Due to the sensitive information in the files, the Solutions Architect must restrict the data access requests made to the storage solution to a specific Amazon VPC only. The solution should also prevent the files from being deleted or overwritten to meet the regulatory requirement of having a write-once-read-many (WORM) storage model.

Which combination of the following options should the Architect implement? (Select TWO.)
```

Amazon S3

Access points are named network endpoints that attach to your bucket so you can perform file operations on them. Such as: `GetObject` and `PutObject`.

You can specify distinct permissions and network controls that S3 applies for any request that is made through that access point.

S3 Object Lock allows you to store objects with the write once read many (WORM) model.

Object lock prevents files from being deleted or overwritten

Once object lock is enabled you cannot disable it

==– Configure an Amazon S3 Access Point for the S3 bucket to restrict data access to a particular VPC only.==

==– Create a new Amazon S3 bucket with the S3 Object Lock feature enabled. Store the documents in the bucket and set the Legal Hold option for object retention.==

Versioning is required for object lock to be enabled not disabled

---
##### 49. QUESTION
```
A medical records company is planning to store sensitive clinical trial data in an Amazon S3 repository with the object-level versioning feature enabled. The Solutions Architect is tasked with ensuring that no object can be overwritten or deleted by any user in a period of one year only. To meet the strict compliance requirements, the root user of the company’s AWS account must also be restricted from making any changes to an object in the S3 bucket. 

Which of the following is the most secure way of storing the data in S3?
```

Amazon S3

![|500](https://media.tutorialsdojo.com/Amazon-S3-Object-Lock-Retention-Modes.png)

governance mode still allows you the ability to assign some users the ability to update retention policies after the fact

compliance mode objects versions can't be overwritten or deleted by any user.

Legal Hold vs. Retention Period

Legal Hold 
Prevents an object version from being overwritten or deleted. However, a legal hold doesn’t have an associated retention period and remains in effect until removed.
(Indefinite until released) Indefinite hold

Retention Period
Time-bound then released

 ==Enable S3 Object Lock in compliance mode with a retention period of one year.==

Since the question asks that no one can delete the data for one year we would want a legal hold instead of a compliance hold.

---
##### 50. QUESTION
```
A popular social media website uses a Amazon CloudFront web distribution to serve static content to millions of users around the globe. Recently, the website has received a number of complaints about long login times. Additionally, there are instances where users encounter HTTP 504 errors. The manager has instructed the team to significantly reduce login time and further optimize the system.

Which of the following options should be used together to set up a cost-effective solution that improves the application’s performance? (Select TWO.)
```

You can use Lambda@Edge to run your code closer to the end user

You can also implement an origin failover to allow for better availability

==–  Customize the content that the CloudFront web distribution delivers to your users using Lambda@Edge, which allows your AWS Lambda functions to execute the authentication process in AWS locations closer to the users.==

==–  Implement an origin failover by creating an origin group that includes two origins. Assign one as the primary origin and the other as secondary, which enables CloudFront to automatically switch to if the primary origin encounters specific HTTP status code failure responses.==

Deploying your app in multiple regions would help fix the performance issues but would add much cost and overhead when the question as for a "cost-effective solution"

---
##### 54. QUESTION

```
A popular social network is hosted in AWS and is using a Amazon DynamoDB table as its database. There is a requirement to implement a ‘follow’ feature where users can subscribe to certain updates made by a particular user and be notified via email.

Which of the following is the most suitable solution to implement to meet the requirement?
```

Amazon DynamoDB
has a feature called DynamoDB stream which is a order of changes made to the database.

You can use AWS Lambda to create triggers whenever certain changes are made.

Those changes then processed by Lambda can be sent to an SNS topic that subscribers will get via an Email.

==Enable DynamoDB Stream and create an AWS Lambda trigger, as well as the IAM role which contains all of the permissions that the Lambda function will need at runtime. The data from the stream record will be processed by the Lambda function which will then publish a message to Amazon SNS Topic that will notify the subscribers via email.==

DynamoDB Accelerator (DAX) is a feature made for in-memory read performance not capturing edits made to the DB.

---
##### 57. QUESTION
```
A car dealership website hosted in Amazon EC2 stores car listings in an Amazon Aurora database managed by Amazon RDS. Once a vehicle has been sold, its data must be removed from the current listings and forwarded to a distributed processing system.

Which of the following options can satisfy the given requirement?
```

AWS Lambda Function
You can invoke a Lambda from an Amazon Aurora database cluster.

For this question you would want to invoke the Lambda every time a listing is deleted from the database.

The Lambda could then send the listing to a SQS queue for further processing.

==Create a native function or a stored procedure that invokes an AWS Lambda function. Configure the Lambda function to send event notifications to an Amazon SQS queue for the processing system to consume.==

RDS event subscriptions only track infrastructure-related events and not actual database changes.

---
##### 60. QUESTION
```
A healthcare organization wants to build a system that can predict drug prescription abuse. The organization will gather real-time data from multiple sources, which include Personally Identifiable Information (PII). It’s crucial that this sensitive information is anonymized prior to landing in a NoSQL database for further processing.

Which solution would meet the requirements?
```

Amazon Kinesis Data Streams (KDS)
Scalable real time data streaming service

integrates with Lambda

Lambdas can be used to remove PII before data is sent to storage (Like a DB)

==Ingest real-time data using Amazon Kinesis Data Stream. Use an AWS Lambda function to anonymize the PII, then store it in Amazon DynamoDB.==

Redshift is a SQL database and the question asked for a non SQL DB

---
##### 62. QUESTION
```
A financial services company plans to migrate its trading application from on-premises Microsoft Windows Server to Amazon Web Services (AWS).  
The solution must ensure high availability across multiple Availability Zones and offer low-latency access to block storage.

Which of the following solutions will fulfill these requirements?
```

Amazon FSx for NetApp ONTAP
- Multi AZ file system
- Designed for constant availability across AZs
- Sub-millisecond file operation latencies with SSD storage (Great for block storage workloads)
- fully supports block storage protocols like iSCSI (commonly used in Windows Server)

Windows server supports NetApp

==Configure the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon FSx for NetApp ONTAP to create a Multi-AZ file system and access the data via iSCSI protocol.==

Amazon FSx for Windows File Server is low-latency file storage for Windows environments. But does not support shared block storage.

# Wrong Questions Raw
```
[Skip to content](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#learndash-page-content)

[](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#)

## [![Tutorials Dojo Portal Logo Blue](https://td-portal-cdn.tutorialsdojo.com/wp-content/uploads/2020/01/tutorialsdojo-logo-blue.png)](https://portal.tutorialsdojo.com/)

[](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#)[](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#)

[](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#)

[](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#)

[![](https://td-portal-cdn.tutorialsdojo.com/wp-content/plugins/buddyboss-platform/bp-core/images/profile-avatar-buddyboss-50.png)](https://portal.tutorialsdojo.com/members/calvinator/)

[calvinator](https://portal.tutorialsdojo.com/members/calvinator/)

[My Account](https://portal.tutorialsdojo.com/members/calvinator/settings/)

---

- [Home](https://portal.tutorialsdojo.com/)
- [All Courses and eBooks](https://portal.tutorialsdojo.com/shop/)
- [My Courses](https://portal.tutorialsdojo.com/my-courses/)
- [SALE!](https://portal.tutorialsdojo.com/promotions)
- [PlayCloud Hands-On Labs](https://portal.tutorialsdojo.com/playcloud/)
- [FAQs](https://portal.tutorialsdojo.com/faqs/)
- [What’s New?](https://portal.tutorialsdojo.com/new-releases/)
- [Forums](https://portal.tutorialsdojo.com/forums/)
- [AWS Cheat Sheets](https://tutorialsdojo.com/aws-cheat-sheets/)
- [Microsoft Azure Cheat Sheets](https://tutorialsdojo.com/links-to-all-azure-cheat-sheets/)
- [Google Cloud Cheat Sheets](https://tutorialsdojo.com/links-to-all-google-cloud-gcp-cheat-sheets/)

---

- [My Dashboard](https://portal.tutorialsdojo.com/my-dashboard/)
- [Hands-On Labs](https://portal.tutorialsdojo.com/playclouds-lab-collection/)
- [My Downloads](https://portal.tutorialsdojo.com/billing-account/downloads/)
- [My Account](https://portal.tutorialsdojo.com/billing-account/)
- [SALE!](https://portal.tutorialsdojo.com/promotions)
- [Microsoft Learn](https://learn.microsoft.com/en-us/)
- [Shop](https://portal.tutorialsdojo.com/shop/)
- [Subscribe on YouTube](https://www.youtube.com/channel/UCw4cxFG34-cCbG5RdA-7mTw)
- [Connect with Jon](https://www.linkedin.com/in/jonbonso/)
- [Join us on Slack](http://slack.tutorialsdojo.com/)
- [Be an Affiliate!](https://portal.tutorialsdojo.com/faq-for-affiliate-marketing/)
- [Email Support](mailto:support@tutorialsdojo.com)
- [Terms of Service](https://portal.tutorialsdojo.com/terms-of-service/)
- [Join Our Team!](https://tutorialsdojo.com/join-dojo/)
- [Log Out](https://portal.tutorialsdojo.com/login/?action=logout&redirect_to=https%3A%2F%2Fportal.tutorialsdojo.com%2Fcourses%2Faws-certified-solutions-architect-associate-practice-exams%2Flessons%2Fpractice-exams-timed-mode-4%2Fquizzes%2Faws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1%2F&_wpnonce=ff20eab5d5)

[Log Out](https://portal.tutorialsdojo.com/login/?action=logout&_wpnonce=ff20eab5d5)

[](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/#)

[Back to Course](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/ "AWS Certified Solutions Architect Associate Practice Exams SAA-C03 2025")

## AWS Certified Solutions Architect Associate Practice Exams SAA-C03 2025

0% Complete

Last activity on May 18, 2025 

1. [
    
    📊 Your Progress
    
    ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/%f0%9f%93%8a-your-progress/ "📊 Your Progress")
2. [Practice Exams – Timed Mode
    
    1 Topic](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/ "Practice Exams - Timed Mode") 
    
    1. [AWS Certified Solutions Architect Associate Practice Exam (Timed Mode) – Instructions
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/topic/aws-certified-solutions-architect-associate-practice-exam-timed-mode-instructions/ "AWS Certified Solutions Architect Associate Practice Exam (Timed Mode) - Instructions")
    
    - [Timed Mode Diagnostic Test – AWS Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/timed-mode-diagnostic-test-aws-solutions-architect-associate/ "Timed Mode Diagnostic Test - AWS Solutions Architect Associate")
    - [Timed Mode Set 1 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-1/ "Timed Mode Set 1 - AWS Certified Solutions Architect Associate")
    - [Timed Mode Set 2 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-2/ "Timed Mode Set 2 - AWS Certified Solutions Architect Associate")
    - [Timed Mode Set 3 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-3/ "Timed Mode Set 3 - AWS Certified Solutions Architect Associate")
    - [Timed Mode Set 4 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-4/ "Timed Mode Set 4 - AWS Certified Solutions Architect Associate")
    - [Timed Mode Set 5 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-5/ "Timed Mode Set 5 - AWS Certified Solutions Architect Associate")
    - [Timed Mode Set 6 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-6/ "Timed Mode Set 6 - AWS Certified Solutions Architect Associate")
    - [Timed Mode Set 7 – AWS Certified Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/timed-mode-bonus-test-aws-certified-solutions-architect-associate/ "Timed Mode Set 7 - AWS Certified Solutions Architect Associate")
    - [Bonus Timed Mode Set 8 – AWS Solutions Architect Associate
        
        ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/bonus-timed-mode-set-8-aws-solutions-architect-associate/ "Bonus Timed Mode Set 8 - AWS Solutions Architect Associate")
    
3. [Practice Exams – Review Mode
    
    1 Topic](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-review-mode-4/ "Practice Exams - Review Mode") 
4. [Practice Exams – Section-Based
    
    1 Topic](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-section-based-4/ "Practice Exams - Section-Based") 
5. [
    
    Practice Exams – Topic-Based
    
    ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-topic-based/ "Practice Exams - Topic-Based")
6. [Final Test
    
    1 Topic](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/final-test-4/ "Final Test") 
7. [BONUS SECTION – FLASHCARDS
    
    2 Topics](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/bonus-section-flashcards-4/ "BONUS SECTION - FLASHCARDS") 
8. [
    
    Additional Resources for the AWS Certified Solutions Architect Associate Exam
    
    ](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/additional-resources-for-the-aws-certified-solutions-architect-associate-exam-2/ "Additional Resources for the AWS Certified Solutions Architect Associate Exam")

QUIZ 2 OF 36

May 17, 2026

[←Previous](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/timed-mode-diagnostic-test-aws-solutions-architect-associate/)[Next →](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/lessons/practice-exams-timed-mode-4/quizzes/aws-certified-solutions-architect-associate-practice-exam-timed-mode-set-2/)

# Timed Mode Set 1 – AWS Certified Solutions Architect Associate

36 of 65 questions answered correctly

YOUR TIME: 01:25:59

You have reached 36 of 65 point(s), (55.38%)

#### Categories

1. CSAA – Design Cost-Optimized Architectures33.33%
2. CSAA – Design High-Performing Architectures57.14%
3. CSAA – Design Resilient Architectures52.63%
4. CSAA – Design Secure Architectures59.09%

- Sorry, you failed the test. Carefully read our detailed explanations including the references and [cheat sheets](https://tutorialsdojo.com/links-to-all-aws-cheat-sheets/) then try again.
    
    To view your record of all previous attempts:
    
    ![](https://td-portal-cdn.tutorialsdojo.com/wp-content/uploads/2020/10/tutorialsdojo-view-results.gif)
    
    Visit our [FAQ page](https://portal.tutorialsdojo.com/faqs/) for more information on the site’s features.
    

  View All Questions  View Incorrect Answers  View Correct Answers 

1. 1
2. 2
3. 3
4. 4
5. 5
6. 6
7. 7
8. 8
9. 9
10. 10
11. 11
12. 12
13. 13
14. 14
15. 15
16. 16
17. 17
18. 18
19. 19
20. 20
21. 21
22. 22
23. 23
24. 24
25. 25
26. 26
27. 27
28. 28
29. 29
30. 30
31. 31
32. 32
33. 33
34. 34
35. 35
36. 36
37. 37
38. 38
39. 39
40. 40
41. 41
42. 42
43. 43
44. 44
45. 45
46. 46
47. 47
48. 48
49. 49
50. 50
51. 51
52. 52
53. 53
54. 54
55. 55
56. 56
57. 57
58. 58
59. 59
60. 60
61. 61
62. 62
63. 63
64. 64
65. 65

66. ✔️ Answered
67. 🏳️ For Review

68. ##### 2. QUESTION
    
    A company hosted a web application in an Auto Scaling group of EC2 instances. The IT manager is concerned about the over-provisioning of the resources that can cause higher operating costs. A Solutions Architect has been instructed to create a cost-effective solution without affecting the performance of the application.
    
    Which dynamic scaling policy should be used to satisfy this requirement?
    
    - Use suspend and resume scaling.
    - Use simple scaling.
    - Use target tracking scaling.
    - Use scheduled scaling.
    
    Incorrect
    
    An **Auto Scaling group** contains a collection of Amazon EC2 instances that are treated as a logical grouping for the purposes of automatic scaling and management. An Auto Scaling group also enables you to use Amazon EC2 Auto Scaling features such as health check replacements and scaling policies. Both maintaining the number of instances in an Auto Scaling group and automatic scaling are the core functionality of the Amazon EC2 Auto Scaling service. The size of an Auto Scaling group depends on the number of instances that you set as the desired capacity. You can adjust its size to meet demand, either manually or by using automatic scaling.
    
    Step scaling policies and simple scaling policies are two of the dynamic scaling options available for you to use. Both require you to create CloudWatch alarms for the scaling policies. Both require you to specify the high and low thresholds for the alarms. Both require you to define whether to add or remove instances, and how many, or set the group to an exact size. The main difference between the policy types is the step adjustments that you get with step scaling policies. When step adjustments are applied, and they increase or decrease the current capacity of your Auto Scaling group, the adjustments vary based on the size of the alarm breach.
    
    ![](https://media.tutorialsdojo.com/as-basic-diagram.png)
    
    The primary issue with simple scaling is that after a scaling activity is started, the policy must wait for the scaling activity or health check replacement to complete and the cooldown period to expire before responding to additional alarms. Cooldown periods help to prevent the initiation of additional scaling activities before the effects of previous activities are visible.
    
    With a target tracking scaling policy, you can increase or decrease the current capacity of the group based on a target value for a specific metric. This policy will help resolve the over-provisioning of your resources. The scaling policy adds or removes capacity as required to keep the metric at, or close to, the specified target value. In addition to keeping the metric close to the target value, a target tracking scaling policy also adjusts to changes in the metric due to a changing load pattern.
    
    Hence, the correct answer is: **Use target tracking scaling.**
    
    The option that says: **Use simple scaling** is incorrect because you need to wait for the cooldown period to complete before initiating additional scaling activities. Target tracking or step scaling policies can trigger a scaling activity immediately without waiting for the cooldown period to expire.
    
    The option that says: **Use scheduled scaling** is incorrect because this policy is mainly used for predictable traffic patterns. You need to use the target tracking scaling policy to optimize the cost of your infrastructure without affecting the performance.
    
    The option that says: **Use suspend and resume scaling** is incorrect because this type is used to temporarily pause scaling activities triggered by your scaling policies and scheduled actions.
    
    **References:** 
    
    [https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html)
    
    [https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)
    
    **Check out this AWS Auto Scaling Cheat Sheet:**
    
    [https://tutorialsdojo.com/aws-auto-scaling/](https://tutorialsdojo.com/aws-auto-scaling/)
    
69. ##### 4. QUESTION
    
    A company uses an Application Load Balancer (ALB) for its public-facing multi-tier web applications. The security team has recently reported that there has been a surge of SQL injection attacks lately, which causes critical data discrepancy issues. The same issue is also encountered by its other web applications in other AWS accounts that are behind an ALB. An immediate solution is required to prevent the remote injection of unauthorized SQL queries and protect their applications hosted across multiple accounts.
    
    As a Solutions Architect, what solution would you recommend?
    
    - Use AWS Network Firewall to filter web vulnerabilities and brute force attacks using stateful rule groups across all Application Load Balancers on all AWS accounts. Refactor the web application to be less susceptible to SQL injection attacks based on the security assessment.
    - Use AWS WAF and set up a managed rule to block request patterns associated with the exploitation of SQL databases, like SQL injection attacks. Associate it with the Application Load Balancer. Integrate AWS WAF with AWS Firewall Manager to reuse the rules across all the AWS accounts.
    - Use Amazon GuardDuty and set up a managed rule to block request patterns associated with the exploitation of SQL databases, like SQL injection attacks. Associate it with the Application Load Balancer and utilize the AWS Security Hub service to reuse the managed rules across all the AWS accounts
    - Use Amazon Macie to scan for vulnerabilities and unintended network exposure. Refactor the web application to be less susceptible to SQL injection attacks based on the security assessment. Utilize the AWS Audit Manager to reuse the security assessment across all AWS accounts.
    
    Incorrect
    
    **AWS WAF** is a web application firewall that lets you monitor the HTTP(S) requests that are forwarded to an Amazon CloudFront distribution, an Amazon API Gateway REST API, an Application Load Balancer, or an AWS AppSync GraphQL API.
    
    **-Web ACLs** – You use a web access control list (ACL) to protect a set of AWS resources. You create a web ACL and define its protection strategy by adding rules. Rules define criteria for inspecting web requests and specify how to handle requests that match the criteria. You set a default action for the web ACL that indicates whether to block or allow through those requests that pass the rules inspections.
    
    **-Rules** – Each rule contains a statement that defines the inspection criteria and an action to take if a web request meets the criteria. When a web request meets the criteria, that’s a match. You can configure rules to block matching requests, allow them through, count them, or run CAPTCHA controls against them.
    
    **-Rules groups** – You can use rules individually or in reusable rule groups. AWS Managed Rules and AWS Marketplace sellers provide managed rule groups for your use. You can also define your own rule groups.
    
    **AWSManagedRulesSQLiRuleSet** – The SQL database rule group contains rules to block request patterns associated with the exploitation of SQL databases, like SQL injection attacks. This can help prevent remote injection of unauthorized queries. Evaluate this rule group for use if your application interfaces with an SQL database.
    
    ![](https://media.tutorialsdojo.com/aws-waf-sql-injection-attack-prevention.png)
    
    AWS WAF is easy to deploy and protect applications deployed on either Amazon CloudFront as part of your CDN solution, the Application Load Balancer that fronts all your origin servers, Amazon API Gateway for your REST APIs, or AWS AppSync for your GraphQL APIs. There is no additional software to deploy, DNS configuration, SSL/TLS certificate to manage, or need for a reverse proxy setup.
    
    With AWS Firewall Manager integration, you can centrally define and manage your rules and reuse them across all the web applications that you need to protect.
    
    Therefore, the correct answer is: **Use AWS WAF and set up a managed rule to block request patterns associated with the exploitation of SQL databases, like SQL injection attacks. Associate it with the Application Load Balancer. Integrate AWS WAF with AWS Firewall Manager to reuse the rules across all the AWS accounts.**
    
    The option that says: **Use Amazon GuardDuty and set up a managed rule to block request patterns associated with the exploitation of SQL databases, like SQL injection attacks. Associate it with the Application Load Balancer and utilize the AWS Security Hub service to reuse the managed rules across all the AWS accounts**is incorrect because Amazon GuardDuty is only a threat detection service and cannot directly be integrated with the Application Load Balancer.
    
    The options that says: **Use AWS Network Firewall to filter web vulnerabilities and brute force attacks using stateful rule groups across all Application Load Balancers on all AWS accounts. Refactor the web application to be less susceptible to SQL injection attacks based on the security assessment**is incorrect because AWS Network Firewall is a managed service that is primarily used to deploy essential network protections for all of your Amazon Virtual Private Clouds (VPCs) and not particularly to your Application Load Balancers. Take note that the AWS Network Firewall is account-specific by default and needs to be integrated with the AWS Firewall Manager to easily share the firewall across your other AWS accounts. In addition, refactoring the web application will require an immense amount of time.
    
    The options that says: **Use Amazon Macie to scan for vulnerabilities and unintended network exposure. Refactor the web application to be less susceptible to SQL injection attacks based on the security assessment. Utilize the AWS Audit Manager to reuse the security assessment across all AWS accounts** is incorrect because Amazon Macie is only used for data security and data privacy service that uses machine learning and pattern matching to discover and protect your sensitive data. Just like before, refactoring the web application will require an immense amount of time. The use of the AWS Audit Manager is not relevant as well. The AWS Audit Manager simply helps you continually audit your AWS usage to simplify how you manage risk and compliance with regulations and industry standards.
    
    **References:** 
    
    [https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html](https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html)
    
    [https://docs.aws.amazon.com/waf/latest/developerguide/fms-chapter.html](https://docs.aws.amazon.com/waf/latest/developerguide/fms-chapter.html)
    
    [https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-use-case.html#aws-managed-rule-groups-use-case-sql-db](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-use-case.html#aws-managed-rule-groups-use-case-sql-db)
    
    **Check out this AWS Web Application Firewall Cheat Sheet:**
    
    [https://tutorialsdojo.com/aws-waf](https://tutorialsdojo.com/aws-waf/)
    
70. ##### 5. QUESTION
    
    A business has recently migrated its applications to AWS. The audit team must be able to assess whether the services the company is using meet common security and regulatory standards. A solutions architect needs to provide the team with a report of all compliance-related documents for their account.
    
     Which action should a solutions architect consider?
    
    - Run an Amazon Inspector assessment job to download all of the AWS compliance-related information.
    - Use AWS Artifact to view the security reports as well as other AWS compliance-related information. 
    - Run an Amazon Macie job to view the Service Organization Control (SOC), Payment Card Industry (PCI), and other compliance reports from AWS Certificate Manager (ACM).
    - View all of the AWS security compliance reports from AWS Security Hub.
    
    Incorrect
    
    **AWS Artifact** is your go-to, central resource for compliance-related information that matters to you. It provides on-demand access to AWS’ security and compliance reports and select online agreements. Reports available in AWS Artifact include our Service Organization Control (SOC) reports, Payment Card Industry (PCI) reports, and certifications from accreditation bodies across geographies and compliance verticals that validate the implementation and operating effectiveness of AWS security controls. Agreements available in AWS Artifact include the Business Associate Addendum (BAA) and the Nondisclosure Agreement (NDA).
    
    ![](https://media.tutorialsdojo.com/aws-artifact-console.jpg)
    
    All AWS Accounts have access to AWS Artifact. Root users and IAM users with admin permissions can download all audit artifacts available to their accounts by agreeing to the associated terms and conditions. You will need to grant IAM users with non-admin permissions access to AWS Artifact using IAM permissions. This allows you to grant a user access to AWS Artifact while restricting access to other services and resources within your AWS Account.
    
    Hence, the correct answer in this scenario is: **Use AWS Artifact to view the security reports as well as other AWS compliance-related information.**
    
    The option that says: **Run an Amazon Inspector assessment job to download all of the AWS compliance-related information** is incorrect. Amazon Inspector is simply a security tool for detecting vulnerabilities in AWS workloads. For this scenario, it is better to use the readily-available security reports in AWS Artifact instead.
    
    The option that says: **Run an Amazon Macie job to view the Service Organization Control (SOC), Payment Card Industry (PCI), and other compliance reports from AWS Certificate Manager (ACM)** is incorrect because ACM is just a service that lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with AWS services and your internal connected resources. This service does not store certifications or compliance-related documents.
    
    The option that says: **View all of the AWS security compliance reports from AWS Security Hub** is incorrect because AWS Security Hub only provides you a comprehensive view of your high-priority security alerts and security posture across your AWS accounts.
    
    **References:**
    
    [https://aws.amazon.com/artifact/getting-started/](https://aws.amazon.com/artifact/getting-started/)
    
    [https://docs.aws.amazon.com/artifact/latest/ug/what-is-aws-artifact.html](https://docs.aws.amazon.com/artifact/latest/ug/what-is-aws-artifact.html)
    
    **Check out this AWS Artifact Cheat Sheet:**
    
    [https://tutorialsdojo.com/aws-artifact/](https://tutorialsdojo.com/aws-artifact/)
    
71. ##### 6. QUESTION
    
    A financial application consists of an Auto Scaling group of Amazon EC2 instances, an Application Load Balancer, and a MySQL RDS instance set up in a Multi-AZ Deployment configuration. To protect customers’ confidential data, it must be ensured that the Amazon RDS database is only accessible using an authentication token specific to the profile credentials of EC2 instances.
    
    Which of the following actions should be taken to meet this requirement?
    
    - Enable the IAM DB Authentication.
    - Configure SSL in your application to encrypt the database connection to RDS.
    - Create an IAM Role and assign it to your EC2 instances which will grant exclusive access to your RDS instance.
    - Use a combination of IAM and STS to enforce restricted access to your RDS instance using a temporary authentication token.
    
    Incorrect
    
    You can authenticate to your DB instance using AWS Identity and Access Management (IAM) database authentication. IAM database authentication works with MySQL and PostgreSQL. With this authentication method, you don’t need to use a password when you connect to a DB instance. Instead, you use an authentication token.
    
    An **_authentication token_** is a unique string of characters that Amazon RDS generates on request. Authentication tokens are generated using AWS Signature Version 4. Each token has a lifetime of 15 minutes. You don’t need to store user credentials in the database, because authentication is managed externally using IAM. You can also still use standard database authentication.
    
    ![IAM database authentication](https://media.tutorialsdojo.com/2019-01-13_07-04-06-a2157247b0fa129795001208504fcb51.png)
    
    IAM database authentication provides the following benefits:
    
    1. Network traffic to and from the database is encrypted using Secure Sockets Layer (SSL).
    2. You can use IAM to centrally manage access to your database resources, instead of managing access individually on each DB instance.
    3. For applications running on Amazon EC2, you can use profile credentials specific to your EC2 instance to access your database instead of a password, for greater security
    
    Hence, the correct answer is: **Enable the IAM DB Authentication**.
    
    The option that says: **Configuring SSL in your application to encrypt the database connection to RDS** is incorrect because an SSL connection is not just using an authentication token from IAM. Although configuring SSL to your application can improve the security of your data in flight, it is still not a suitable option to use in this scenario.
    
    The option that says: **Creating an IAM Role and assigning it to your EC2 instances which will grant exclusive access to your RDS instance** is incorrect because although you can create and assign an IAM Role to your EC2 instances, you still need to configure your RDS to use IAM DB Authentication.
    
    The option that says: **Use a combination of IAM and STS to enforce restricted access to your RDS instance using a temporary authentication token** is incorrect because you have to use IAM DB Authentication for this scenario, and not simply a combination of an IAM and STS. Although STS is used to send temporary tokens for authentication, this is not a compatible use case for RDS.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAMDBAuth.html](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAMDBAuth.html)
    
    [https://aws.amazon.com/rds/](https://aws.amazon.com/rds/%C2%A0) 
    
    **Check out this Amazon RDS cheat sheet:**
    
    [https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)
    
72. ##### 7. QUESTION
    
    A tech company that you are working for has undertaken a Total Cost Of Ownership (TCO) analysis evaluating the use of Amazon S3 versus acquiring more storage hardware. The result was that all 1200 employees would be granted access to use Amazon S3 for the storage of their personal documents. 
    
    Which of the following will you need to consider so you can set up a solution that incorporates a single sign-on feature from your corporate AD or LDAP directory and also restricts access for each individual user to a designated user folder in an S3 bucket? (Select TWO.)
    
    - Map each individual user to a designated user folder in S3 using Amazon WorkDocs to access their personal documents.
    - Set up a matching IAM user for each of the 1200 users in your corporate directory that needs access to a folder in the S3 bucket.
    - Set up a Federation proxy or an Identity provider, and use AWS Security Token Service to generate temporary tokens.
    - Use 3rd party Single Sign-On solutions such as Atlassian Crowd, OKTA, OneLogin and many others.
    - Configure an IAM role and an IAM Policy to access the bucket.
    
    Incorrect
    
    The question refers to one of the common scenarios for temporary credentials in AWS. Temporary credentials are useful in scenarios that involve identity federation, delegation, cross-account access, and IAM roles. In this example, it is called **enterprise identity federation,** considering that you also need to set up a single sign-on (SSO) capability.
    
    The correct answers are:
    
    **– Setup a Federation proxy or an Identity provider, and** **use AWS Security Token Service to generate temporary tokens** 
    
    **– Configure an IAM role and an IAM Policy to access the bucket.** 
    
    ![](https://media.tutorialsdojo.com/saml-based-federation.diagram.png)In an enterprise identity federation, you can authenticate users in your organization’s network, and then provide those users access to AWS without creating new AWS identities for them and requiring them to sign in with a separate user name and password. This is known as the _single sign-on_ (SSO) approach to temporary access. AWS STS supports open standards like Security Assertion Markup Language (SAML) 2.0, with which you can use Microsoft AD FS to leverage your Microsoft Active Directory. You can also use SAML 2.0 to manage your own solution for federating user identities.
    
    **Using 3rd party Single Sign-On solutions such as Atlassian Crowd, OKTA, OneLogin and many others** is incorrect since you don’t have to use 3rd party solutions to provide the access. AWS already provides the necessary tools that you can use in this situation.
    
    **Mapping each individual user to a designated user folder in S3 using Amazon WorkDocs to access their personal documents** is incorrect as there is no direct way of integrating Amazon S3 with Amazon WorkDocs for this particular scenario. Amazon WorkDocs is simply a fully managed, secure content creation, storage, and collaboration service. With Amazon WorkDocs, you can easily create, edit, and share content. And because it’s stored centrally on AWS, you can access it from anywhere on any device.
    
    **Setting up a matching IAM user for each of the 1200 users in your corporate directory that needs access to a folder in the S3 bucket**is incorrect since creating that many IAM users would be unnecessary. Also, you want the account to integrate with your AD or LDAP directory, hence, IAM Users does not fit these criteria.
    
    **References:**
    
    [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html)
    
    [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html)
    
    [https://aws.amazon.com/premiumsupport/knowledge-center/iam-s3-user-specific-folder/](https://aws.amazon.com/premiumsupport/knowledge-center/iam-s3-user-specific-folder/)
    
    **Check out this AWS IAM Cheat Sheet:** 
    
    [https://tutorialsdojo.com/aws-identity-and-access-management-iam/](https://tutorialsdojo.com/aws-identity-and-access-management-iam/)
    
73. ##### 8. QUESTION
    
    A Forex trading platform, which frequently processes and stores global financial data every minute, is hosted in an on-premises data center and uses an Oracle database. Due to a recent cooling problem in its data center, the company urgently needs to migrate its infrastructure to AWS to improve the performance of its applications. As the Solutions Architect, the responsibility is to ensure that the database is properly migrated and remains available in case of database server failure in the future.
    
    Which combination of actions would meet the requirement? (Select TWO.)
    
    - Migrate the Oracle database to AWS using the AWS Database Migration Service
    - Launch an Oracle database instance in Amazon RDS with Recovery Manager (RMAN) enabled.
    - Convert the database schema using the AWS Schema Conversion Tool. 
    - Create an Oracle database in Amazon RDS with Multi-AZ deployments.
    - Migrate the Oracle database to a non-cluster Amazon Aurora with a single instance.
    
    Incorrect
    
    Amazon RDS Multi-AZ deployments provide enhanced availability and durability for Database (DB) Instances, making them a natural fit for production database workloads. When you provision a Multi-AZ DB Instance, Amazon RDS automatically creates a primary DB Instance and synchronously replicates the data to a standby instance in a different Availability Zone (AZ). Each AZ runs on its own physically distinct, independent infrastructure, and is engineered to be highly reliable.
    
    ![Amazon RDS Multi-AZ deployments](https://media.tutorialsdojo.com/con-multi-AZ.png)
    
    In case of an infrastructure failure, Amazon RDS performs an automatic failover to the standby (or to a read replica in the case of Amazon Aurora) so that you can resume database operations as soon as the failover is complete. Since the endpoint for your DB Instance remains the same after a failover, your application can resume database operation without the need for manual administrative intervention.
    
    In this scenario, the best RDS configuration to use is an Oracle database in RDS with Multi-AZ deployments to ensure high availability even if the primary database instance goes down. You can use AWS DMS to move the on-premises database to AWS with minimal downtime and zero data loss. It supports over 20 engines, including Oracle to Aurora MySQL, MySQL to RDS for MySQL, SQL Server to Aurora PostgreSQL, MongoDB to DocumentDB, Oracle to Redshift, and S3.
    
    Hence, the correct answers are:
    
    **– Create an Oracle database in Amazon RDS with Multi-AZ deployments.**
    
    – **Migrate the Oracle database to AWS using the AWS Database Migration Service.**
    
    The option that says: **Launch an Oracle database instance in Amazon RDS with Recovery Manager (RMAN) enabled** is incorrect because Oracle RMAN is not supported in RDS.
    
    The option that says: **Convert the database schema using the AWS Schema Conversion Tool** is incorrect. AWS Schema Conversion Tool is typically used for heterogeneous migrations where you’re moving from one type of database to another (e.g., Oracle to PostgreSQL). In the scenario, the migration is homogenous, meaning it’s an Oracle-to-Oracle migration. As a result, there’s no need to convert the schema since you’re staying within the same database type.
    
    The option that says: **Migrate the Oracle database to a non-cluster Amazon Aurora with a single instance** is incorrect. While a single-instance Aurora can be a feasible solution for non-critical applications or environments like development or testing, it is typically not suitable for applications that demand high availability.
    
    **References:**
    
    [https://aws.amazon.com/rds/details/multi-az/](https://aws.amazon.com/rds/details/multi-az/)
    
    [https://aws.amazon.com/dms/](https://aws.amazon.com/dms/)
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)
    
    **Check out this Amazon RDS Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)
    
74. ##### 10. QUESTION
    
    A newly hired Solutions Architect is assigned to manage a set of CloudFormation templates that are used in the company’s cloud architecture in AWS. The Architect accessed the templates and tried to analyze the configured IAM policy for an S3 bucket.
    
    { 
     "Version": "2012-10-17", 
     "Statement": [ 
      { 
       "Effect": "Allow", 
       "Action": [ 
        "s3:Get*", 
        "s3:List*" 
      ], 
      "Resource": "*" 
     }, 
     { 
       "Effect": "Allow", 
       "Action": "s3:PutObject", 
       "Resource": "arn:aws:s3:::boracay/*" 
      } 
     ] 
    }
    
    What does the above IAM policy allow? (Select THREE.)
    
    - An IAM user with this IAM policy is allowed to read and delete objects from the `boracay` S3 bucket.
    - An IAM user with this IAM policy is allowed to change access rights for the `boracay` S3 bucket.
    - An IAM user with this IAM policy is allowed to read objects from the `boracay` S3 bucket.
    - An IAM user with this IAM policy is allowed to read objects from all S3 buckets owned by the account.
    - An IAM user with this IAM policy is allowed to write objects into the `boracay` S3 bucket.
    - An IAM user with this IAM policy is allowed to read objects in the `boracay` S3 bucket but not allowed to list the objects in the bucket.
    
    Incorrect
    
    You manage access in AWS by creating policies and attaching them to IAM identities (users, groups of users, or roles) or AWS resources. A policy is an object in AWS that, when associated with an identity or resource, defines their permissions. AWS evaluates these policies when an IAM principal (user or role) makes a request. Permissions in the policies determine whether the request is allowed or denied. Most policies are stored in AWS as JSON documents. AWS supports six types of policies: identity-based policies, resource-based policies, permissions boundaries, AWS Organizations SCPs, ACLs, and session policies.
    
    IAM policies define permissions for action regardless of the method that you use to perform the operation. For example, if a policy allows the [GetUser](https://docs.aws.amazon.com/IAM/latest/APIReference/API_GetUser.html) action, then a user with that policy can get user information from the AWS Management Console, the AWS CLI, or the AWS API. When you create an IAM user, you can choose to allow console or programmatic access. If console access is allowed, the IAM user can sign in to the console using a user name and password. Or if programmatic access is allowed, the user can use access keys to work with the CLI or API.
    
    ![](https://media.tutorialsdojo.com/CC_Diagram1_0717.png)
    
    Based on the provided IAM policy, the user is only allowed to get, write, and list all of the objects for the `boracay` s3 bucket. The `s3:PutObject` basically means that you can submit a PUT object request to the S3 bucket to store data.
    
    Hence, the correct answers are:
    
    **– An IAM user with this IAM policy is allowed to read objects from all S3 buckets owned by the account.**
    
    **– An IAM user with this IAM policy is allowed to write objects into the `boracay` S3 bucket.**
    
    **– An IAM user with this IAM policy is allowed to read objects from the `boracay` S3 bucket.**
    
    The option that says: **An IAM user with this IAM policy is allowed to change access rights for the `boracay` S3 bucket** is incorrect because the template does not have any statements which allow the user to change access rights in the bucket.
    
    The option that says: **An IAM user with this IAM policy is allowed to read objects in the `boracay` S3 bucket but not allowed to list the objects in the bucket** is incorrect because it can clearly be seen in the template that there is a `s3:List*` which permits the user to list objects.
    
    The option that says: **An IAM user with this IAM policy is allowed to read and delete objects from the `boracay` S3 bucket** is incorrect. Although you can read objects from the bucket, you cannot delete any objects.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectOps.html](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectOps.html)
    
    [https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
    
    **Check out this Amazon S3 Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-s3/](https://tutorialsdojo.com/amazon-s3/)
    
75. ##### 11. QUESTION
    
    An online shopping platform is hosted on an Auto Scaling group of Amazon EC2 Spot instances and utilizes Amazon Aurora PostgreSQL as its database. It is required to optimize database workloads in the cluster by directing the production traffic to high-capacity instances and routing the reporting queries from the internal staff to the low-capacity instances.
    
    Which is the most suitable configuration for the application as well as the Aurora database cluster to achieve this requirement?
    
    - Create a custom endpoint in Aurora based on the specified criteria for the production traffic and another custom endpoint to handle the reporting queries.
    - Configure your application to use the reader endpoint for both production traffic and reporting queries, which will enable your Aurora database to automatically perform load-balancing among all the Aurora Replicas.
    - In your application, use the instance endpoint of your Aurora database to handle the incoming production traffic and use the cluster endpoint to handle reporting queries.
    - Do nothing since by default, Aurora will automatically direct the production traffic to your high-capacity instances and the reporting queries to your low-capacity instances.
    
    Incorrect
    
    **Amazon Aurora** typically involves a cluster of DB instances instead of a single instance. Each connection is handled by a specific DB instance. When you connect to an Aurora cluster, the host name and port that you specify point to an intermediate handler called an _endpoint_. Aurora uses the endpoint mechanism to abstract these connections. Thus, you don’t have to hardcode all the hostnames or write your own logic for load-balancing and rerouting connections when some DB instances aren’t available.
    
    For certain Aurora tasks, different instances or groups of instances perform different roles. For example, the primary instance handles all data definition language (DDL) and data manipulation language (DML) statements. Up to 15 Aurora Replicas handle read-only query traffic.
    
    ![Amazon Aurora endpoints](https://media.tutorialsdojo.com/2019-04-03_00-40-18-850e58ca7802358579be53729bfc0cbb.gif)
    
    Using endpoints, you can map each connection to the appropriate instance or group of instances based on your use case. For example, to perform DDL statements you can connect to whichever instance is the primary instance. To perform queries, you can connect to the reader endpoint, with Aurora automatically performing load-balancing among all the Aurora Replicas. For clusters with DB instances of different capacities or configurations, you can connect to custom endpoints associated with different subsets of DB instances. For diagnosis or tuning, you can connect to a specific instance endpoint to examine details about a specific DB instance.
    
    The custom endpoint provides load-balanced database connections based on criteria other than the read-only or read-write capability of the DB instances. For example, you might define a custom endpoint to connect to instances that use a particular AWS instance class or a particular DB parameter group. Then you might tell particular groups of users about this custom endpoint. For example, you might direct internal users to low-capacity instances for report generation or ad hoc (one-time) querying, and direct production traffic to high-capacity instances.
    
    Hence, the correct answer is: **Create a custom endpoint in Aurora based on the specified criteria for the production traffic and another custom endpoint to handle the reporting queries.**
    
    The option that says: **Configuring your application to use the reader endpoint for both production traffic and reporting queries, which will enable your Aurora database to automatically perform load-balancing among all the Aurora Replicas** is incorrect. Although it is true that a reader endpoint enables your Aurora database to automatically perform load-balancing among all the Aurora Replicas, it is quite limited to doing read operations only. You still need to use a custom endpoint to load-balance the database connections based on the specified criteria.
    
    The option that says: **In your application, use the instance endpoint of your Aurora database to handle the incoming production traffic and use the cluster endpoint to handle reporting queries** is incorrect because a cluster endpoint (also known as a writer endpoint) for an Aurora DB cluster simply connects to the current primary DB instance for that DB cluster. This endpoint can perform write operations in the database such as DDL statements, which is perfect for handling production traffic but not suitable for handling queries for reporting since there will be no write database operations that will be sent. Moreover, the endpoint does not point to lower-capacity or high-capacity instances as per the requirement. A better solution for this is to use a custom endpoint.
    
    The option that says: **Do nothing since by default, Aurora will automatically direct the production traffic to your high-capacity instances and the reporting queries to your low-capacity instances** is incorrect because Aurora does not do this by default. You have to create custom endpoints in order to accomplish this requirement.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.Endpoints.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.Endpoints.html)
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Endpoints.Custom.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Endpoints.Custom.html)
    
    **Check out this Amazon Aurora Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-aurora/](https://tutorialsdojo.com/amazon-aurora/)
    
76. ##### 15. QUESTION
    
    A company plans to host a web application in an Auto Scaling group of Amazon EC2 instances. The application will be used globally by users to upload and store several types of files. Based on user trends, files that are older than 2 years must be stored in a different storage class. The Solutions Architect of the company needs to create a cost-effective and scalable solution to store the old files yet still provide durability and high availability.
    
    Which of the following approach can be used to fulfill this requirement? (Select TWO.)
    
    - Use Amazon EFS and create a lifecycle policy that will move the objects to Amazon EFS-IA after 2 years.
    - Use Amazon S3 and create a lifecycle policy that will move the objects to Amazon S3 Glacier after 2 years.
    - Use Amazon EBS volumes to store the files. Configure the Amazon Data Lifecycle Manager (DLM) to schedule snapshots of the volumes after 2 years.
    - Use Amazon S3 and create a lifecycle policy that will move the objects to Amazon S3 Standard-IA after 2 years. 
    - Use a RAID 0 storage configuration that stripes multiple Amazon EBS volumes together to store the files. Configure the Amazon Data Lifecycle Manager (DLM) to schedule snapshots of the volumes after 2 years.
    
    Incorrect
    
    **Amazon S3** stores data as objects within buckets. An object is a file and any optional metadata that describes the file. To store a file in Amazon S3, you upload it to a bucket. When you upload a file as an object, you can set permissions on the object and any metadata. Buckets are containers for objects. You can have one or more buckets. You can control access for each bucket, deciding who can create, delete, and list objects in it. You can also choose the geographical region where Amazon S3 will store the bucket and its contents and view access logs for the bucket and its objects.
    
    ![Transitioning objects using Amazon S3 Lifecycle - Amazon Simple Storage Service](https://media.tutorialsdojo.com/lifecycle-transitions-v2.png)
    
    To move a file to a different storage class, you can use Amazon S3 or Amazon EFS. Both services have lifecycle configurations. Take note that Amazon EFS can only transition a file to the IA storage class after 90 days. Since you need to move the files that are older than 2 years to a more cost-effective and scalable solution, you should use the Amazon S3 lifecycle configuration. With S3 lifecycle rules, you can transition files to S3 Standard IA or S3 Glacier. Using S3 Glacier expedited retrieval, you can quickly access your files within 1-5 minutes.
    
    Hence, the correct answers are:
    
    **– Use Amazon S3 and create a lifecycle policy that will move the objects to Amazon S3 Glacier after 2 years.**
    
    **– Use Amazon S3 and create a lifecycle policy that will move the objects to Amazon S3 Standard-IA after 2 years.**
    
    The option that says: **Use Amazon EFS and create a lifecycle policy that will move the objects to Amazon EFS-IA after 2 years** is incorrect because the maximum days of EFS lifecycle policies is only up to 365 days. Therefore, it still does not meet the requirement of moving files older than 2 years or 730 days.
    
    The option that says: **Use Amazon EBS volumes to store the files. Configure the Amazon Data Lifecycle Manager (DLM) to schedule snapshots of the volumes after 2 years** is incorrect because the Amazon EBS simply costs more and is not as scalable as Amazon S3. It has some limitations when accessed by multiple EC2 instances. There are also huge costs involved in using the multi-attach feature on a Provisioned IOPS EBS volume to allow multiple EC2 instances to access the volume.
    
    The option that says: **Use a RAID 0 storage configuration that stripes multiple Amazon EBS volumes together to store the files. Configure the Amazon Data Lifecycle Manager (DLM) to schedule snapshots of the volumes after 2 years** is incorrect because RAID (Redundant Array of Independent Disks) is just a data storage virtualization technology that combines multiple storage devices to achieve higher performance or data durability. RAID 0 can stripe multiple volumes together for greater I/O performance than you can achieve with a single volume. On the other hand, RAID 1 can mirror two volumes together to achieve on-instance redundancy.
    
    **References:** 
    
    [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html)
    
    [https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html](https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html)
    
    [https://docs.aws.amazon.com/efs/latest/ug/API_LifecyclePolicy.html](https://docs.aws.amazon.com/efs/latest/ug/API_LifecyclePolicy.html)
    
    [https://aws.amazon.com/s3/faqs/](https://aws.amazon.com/s3/faqs/)
    
    **Check out this Amazon S3 Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-s3/](https://tutorialsdojo.com/amazon-s3/)
    
77. ##### 16. QUESTION
    
    An AI-powered Forex trading application consumes thousands of data sets to train its machine learning model. The application’s workload requires a high-performance, parallel hot storage to process the training datasets concurrently. It also needs cost-effective cold storage to archive those datasets that yield low profit.
    
    Which of the following Amazon storage services should the developer use?
    
    - Use Amazon Elastic File System and Amazon S3 for hot and cold storage respectively.
    - Use Amazon FSx For Lustre and Amazon S3 for hot and cold storage respectively.
    - Use Amazon FSx For Lustre and the Provisioned IOPS SSD (io1) volumes of Amazon EBS for hot and cold storage respectively.
    - Use Amazon FSx For Windows File Server and Amazon S3 for hot and cold storage respectively.
    
    Incorrect
    
    **Hot storage** refers to the storage that keeps frequently accessed data (hot data). **Warm storage** refers to the storage that keeps less frequently accessed data (warm data). **Cold storage** refers to the storage that keeps rarely accessed data (cold data). In terms of pricing, the colder the data, the cheaper it is to store, and the costlier it is to access when needed.
    
    ![AWS Storage Services](https://media.tutorialsdojo.com/aws-storage-services.png)
    
    **Amazon FSx For Lustre** is a high-performance file system for fast processing of workloads. Lustre is a popular open-source **parallel file system** which stores data across multiple network file servers to maximize performance and reduce bottlenecks.
    
    **Amazon FSx for Windows File Server** is a fully managed Microsoft Windows file system with full support for the SMB protocol, Windows NTFS, and Microsoft Active Directory (AD) Integration.
    
    **Amazon Elastic File System** is a fully-managed file storage service that makes it easy to set up and scale file storage in the Amazon Cloud. 
    
    **Amazon S3 is** an object storage service that offers industry-leading scalability, data availability, security, and performance. S3 offers different storage tiers for different use cases (frequently accessed data, infrequently accessed data, and rarely accessed data).
    
     The question has two requirements:
    
    1. High-performance, parallel hot storage to process the training datasets concurrently.
    2. Cost-effective cold storage to keep the archived datasets that are accessed infrequently
    
    In this case, we can use **Amazon FSx For Lustre** for the first requirement, as it provides a high-performance, parallel file system for hot data. On the second requirement, we can use Amazon S3 for storing cold data. Amazon S3 supports a cold storage system via Amazon S3 Glacier / Glacier Deep Archive.
    
    Hence, the correct answer is: **Use Amazon FSx For Lustre and Amazon S3 for hot and cold storage respectively.**
    
    The option that says: **Use Amazon FSx For Lustre and Amazon EBS Provisioned IOPS SSD (io1) volumes for hot and cold storage respectively** is incorrect because the Provisioned IOPS SSD (io1) volumes are primarily designed for storing hot data (data that are frequently accessed) used in I/O-intensive workloads. EBS has a storage option called “Cold HDD,” but due to its price, it is not ideal for data archiving. EBS Cold HDD is much more expensive than Amazon S3 Glacier / Glacier Deep Archive and is often utilized in applications where sequential cold data is read less frequently.
    
    The option that says: **Use Amazon Elastic File System and Amazon S3 for hot and cold storage respectively** is incorrect. Although EFS supports concurrent access to data, it does not have the high-performance ability that is typically required for machine learning workloads.
    
    The option that says: **Use Amazon FSx For Windows File Server and Amazon S3 for hot and cold storage respectively** is incorrect because Amazon FSx For Windows File Server does not have a parallel file system, unlike Lustre.
    
    **References:**
    
    [https://aws.amazon.com/fsx/](https://aws.amazon.com/fsx/)
    
    [https://docs.aws.amazon.com/whitepapers/latest/cost-optimization-storage-optimization/aws-storage-services.html](https://docs.aws.amazon.com/whitepapers/latest/cost-optimization-storage-optimization/aws-storage-services.html)
    
    [https://aws.amazon.com/blogs/startups/picking-the-right-data-store-for-your-workload/](https://aws.amazon.com/blogs/startups/picking-the-right-data-store-for-your-workload/)
    
    **Check out this Amazon FSx Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-fsx/](https://tutorialsdojo.com/amazon-fsx/)
    
78. ##### 18. QUESTION
    
    A telecommunications company is planning to grant AWS Console access to its developers. Company policy requires the use of identity federation and role-based access control. Currently, the roles are already assigned via groups in the corporate Active Directory. 
    
    In this scenario, what combination of the following services can provide developers access to the AWS console? (Select TWO.)
    
    - AWS Directory Service Simple AD
    - AWS Directory Service AD Connector
    - IAM Roles
    - IAM Groups
    - AWS Lambda
    
    Incorrect
    
    **AWS Directory Service** provides multiple ways to use Amazon Cloud Directory and Microsoft Active Directory (AD) with other AWS services. Directories store information about users, groups, and devices, and administrators use them to manage access to information and resources. AWS Directory Service provides multiple directory choices for customers who want to use existing Microsoft AD or Lightweight Directory Access Protocol (LDAP)–aware applications in the cloud. It also offers those same choices to developers who need a directory to manage users, groups, devices, and access.
    
    ![AWS Directory Service - How it works](https://media.tutorialsdojo.com/directory_service_howitworks.80bfccbf2f5d1d63558ec3c086aff247147258f1.png)
    
    Considering that the company is using a corporate Active Directory, it is best to use AWS Directory Service AD Connector for easier integration. In addition, since the roles are already assigned using groups in the corporate Active Directory, it would be better to also use IAM Roles. Take note that you can assign an IAM Role to the users or groups from your Active Directory once it is integrated with your VPC via the AWS Directory Service AD Connector.
    
    Hence, the correct answers are:
    
    **–**  **AWS Directory Service AD Connector.** 
    
    **–**  **IAM Roles.** 
    
    **AWS Directory Service Simple AD** is incorrect because this only provides a subset of the features offered by AWS Managed Microsoft AD, including the ability to manage user accounts and group memberships, create and apply group policies, securely connect to Amazon EC2 instances, and provide Kerberos-based single sign-on (SSO). In this scenario, the more suitable component to use is the AD Connector since it is a directory gateway with which you can redirect directory requests to your on-premises Microsoft Active Directory.
    
    **IAM Groups** is incorrect because this is just a collection of _IAM_ users. _Groups_ let you specify permissions for multiple users, which can make it easier to manage the permissions for those users. In this scenario, the more suitable one to use is IAM Roles in order for permissions to create AWS Directory Service resources.
    
    **AWS Lambda** is incorrect because this is primarily used for serverless computing.
    
    **References:**
    
    [https://aws.amazon.com/blogs/security/how-to-connect-your-on-premises-active-directory-to-aws-using-ad-connector/](https://aws.amazon.com/blogs/security/how-to-connect-your-on-premises-active-directory-to-aws-using-ad-connector/)
    
    [https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ad_connector_getting_started.html](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ad_connector_getting_started.html)
    
    **Check out these AWS IAM and Directory Service Cheat Sheets:**
    
    [https://tutorialsdojo.com/aws-identity-and-access-management-iam/](https://tutorialsdojo.com/aws-identity-and-access-management-iam/)
    
    [https://tutorialsdojo.com/aws-directory-service/](https://tutorialsdojo.com/aws-directory-service/)
    
    **Here is a video tutorial on AWS Directory Service:**
    
    [https://youtu.be/4XeqotTYBtY](https://youtu.be/4XeqotTYBtY "Share link")
    
79. ##### 20. QUESTION
    
    A company is using a combination of API Gateway and AWS Lambda for the web services of an online web portal that is accessed by hundreds of thousands of clients each day. The company will be announcing a new revolutionary product, and it is expected that the web portal will receive a massive number of visitors from all around the globe.
    
    How can the back-end systems and applications be protected from traffic spikes?
    
    - Manually upgrade the Amazon EC2 instances being used by API Gateway
    - Use throttling limits in API Gateway
    - API Gateway will automatically scale and handle massive traffic spikes so you do not have to do anything.
    - Deploy Multi-AZ in API Gateway with Read Replica
    
    Incorrect
    
    **Amazon API Gateway** provides throttling at multiple levels including global and by a service call. Throttling limits can be set for standard rates and bursts. For example, API owners can set a rate limit of 1,000 requests per second for a specific method in their REST APIs, and also configure Amazon API Gateway to handle a burst of 2,000 requests per second for a few seconds.
    
    ![Amazon API Gateway](https://media.tutorialsdojo.com/New-API-GW-Diagram.c9fc9835d2a9aa00ef90d0ddc4c6402a2536de0d.png)
    
    Amazon API Gateway tracks the number of requests per second. Any requests over the limit will receive a 429 HTTP response. The client SDKs generated by Amazon API Gateway retry calls automatically when met with this response.
    
    Hence, the correct answer is: **Use throttling limits in API Gateway.**
    
    The option that says: **API Gateway will automatically scale and handle massive traffic spikes so you do not have to do anything** is incorrect. Although it can scale using AWS Edge locations, you still need to configure the throttling, typically to further manage the bursts of your APIs.
    
    The option that says: **Manually upgrade the Amazon EC2 instances being used by API Gateway** is incorrect because API Gateway is a fully managed service and hence, you do not have access to its underlying resources.
    
    The option that says: **Deploying Multi-AZ in API Gateway with Read Replica** is incorrect because only RDS has Multi-AZ and Read Replica capabilities, and not API Gateway.
    
    **References:**
    
    [https://aws.amazon.com/api-gateway/faqs/#Throttling_and_Caching](https://aws.amazon.com/api-gateway/faqs/#Throttling_and_Caching)
    
    [https://docs.aws.amazon.com/apigateway/](https://docs.aws.amazon.com/apigateway/)
    
    **Check out this Amazon API Gateway Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-api-gateway/](https://tutorialsdojo.com/amazon-api-gateway/)
    
80. ##### 21. QUESTION
    
    A global IT company with offices around the world has multiple AWS accounts. To improve efficiency and drive costs down, the Chief Information Officer (CIO) wants to set up a solution that centrally manages their AWS resources. This will allow them to procure AWS resources centrally and share resources such as AWS Transit Gateways, AWS License Manager configurations, or Amazon Route 53 Resolver rules across their various accounts.
    
    As the Solutions Architect, which combination of options should you implement in this scenario? (Select TWO.)
    
    - Consolidate all of the company's accounts using AWS ParallelCluster.
    - Use the AWS Identity and Access Management service to set up cross-account access that will easily and securely share your resources with your AWS accounts. 
    - Use the AWS Resource Access Manager (RAM) service to easily and securely share your resources with your AWS accounts. 
    - Use AWS Control Tower to easily and securely share your resources with your AWS accounts. 
    - Consolidate all of the company's accounts using AWS Organizations.
    
    Incorrect
    
    AWS Resource Access Manager (RAM) is a service that enables you to easily and securely share AWS resources with any AWS account or within your AWS Organization. You can share AWS Transit Gateways, Subnets, AWS License Manager configurations, and Amazon Route 53 Resolver rules resources with RAM.
    
    Many organizations use multiple accounts to create administrative or billing isolation, and limit the impact of errors. RAM eliminates the need to create duplicate resources in multiple accounts, reducing the operational overhead of managing those resources in every single account you own. You can create resources centrally in a multi-account environment, and use RAM to share those resources across accounts in three simple steps: create a Resource Share, specify resources, and specify accounts. RAM is available to you at no additional charge.
    
    ![](https://media.tutorialsdojo.com/product-page-diagram_AWS-Resource-Access-Manager(1).379df75d48a8e2cc6160859b7ca3626a9b9be0c1.png)
    
    You can procure AWS resources centrally, and use RAM to share resources such as subnets or License Manager configurations with other accounts. This eliminates the need to provision duplicate resources in every account in a multi-account environment, reducing the operational overhead of managing those resources in every account.
    
    AWS Organizations is an account management service that lets you consolidate multiple AWS accounts into an organization that you create and centrally manage. With Organizations, you can create member accounts and invite existing accounts to join your organization. You can organize those accounts into groups and attach policy-based controls.
    
    Hence, the correct combination of options in this scenario is:
    
     **– Consolidate all of the company’s accounts using AWS Organizations.**
    
     **– Use the AWS Resource Access Manager (RAM) service to easily and securely share your resources with your AWS accounts.**
    
    The option that says: **Use the AWS Identity and Access Management service to set up cross-account access that will easily and securely share your resources with your AWS accounts** is incorrect. Although you can delegate access to resources that are in different AWS accounts using IAM, this process is extremely tedious and entails a lot of operational overhead since you have to manually set up cross-account access to each and every AWS account of the company. A better solution is to use AWS Resources Access Manager instead.
    
    The option that says: **Use AWS Control Tower to easily and securely share your resources with your AWS accounts** is incorrect because AWS Control Tower simply offers the easiest way to set up and govern a new, secure, multi-account AWS environment. This is not the most suitable service to use to securely share your resources across AWS accounts or within your Organization. You have to use AWS Resources Access Manager (RAM) instead.
    
    The option that says: **Consolidate all of the company’s accounts using AWS ParallelCluster** is incorrect because AWS ParallelCluster is simply an AWS-supported open-source cluster management tool that makes it easy for you to deploy and manage High-Performance Computing (HPC) clusters on AWS. In this particular scenario, it is more appropriate to use AWS Organizations to consolidate all of your AWS accounts.
    
    **References:**
    
    [https://aws.amazon.com/ram/](https://aws.amazon.com/ram/)
    
    [https://docs.aws.amazon.com/ram/latest/userguide/shareable.html](https://docs.aws.amazon.com/ram/latest/userguide/shareable.html)
    
81. ##### 22. QUESTION
    
    A company has recently migrated its microservices-based application to Amazon Elastic Kubernetes Service (Amazon EKS). As part of the migration, the company must ensure that all sensitive configuration data and credentials, such as database passwords and API keys, are stored securely and encrypted within the Amazon EKS cluster’s etcd key-value store.
    
    What is the most suitable solution to meet the company’s requirements?
    
    - Use AWS Secrets Manager with a new AWS KMS key to securely manage and store sensitive data within the EKS cluster's etcd key-value store.
    - Enable default Amazon EBS volume encryption for the account with a new AWS KMS key to ensure encryption of sensitive data within the Amazon EKS cluster.
    - Enable secret encryption with a new AWS KMS key on an existing Amazon EKS cluster to encrypt sensitive data stored in the EKS cluster's etcd key-value store.
    - Use Amazon EKS default options and the Amazon Elastic Block Store (Amazon EBS) Container Storage Interface (CSI) driver as an add-on to securely store sensitive data within the Amazon EKS cluster.
    
    Incorrect
    
    Enabling secret encryption with a new AWS Key Management Service (KMS) key in an existing Amazon Elastic Kubernetes Service (EKS) cluster is critical to securing sensitive data stored in the cluster’s etcd key-value store. Amazon EKS is a service for running and managing containerized applications, storing configuration data and secrets, etc., and is a distributed data store. By default, these secrets are not encrypted, posing potential security risks. Integrating AWS KMS with Amazon EKS allows for the encryption of these secrets, leveraging AWS KMS’s capabilities to manage cryptographic keys and control their use across AWS services and applications.
    
    ![](https://media.tutorialsdojo.com/public/Amazon-EKS-27072023.jpg)
    
    The process involves creating an AWS KMS key specifically for the EKS cluster and configuring the cluster to encrypt secrets before they are saved in etcd. This setup ensures that all sensitive information within the etcd database is encrypted at rest, enhancing data security. By adopting this approach, organizations can significantly improve their security posture, ensuring that sensitive data and credentials are protected according to industry standards and compliance requirements, thus maintaining data confidentiality and integrity within their Kubernetes environments.
    
    Hence, the correct answer is: **Enable secret encryption with a new AWS KMS key on an existing Amazon EKS cluster to encrypt sensitive data stored in the EKS cluster’s etcd key-value store.**
    
    The option that says: **Use AWS Secrets Manager with a new AWS KMS key to securely manage and store sensitive data within the EKS cluster’s etcd key-value store** is incorrect. AWS Secrets Manager is a powerful tool for managing secrets but it doesn’t directly address encrypting data within the etcd key-value store of an EKS cluster. Secrets Manager is more about managing and retrieving secrets rather than encrypting data within etcd.
    
    The option that says: **Enable default Amazon EBS volume encryption for the account with a new AWS KMS key to ensure encryption of sensitive data within the Amazon EKS cluster** is incorrect. Enabling default Amazon EBS volume encryption is a way to ensure that data at rest in EBS volumes is encrypted. However, the EBS volumes are primarily used for persistent storage of the worker nodes. They are not directly related to the storage of sensitive configuration data and credentials within the EKS cluster’s etcd key-value store.
    
    The option that says: **Use Amazon EKS default options and the Amazon Elastic Block Store (Amazon EBS) Container Storage Interface (CSI) driver as an add-on to securely store sensitive data within the Amazon EKS cluster** is incorrect. Amazon EBS CSI driver enables Amazon Elastic Block Store (EBS) volumes as persistent storage for Kubernetes applications running on the Amazon EKS. While this can provide secure persistent storage for your microservices, it does not address the specific requirement of securely storing sensitive data within the EKS cluster’s etcd key-value store.
    
    **References:**
    
    [https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)
    
    [https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)
    
    [https://docs.aws.amazon.com/eks/latest/userguide/enable-kms.html](https://docs.aws.amazon.com/eks/latest/userguide/enable-kms.html)
    
    **Check out this Amazon Elastic Kubernetes Service Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-elastic-kubernetes-service-eks/](https://tutorialsdojo.com/amazon-elastic-kubernetes-service-eks/)
    
82. ##### 25. QUESTION
    
    A travel photo-sharing website is using Amazon S3 to serve high-quality photos to visitors. After a few days, it was discovered that other travel websites are linking to and using these photos. This has resulted in financial losses for the business.
    
    What is the MOST effective method to mitigate this issue?
    
    - Use Amazon CloudFront distributions for your photos.
    - Configure your S3 bucket to remove public read access and use pre-signed URLs with expiry dates.
    - Store and privately serve the high-quality photos on Amazon WorkDocs instead.
    - Block the IP addresses of the offending websites using NACL.
    
    Incorrect
    
    In Amazon S3, all objects are private by default. Only the object owner has permission to access these objects. However, the object owner can optionally share objects with others by creating a pre-signed URL, using their own security credentials, to grant time-limited permission to download the objects.
    
    When you create a pre-signed URL for your object, you must provide your security credentials, specify a bucket name, an object key, specify the HTTP method (GET to download the object), and the expiration date and time. The pre-signed URLs are valid only for the specified duration.
    
    ![Configure your S3 bucket to remove public read access and use pre-signed URLs with expiry dates](https://media.tutorialsdojo.com/2019-02-13_00-58-48-19fb174d579477b46d422a9b264f6455.jpg)
    
    Anyone who receives the pre-signed URL can then access the object. For example, if you have a video in your bucket and both the bucket and the object are private, you can share the video with others by generating a pre-signed URL.
    
    Hence, the correct answer is: **Configure your S3 bucket to remove public read access and use pre-signed URLs with expiry dates.**
    
    The option that says: **Using Amazon CloudFront distributions for your photos** is incorrect. CloudFront is primarily a content delivery network service that speeds up the delivery of content to your customers.
    
    The option that says: **Blocking the IP addresses of the offending websites using NACL** is also incorrect. Blocking IP addresses using NACLs is not a very efficient method because a quick change in IP address would easily bypass this configuration.
    
    The option that says: **Storing and privately serving the high-quality photos on Amazon WorkDocs instead** is incorrect as WorkDocs is simply a fully managed, secure content creation, storage, and collaboration service. It is not a suitable service for storing static content. Amazon WorkDocs is more often used to easily create, edit, and share documents for collaboration and not for serving object data like Amazon S3.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html)
    
    [https://docs.aws.amazon.com/AmazonS3/latest/dev/ObjectOperations.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/ObjectOperations.html)
    
    **Check out this Amazon CloudFront Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-cloudfront/](https://tutorialsdojo.com/amazon-cloudfront/)
    
    **Comparison of AWS Services Cheat Sheets:**
    
    [https://tutorialsdojo.com/comparison-of-aws-services/](https://tutorialsdojo.com/comparison-of-aws-services/)
    
83. ##### 27. QUESTION
    
    The company that you are working for has a highly available architecture consisting of an elastic load balancer and several EC2 instances configured with auto-scaling in three Availability Zones. You want to monitor your EC2 instances based on a particular metric, which is not readily available in CloudWatch.
    
    Which of the following is a custom metric in CloudWatch which you have to manually set up?
    
    - Disk Reads activity of an EC2 instance
    - Memory Utilization of an EC2 instance
    - Network packets out of an EC2 instance
    - CPU Utilization of an EC2 instance
    
    Incorrect
    
    CloudWatch has available Amazon EC2 Metrics for you to use for monitoring. CPU Utilization identifies the processing power required to run an application upon a selected instance. Network Utilization identifies the volume of incoming and outgoing network traffic to a single instance. Disk Reads metric is used to determine the volume of the data the application reads from the hard disk of the instance. This can be used to determine the speed of the application. However, there are certain metrics that are not readily available in CloudWatch such as memory utilization, disk space utilization, and many others which can be collected by setting up a custom metric.
    
    You need to prepare a custom metric using CloudWatch Monitoring Scripts which is written in Perl. You can also install CloudWatch Agent to collect more system-level metrics from Amazon EC2 instances. Here’s the list of custom metrics that you can set up:
    
    – Memory utilization  
    – Disk swap utilization  
    – Disk space utilization  
    – Page file utilization  
    – Log collection
    
    ![](https://media.tutorialsdojo.com/2019-02-13_00-48-03-503799cd694a657201456b3add758b53.png)
    
    **CPU Utilization of an EC2 instance**, **Disk Reads activity of an EC2 instance**, and **Network packets out of an EC2 instance** are all incorrect because these metrics are readily available in CloudWatch by default.
    
    **References:** 
    
    [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html)
    
    [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html#using_put_script](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html#using_put_script)
    
    **Check out this Amazon EC2 Cheat Sheet:**
    
    **[https://tutorialsdojo.com/amazon-elastic-compute-cloud-amazon-ec2/](https://tutorialsdojo.com/amazon-elastic-compute-cloud-amazon-ec2/)**
    
    **Check out this Amazon CloudWatch Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-cloudwatch/](https://tutorialsdojo.com/amazon-cloudwatch/)
    
84. ##### 31. QUESTION
    
    A company has a web application that uses Amazon CloudFront to distribute its images, videos, and other static content stored in its Amazon S3 bucket to users around the world. The company has recently introduced a new member-only access feature for some of its high-quality media files. There is a requirement to provide access to multiple private media files only to paying subscribers without having to change the current URLs.
    
    Which of the following is the most suitable solution to implement to satisfy this requirement?
    
    - Configure your CloudFront distribution to use Field-Level Encryption to protect your private data and only allow access to members.
    - Create a Signed URL with a custom policy which only allows the members to see the private files.
    - Configure your CloudFront distribution to use Match Viewer as its Origin Protocol Policy which will automatically match the user request. This will allow access to the private content if the request is a paying member and deny it if it is not a member.
    - Use Signed Cookies to control who can access the private files in your CloudFront distribution by modifying your application to determine whether a user should have access to your content. For members, send the required `Set-Cookie` headers to the viewer which will unlock the content only to them.
    
    Incorrect
    
    Many companies that distribute content over the internet want to restrict access to documents, business data, media streams, or content that is intended for selected users, for example, users who have paid a fee. To securely serve this private content by using CloudFront, you can do the following:
    
    – Require that your users access your private content by using special CloudFront signed URLs or signed cookies.
    
    – Require that your users access your content by using CloudFront URLs, not URLs that access content directly on the origin server (for example, Amazon S3 or a private HTTP server). Requiring CloudFront URLs isn’t necessary, but we recommend it to prevent users from bypassing the restrictions that you specify in signed URLs or signed cookies.
    
    CloudFront signed URLs and signed cookies provide the same basic functionality: they allow you to control who can access your content.
    
    ![Amazon CloudFront signed URL signed Cookies](https://media.tutorialsdojo.com/amazon-cloud-front-signed-URL-signed-Cookies.png)
    
    If you want to serve private content through CloudFront and you’re trying to decide whether to use signed URLs or signed cookies, consider the following:
    
    Use **signed URLs** for the following cases:
    
    – You want to use an RTMP distribution. Signed cookies aren’t supported for RTMP distributions.
    
    – You want to restrict access to individual files, for example, an installation download for your application.
    
    – Your users are using a client (for example, a custom HTTP client) that doesn’t support cookies.
    
    Use **signed cookies** for the following cases:
    
    – You want to provide access to multiple restricted files, for example, all of the files for a video in HLS format or all of the files in the subscribers’ area of a website.
    
    – You don’t want to change your current URLs.
    
    Hence, the correct answer is: **Use Signed Cookies to control who can access the private files in your CloudFront distribution by modifying your application to determine whether a user should have access to your content. For members, send the required `Set-Cookie` headers to the viewer which will unlock the content only to them.**
    
    The option that says: **Configure your CloudFront distribution to use Match Viewer as its Origin Protocol Policy which will automatically match the user request. This will allow access to the private content if the request is a paying member and deny it if it is not a member** is incorrect because a Match Viewer is an Origin Protocol Policy that configures CloudFront to communicate with your origin using HTTP or HTTPS, depending on the protocol of the viewer request. CloudFront caches the object only once even if viewers make requests using both HTTP and HTTPS protocols.
    
    The option that says: **Create a Signed URL with a custom policy which only allows the members to see the private files** is incorrect because Signed URLs are primarily used for providing access to individual files, as shown in the above explanation. In addition, the scenario explicitly says that they don’t want to change their current URLs which is why implementing Signed Cookies is more suitable than Signed URLs.
    
    The option that says: **Configure your CloudFront distribution to use Field-Level Encryption to protect your private data and only allow access to members** is incorrect because Field-Level Encryption only allows you to securely upload user-submitted sensitive information to your web servers. It does not provide access to download multiple private files.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-choosing-signed-urls-cookies.html](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-choosing-signed-urls-cookies.html)
    
    [https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-signed-cookies.html](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-signed-cookies.html)
    
    **Check out this Amazon CloudFront Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-cloudfront/](https://tutorialsdojo.com/amazon-cloudfront/)
    
85. ##### 32. QUESTION
    
    A Docker application, which is running on an Amazon ECS cluster behind a load balancer, is heavily using Amazon DynamoDB. The application requires improved database performance by distributing the workload evenly and utilizing the provisioned throughput efficiently.
    
    Which of the following should be implemented for the DynamoDB table?
    
    - Use partition keys with low-cardinality attributes, which have a few number of distinct values for each item.
    - Reduce the number of partition keys in the DynamoDB table.
    - Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item.
    - Avoid using a composite primary key, which is composed of a partition key and a sort key.
    
    Incorrect
    
    The partition key portion of a table’s primary key determines the logical partitions in which a table’s data is stored. This in turn affects the underlying physical partitions. Provisioned I/O capacity for the table is divided evenly among these physical partitions. Therefore a partition key design that doesn’t distribute I/O requests evenly can create “hot” partitions that result in throttling and use your provisioned I/O capacity inefficiently.
    
    ![DynamoDB Adaptive Capacity](https://media.tutorialsdojo.com/public/DynamoDB-adaptive-capacity.png)
    
    The optimal usage of a table’s provisioned throughput depends not only on the workload patterns of individual items, but also on the partition-key design. This doesn’t mean that you must access all partition key values to achieve an efficient throughput level, or even that the percentage of accessed partition key values must be high. It does mean that the more distinct partition key values that your workload accesses, the more those requests will be spread across the partitioned space. In general, you will use your provisioned throughput more efficiently as the ratio of partition key values accessed to the total number of partition key values increases.
    
    Hence, the correct answer is: **Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item**.
    
    The option that says: **Reducing the number of partition keys in the DynamoDB table** is incorrect. Instead of doing this, you should actually add more to improve its performance to distribute the I/O requests evenly and not simply avoid “hot” partitions.
    
    The option that says: **Using partition keys with low-cardinality attributes, which have a few number of distinct values for each item** is incorrect because this is only the exact opposite of the correct answer. Remember that the more distinct partition key values your workload accesses, the more those requests will be spread across the partitioned space. Conversely, the less distinct partition key values, the less evenly spread it would be across the partitioned space, which effectively slows the performance.
    
    The option that says: **Avoid using a composite primary key, which is composed of a partition key and a sort key** is incorrect because as mentioned, a composite primary key will provide more partition for the table and in turn, improves the performance. Hence, it should be used and not avoided.
    
    **References:**
    
    [https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-uniform-load.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-uniform-load.html)
    
    [https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/](https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/)
    
    **Check out this Amazon DynamoDB Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-dynamodb/](https://tutorialsdojo.com/amazon-dynamodb/)
    
    **Amazon DynamoDB Overview:**
    
86. ##### 42. QUESTION
    
    An online cryptocurrency exchange platform is hosted in AWS which uses ECS Cluster and RDS in Multi-AZ Deployments configuration. The application is heavily using the RDS instance to process complex read and write database operations. To maintain the reliability, availability, and performance of your systems, you have to closely monitor how the different processes or threads on a DB instance use the CPU, including the percentage of the CPU bandwidth and total memory consumed by each process.
    
    Which of the following is the most suitable solution to properly monitor your database?
    
    - Use Amazon CloudWatch to monitor the CPU Utilization of your database.
    - Check the `CPU%` and `MEM%` metrics which are readily available in the Amazon RDS console that shows the percentage of the CPU bandwidth and total memory consumed by each database process of your RDS instance.
    - Create a script that collects and publishes custom metrics to CloudWatch, which tracks the real-time CPU Utilization of the RDS instance, and then set up a custom CloudWatch dashboard to view the metrics.
    - Enable Enhanced Monitoring in RDS.
    
    Incorrect
    
    Amazon RDS provides metrics in real time for the operating system (OS) that your DB instance runs on. You can view the metrics for your DB instance using the console, or consume the Enhanced Monitoring JSON output from CloudWatch Logs in a monitoring system of your choice. By default, Enhanced Monitoring metrics are stored in the CloudWatch Logs for 30 days. To modify the amount of time the metrics are stored in the CloudWatch Logs, change the retention for the `RDSOSMetrics` log group in the CloudWatch console.
    
    Take note that there are certain differences between CloudWatch and Enhanced Monitoring Metrics.  CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance, and Enhanced Monitoring gathers its metrics from an agent on the instance. As a result, you might find differences between the measurements, because the hypervisor layer performs a small amount of work. Hence, **enabling Enhanced Monitoring in RDS** is the correct answer in this specific scenario.
    
    The differences can be greater if your DB instances use smaller instance classes, because then there are likely more virtual machines (VMs) that are managed by the hypervisor layer on a single physical instance. Enhanced Monitoring metrics are useful when you want to see how different processes or threads on a DB instance use the CPU.
    
    ![](https://media.tutorialsdojo.com/metrics2.png)
    
    **Using Amazon CloudWatch to monitor the CPU Utilization of your database** is incorrect. Although you can use this to monitor the CPU Utilization of your database instance, it does not provide the percentage of the CPU bandwidth and total memory consumed by each database process in your RDS instance. Take note that CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance while RDS Enhanced Monitoring gathers its metrics from an agent on the instance.
    
    The option that says: **Create a script that collects and publishes custom metrics to CloudWatch, which tracks the real-time CPU Utilization of the RDS instance and then set up a custom CloudWatch dashboard to view the metrics** is incorrect. Although you can use Amazon CloudWatch Logs and CloudWatch dashboard to monitor the CPU Utilization of the database instance, using CloudWatch alone is still not enough to get the specific percentage of the CPU bandwidth and total memory consumed by each database processes. The data provided by CloudWatch is not as detailed as compared with the Enhanced Monitoring feature in RDS. Take note as well that you do not have direct access to the instances/servers of your RDS database instance, unlike with your EC2 instances where you can install a CloudWatch agent or a custom script to get CPU and memory utilization of your instance.
    
    The option that says: **Check the `CPU%` and `MEM%` metrics which are readily available in the Amazon RDS console that shows the percentage of the CPU bandwidth and total memory consumed by each database process of your RDS instance** is incorrect because the CPU% and MEM% metrics are not readily available in the Amazon RDS console, which is contrary to what is being stated in this option.
    
    **References:** 
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html#USER_Monitoring.OS.CloudWatchLogs](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html#USER_Monitoring.OS.CloudWatchLogs)
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MonitoringOverview.html#monitoring-cloudwatch](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MonitoringOverview.html#monitoring-cloudwatch)
    
    **Check out this Amazon CloudWatch Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-cloudwatch/](https://tutorialsdojo.com/amazon-cloudwatch/)
    
    **Check out this Amazon RDS Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)
    
87. ##### 44. QUESTION
    
    A company has a cloud architecture composed of Linux and Windows Amazon EC2 instances that process high volumes of financial data 24 hours a day, 7 days a week. To ensure high availability of the systems, the Solutions Architect must create a solution that enables monitoring of memory and disk utilization metrics for all instances.
    
    Which of the following is the most suitable monitoring solution to implement?
    
    - Install the Amazon CloudWatch agent to all the EC2 instances that gather the memory and disk utilization data. View the custom metrics in the CloudWatch console.
    - Use Amazon Inspector and install the Inspector agent to all EC2 instances.
    - Use the default Amazon CloudWatch configuration to EC2 instances where the memory and disk utilization metrics are already available. Install the AWS Systems Manager (SSM) Agent to all the EC2 instances.
    - Enable the Enhanced Monitoring option in EC2 and install Amazon CloudWatch agent to all the EC2 instances to be able to view the memory and disk utilization in the CloudWatch dashboard.
    
    Incorrect
    
    **Amazon CloudWatch** has available Amazon EC2 Metrics for you to use for monitoring CPU utilization, Network utilization, Disk performance, and Disk Reads/Writes. In case you need to monitor the below items, you need to prepare a custom metric using a Perl or other shell script, as there are no ready to use metrics for:
    
    1. Memory utilization
    2. Disk swap utilization
    3. Disk space utilization
    4. Page file utilization
    5. Log collection
    
    ![AEB Container CloudWatch](https://media.tutorialsdojo.com/public/aeb-container-cw.png)
    
    Take note that there is a multi-platform CloudWatch agent which can be installed on both Linux and Windows-based instances. You can use a single agent to collect both system metrics and log files from Amazon EC2 instances and on-premises servers. This agent supports both Windows Server and Linux and enables you to select the metrics to be collected, including sub-resource metrics such as per-CPU core. It is recommended that you use the new agent instead of the older monitoring scripts to collect metrics and logs.
    
    Hence, the correct answer is: **Install the Amazon CloudWatch agent to all the EC2 instances that gather the memory and disk utilization data. View the custom metrics in the CloudWatch console.**
    
    The option that says: **Use the default Amazon CloudWatch configuration to EC2 instances where the memory and disk utilization metrics are already available. Install the AWS Systems Manager (SSM) Agent to all the EC2 instances** is incorrect because, by default, CloudWatch does not automatically provide memory and disk utilization metrics of your instances. You have to set up custom CloudWatch metrics to monitor the memory, disk swap, disk space, and page file utilization of your instances.
    
    The option that says: **Enable the Enhanced Monitoring option in EC2 and install Amazon CloudWatch agent to all the EC2 instances to be able to view the memory and disk utilization in the CloudWatch dashboard** is incorrect because Enhanced Monitoring is only a feature of Amazon RDS. By default, Enhanced Monitoring metrics are stored for 30 days in the CloudWatch Logs.
    
    The option that says: **Use Amazon Inspector and install the Inspector agent to all EC2 instances** is incorrect because Amazon Inspector is primarily an automated security assessment service that helps you test the network accessibility of your Amazon EC2 instances and the security state of your applications running on the instances. It does not provide a custom metric to track the memory and disk utilization of each and every EC2 instance in your VPC.
    
    **References:**
    
    [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html)
    
    [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html#using_put_script](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html#using_put_script)
    
    **Check out this Amazon CloudWatch Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-cloudwatch/](https://tutorialsdojo.com/amazon-cloudwatch/)
    
    **CloudWatch Agent vs  SSM Agent vs Custom Daemon Scripts:**
    
    [https://tutorialsdojo.com/cloudwatch-agent-vs-ssm-agent-vs-custom-daemon-scripts/](https://tutorialsdojo.com/cloudwatch-agent-vs-ssm-agent-vs-custom-daemon-scripts/)
    
    **Comparison of AWS Services Cheat Sheets:**
    
    [https://tutorialsdojo.com/comparison-of-aws-services/](https://tutorialsdojo.com/comparison-of-aws-services/)
    
88. ##### 45. QUESTION
    
    An application that records weather data every minute is deployed in a fleet of Amazon EC2 Spot instances and uses a MySQL RDS database instance. Currently, there is only one Amazon RDS instance running in one Availability Zone. The database needs to be improved to ensure high availability by enabling synchronous data replication to another RDS instance.
    
    Which of the following performs synchronous data replication in RDS?
    
    - RDS Read Replica
    - RDS DB instance running as a Multi-AZ deployment
    - Amazon CloudFront running as a Multi-AZ deployment
    - Amazon DynamoDB Read Replica
    
    Incorrect
    
    When you create or modify your DB instance to run as a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous **standby** replica in a different Availability Zone. Updates to your DB Instance are synchronously replicated across Availability Zones to the standby in order to keep both in sync and protect your latest database updates against DB instance failure.
    
    ![Amazon RDS DB instance types comparison](https://media.tutorialsdojo.com/2019-06-07_10-00-40-e7c750751ea701ec7b91cbeeb464f364.png)
    
    Therefore, the correct answer is: **RDS DB instance running as a Multi-AZ deployment**
    
    **RDS Read Replica** is incorrect as a Read Replica primarily provides an asynchronous replication instead of synchronous.
    
    **Amazon DynamoDB Read Replica** is incorrect since it does not offer a Read Replica feature. It typically uses global tables to replicate data across multiple AWS Regions.
    
    **Amazon CloudFront running as a Multi-AZ deployment** is incorrect as it also does not have a Read Replica feature. It simply caches content at edge locations rather than replicating data in the database.
    
    **References:**
    
    [https://aws.amazon.com/rds/details/multi-az/](https://aws.amazon.com/rds/details/multi-az/)
    
    [https://aws.amazon.com/rds/features/multi-az/](https://aws.amazon.com/rds/features/multi-az/)
    
    **Check out this Amazon RDS Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)
    
89. ##### 46. QUESTION
    
    A company needs to deploy at least two Amazon EC2 instances to support the normal workloads of its application and automatically scale up to six EC2 instances to handle the peak load. The architecture must be highly available and fault-tolerant as it is processing mission-critical workloads.
    
    As a Solutions Architect, what should be done to meet this requirement?
    
    - Create an Auto Scaling group of EC2 instances and set the minimum capacity to 2 and the maximum capacity to 6. Use 2 Availability Zones and deploy 1 instance for each AZ.
    - Create an Auto Scaling group of EC2 instances and set the minimum capacity to 2 and the maximum capacity to 6. Deploy 4 instances in Availability Zone A.
    - Create an Auto Scaling group of EC2 instances and set the minimum capacity to 4 and the maximum capacity to 6. Deploy 2 instances in Availability Zone A and another 2 instances in Availability Zone B.
    - Create an Auto Scaling group of EC2 instances and set the minimum capacity to 2 and the maximum capacity to 4. Deploy 2 instances in Availability Zone A and 2 instances in Availability Zone B.
    
    Incorrect
    
    **Amazon EC2 Auto Scaling** helps ensure that you have the correct number of Amazon EC2 instances available to handle the load for your application. You create collections of EC2 instances, called Auto Scaling groups. You can specify the minimum number of instances in each Auto Scaling group, and Amazon EC2 Auto Scaling ensures that your group never goes below this size. You can also specify the maximum number of instances in each Auto Scaling group, and Amazon EC2 Auto Scaling ensures that your group never goes above this size.
    
    ![Amazon EC2 Auto Scaling](https://media.tutorialsdojo.com/public/as-basic-diagram.png)
    
    To achieve highly available and fault-tolerant architecture for your applications, you must deploy all your instances in different Availability Zones. This will help you isolate your resources if an outage occurs. Take note that to achieve fault tolerance, you need to have redundant resources in place to avoid any system degradation in the event of a server fault or an Availability Zone outage. Having a fault-tolerant architecture entails an extra cost in running additional resources than what is usually needed. This is to ensure that the mission-critical workloads are processed.
    
    Since the scenario requires at least 2 instances to handle regular traffic, you should have 2 instances running all the time even if an AZ outage occurred. You can use an Auto Scaling Group to automatically scale your compute resources across two or more Availability Zones. You have to specify the minimum capacity to 4 instances and the maximum capacity to 6 instances. If each AZ has 2 instances running, even if an AZ fails, your system will still run a minimum of 2 instances.
    
    Hence, the correct answer is: **Create an Auto Scaling group of EC2 instances and set the minimum capacity to 4 and the maximum capacity to 6. Deploy 2 instances in Availability Zone A and another 2 instances in Availability Zone B.**
    
    The option that says: **Create an Auto Scaling group of EC2 instances and set the minimum capacity to 2 and the maximum capacity to 6. Deploy 4 instances in Availability Zone A** is incorrect because the instances are only deployed in a single Availability Zone. It cannot protect your applications and data from datacenter or AZ failures.
    
    The option that says: **Create an Auto Scaling group of EC2 instances and set the minimum capacity to 2 and the maximum capacity to 6. Use 2 Availability Zones and deploy 1 instance for each AZ** is incorrect. It is required to have 2 instances running all the time. If an AZ outage happened, ASG will launch a new instance on the unaffected AZ. This provisioning does not happen instantly, which means that for a certain period of time, there will only be 1 running instance left.
    
    The option that says: **Create an Auto Scaling group of EC2 instances and set the minimum capacity to 2 and the maximum capacity to 4. Deploy 2 instances in Availability Zone A and 2 instances in Availability Zone B** is incorrect. Although this fulfills the requirement of at least 2 EC2 instances and high availability, the maximum capacity setting is wrong. It should be set to 6 to properly handle the peak load. If an AZ outage occurs and the system is at its peak load, the number of running instances in this setup will only be 4 instead of 6 and this will affect the performance of your application.
    
    **References:**
    
    [https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
    
    [https://docs.aws.amazon.com/documentdb/latest/developerguide/regions-and-azs.html](https://docs.aws.amazon.com/documentdb/latest/developerguide/regions-and-azs.html)
    
    **Check out this AWS Auto Scaling Cheat Sheet:**
    
    [https://tutorialsdojo.com/aws-auto-scaling/](https://tutorialsdojo.com/aws-auto-scaling/)
    
90. ##### 48. QUESTION
    
    A government agency plans to store confidential tax documents on AWS. Due to the sensitive information in the files, the Solutions Architect must restrict the data access requests made to the storage solution to a specific Amazon VPC only. The solution should also prevent the files from being deleted or overwritten to meet the regulatory requirement of having a write-once-read-many (WORM) storage model.
    
    Which combination of the following options should the Architect implement? (Select TWO.)
    
    - Enable Object Lock but disable Object Versioning on the new Amazon S3 bucket to comply with the write-once-read-many (WORM) storage model requirement.
    - Create a new Amazon S3 bucket with the S3 Object Lock feature enabled. Store the documents in the bucket and set the Legal Hold option for object retention.
    - Configure an Amazon S3 Access Point for the S3 bucket to restrict data access to a particular VPC only. 
    - Set up a new Amazon S3 bucket to store the tax documents and integrate it with AWS Network Firewall. Configure the Network Firewall to only accept data access requests from a specific VPC. 
    - Store the tax documents in the Amazon S3 Glacier Instant Retrieval storage class. Use the `PutBucketPolicy` API to apply a bucket policy that restricts access requests to a specific VPC.
    
    Incorrect
    
    Amazon S3 access points simplify data access for any AWS service or customer application that stores data in S3. Access points are named network endpoints that are attached to buckets that you can use to perform S3 object operations, such as `GetObject` and `PutObject`.
    
    Each access point has distinct permissions and network controls that S3 applies for any request that is made through that access point. Each access point enforces a customized access point policy that works in conjunction with the bucket policy that is attached to the underlying bucket. You can configure any access point to accept requests only from a virtual private cloud (VPC) to restrict Amazon S3 data access to a private network. You can also configure custom block public access settings for each access point.
    
    ![Amazon S3 access points](https://media.tutorialsdojo.com/amazon-s3-access-points-via-vpc.png)
    
    You can also use Amazon S3 Multi-Region Access Points to provide a global endpoint that applications can use to fulfill requests from S3 buckets located in multiple AWS Regions. You can use Multi-Region Access Points to build multi-Region applications with the same simple architecture used in a single Region, and then run those applications anywhere in the world. Instead of sending requests over the congested public internet, Multi-Region Access Points provide built-in network resilience with acceleration of internet-based requests to Amazon S3. Application requests made to a Multi-Region Access Point global endpoint use AWS Global Accelerator to automatically route over the AWS global network to the S3 bucket with the lowest network latency.
    
    With S3 Object Lock, you can store objects using a write-once-read-many (WORM) model. Object Lock can help prevent objects from being deleted or overwritten for a fixed amount of time or indefinitely. You can use Object Lock to help meet regulatory requirements that require WORM storage, or to simply add another layer of protection against object changes and deletion.
    
    ![Amazon S3 Object Lock](https://media.tutorialsdojo.com/public/td-s3-object-lock-08-14-2024.png)
    
    Before locking any objects, it is essential to enable S3 Object Lock on a bucket. Previously, Object Lock could only be enabled at the time of bucket creation, but now, Amazon S3 allows you to enable S3 Object Lock for existing buckets with just a few clicks. Once S3 Object Lock is enabled on a bucket, it allows you to lock objects within that bucket to prevent them from being deleted or overwritten for a fixed amount of time or indefinitely. While Object Lock can now be enabled on existing buckets, it is important to note that once enabled, Object Lock itself cannot be disabled. However, you can still manage and configure object lock settings, including retention periods and legal holds, but the core feature of Object Lock remains active and irreversible. Also, versioning, which is required for Object Lock, cannot be suspended or disabled once Object Lock is enabled on the bucket.
    
    Hence, the correct answers are:
    
    **– Configure an Amazon S3 Access Point for the S3 bucket to restrict data access to a particular VPC only.**
    
    **– Create a new Amazon S3 bucket with the S3 Object Lock feature enabled. Store the documents in the bucket and set the Legal Hold option for object retention.**
    
    The option that says: **Set up a new Amazon S3 bucket to store the tax documents and integrate it with AWS Network Firewall. Configure the Network Firewall to only accept data access requests from a specific VPC** is incorrect because you cannot directly use an AWS Network Firewall to restrict S3 bucket data access requests to a specific Amazon VPC only. You have to use an Amazon S3 Access Point instead for this particular use case. An AWS Network Firewall is commonly integrated to your Amazon VPC and not to an S3 bucket.
    
    The option that says: **Store the tax documents in the Amazon S3 Glacier Instant Retrieval storage class. Use the `PutBucketPolicy` API to apply a bucket policy that restricts access requests to a specific VPC** is incorrect because Amazon S3 Glacier Instant Retrieval is just an archive storage class that delivers the lowest-cost storage for long-lived data that is rarely accessed and requires retrieval in milliseconds. Additionally, using a bucket policy to restrict access from a VPC is less efficient compared to using an S3 Access Point.
    
    The option that says: **Enable Object Lock but disable Object Versioning on the new Amazon S3 bucket to comply with the write-once-read-many (WORM) storage model requirement** is incorrect. Although the Object Lock feature does provide write-once-read-many (WORM) storage, the Object Versioning feature must also be enabled in order for this to work. In fact, you cannot manually disable the Object Versioning feature if you have already selected the Object Lock option.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html)
    
    [https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html)
    
    **Check out this Amazon S3 Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-s3/](https://tutorialsdojo.com/amazon-s3/)
    
91. ##### 49. QUESTION
    
    A medical records company is planning to store sensitive clinical trial data in an Amazon S3 repository with the object-level versioning feature enabled. The Solutions Architect is tasked with ensuring that no object can be overwritten or deleted by any user in a period of one year only. To meet the strict compliance requirements, the root user of the company’s AWS account must also be restricted from making any changes to an object in the S3 bucket. 
    
    Which of the following is the most secure way of storing the data in S3? 
    
    - Enable S3 Object Lock in compliance mode with a retention period of one year.
    - Enable S3 Object Lock in governance mode with a legal hold of one year.
    - Enable S3 Object Lock in governance mode with a retention period of one year.
    - Enable S3 Object Lock in compliance mode with a legal hold of one year.
    
    Incorrect
    
    With S3 Object Lock, you can store objects using a write-once-read-many (WORM) model. Object Lock can help prevent objects from being deleted or overwritten for a fixed amount of time or indefinitely. You can use Object Lock to help meet regulatory requirements that require WORM storage or to simply add another layer of protection against object changes and deletion.
    
    Before you lock any objects, you have to enable a bucket to use S3 Object Lock. You enable Object Lock when you create a bucket. After you enable Object Lock on a bucket, you can lock objects in that bucket. When you create a bucket with Object Lock enabled, you can’t disable Object Lock or suspend versioning for that bucket.
    
    S3 Object Lock provides two retention modes:
    
    -Governance mode
    
    -Compliance mode
    
    These retention modes apply different levels of protection to your objects. You can apply either retention mode to any object version that is protected by Object Lock.
    
    ![Amazon S3 Object Lock Retention Modes](https://media.tutorialsdojo.com/Amazon-S3-Object-Lock-Retention-Modes.png)
    
    In governance mode, users can’t overwrite or delete an object version or alter its lock settings unless they have special permissions. With governance mode, you protect objects against being deleted by most users, but you can still grant some users permission to alter the retention settings or delete the object if necessary. You can also use governance mode to test retention-period settings before creating a compliance-mode retention period.
    
    In compliance mode, a protected object version can’t be overwritten or deleted by any user, including the root user in your AWS account. When an object is locked in compliance mode, its retention mode can’t be changed, and its retention period can’t be shortened. Compliance mode helps ensure that an object version can’t be overwritten or deleted for the duration of the retention period.
    
    To override or remove governance-mode retention settings, a user must have the `s3:BypassGovernanceRetention` permission and must explicitly include `x-amz-bypass-governance-retention:true` as a request header with any request that requires overriding governance mode.
    
    **Legal Hold vs. Retention Period**
    
    With Object Lock, you can also place a legal hold on an object version. Like a retention period, a legal hold prevents an object version from being overwritten or deleted. However, a legal hold doesn’t have an associated retention period and remains in effect until removed. Legal holds can be freely placed and removed by any user who has the `s3:PutObjectLegalHold` permission.
    
    Legal holds are independent from retention periods. As long as the bucket that contains the object has Object Lock enabled, you can place and remove legal holds regardless of whether the specified object version has a retention period set. Placing a legal hold on an object version doesn’t affect the retention mode or retention period for that object version.
    
    For example, suppose that you place a legal hold on an object version while the object version is also protected by a retention period. If the retention period expires, the object doesn’t lose its WORM protection. Rather, the legal hold continues to protect the object until an authorized user explicitly removes it. Similarly, if you remove a legal hold while an object version has a retention period in effect, the object version remains protected until the retention period expires.
    
    Hence, the correct answer is: **Enable S3 Object Lock in compliance mode with a retention period of one year.**
    
    The option that says: **Enable S3 Object Lock in governance mode with a retention period of one year** is incorrect because in the governance mode, users typically can’t overwrite or delete an object version or alter its lock settings unless they have special permissions or if a user has access to the root AWS user account. A better option to choose here is to use the compliance mode.
    
    The option that says: **Enable S3 Object Lock in governance mode with a legal hold of one year** is incorrect. You cannot set a time period for a legal hold. You can only do this using the “retention period” option. Take note that a legal hold will still restrict users from changing the S3 objects even after the one-year retention period has elapsed. In addition, a governance mode will allow the root user to modify your S3 objects and override any existing settings.
    
    The option that says: **Enable S3 Object Lock in compliance mode with a legal hold of one year** is incorrect. Although the choice of using the compliance mode is right, you still cannot set a one-year time period for the legal hold option. Keep in mind that the legal hold is independent of the retention period.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html)
    
    [https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-overview.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-overview.html)
    
    **Check out this Amazon S3 Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-s3/](https://tutorialsdojo.com/amazon-s3/)
    
92. ##### 50. QUESTION
    
    A popular social media website uses a Amazon CloudFront web distribution to serve static content to millions of users around the globe. Recently, the website has received a number of complaints about long login times. Additionally, there are instances where users encounter HTTP 504 errors. The manager has instructed the team to significantly reduce login time and further optimize the system.
    
    Which of the following options should be used together to set up a cost-effective solution that improves the application’s performance? (Select TWO.)
    
    - Configure your origin to add a `Cache-Control max-age` directive to your objects, and specify the longest practical value for `max-age` to increase the cache hit ratio of your CloudFront distribution.
    - Customize the content that the CloudFront web distribution delivers to your users using Lambda@Edge, which allows your AWS Lambda functions to execute the authentication process in AWS locations closer to the users.
    - Establish multiple Amazon VPCs in different AWS regions and configure a transit VPC to interconnect all of your resources. To handle the requests faster, set up AWS Lambda functions in each region with the AWS Serverless Application Model (SAM) service.
    - Deploy your application to multiple AWS regions to accommodate your users around the world. Set up a Route 53 record with latency routing policy to route incoming traffic to the region that provides the best latency to the user.
    - Implement an origin failover by creating an origin group that includes two origins. Assign one as the primary origin and the other as secondary, which enables CloudFront to automatically switch to if the primary origin encounters specific HTTP status code failure responses.
    
    Incorrect
    
    Lambda[@Edge](https://portal.tutorialsdojo.com/members/edge/) lets you run Lambda functions to customize the content that CloudFront delivers, executing the functions in AWS locations closer to the viewer. The functions run in response to CloudFront events, without provisioning or managing servers. You can use Lambda functions to change CloudFront requests and responses at the following points:
    
    – After CloudFront receives a request from a viewer (viewer request)
    
    – Before CloudFront forwards the request to the origin (origin request)
    
    – After CloudFront receives the response from the origin (origin response)
    
    – Before CloudFront forwards the response to the viewer (viewer response)
    
    ![Cloudfront Events that Trigger Lambda Functions](https://media.tutorialsdojo.com/cloudfront-events-that-trigger-lambda-functions.png)
    
    In the given scenario, you can use Lambda[@Edge](https://portal.tutorialsdojo.com/members/edge/) to allow your Lambda functions to customize the content that CloudFront delivers and to execute the authentication process in AWS locations closer to the users. In addition, you can set up an origin failover by creating an origin group with two origins with one as the primary origin and the other as the second origin which CloudFront automatically switches to when the primary origin fails. This will alleviate the occasional HTTP 504 errors that users are experiencing.
    
    Therefore, the correct answers are:
    
    **–**  **Customize the content that the CloudFront web distribution delivers to your users using Lambda[@Edge](https://portal.tutorialsdojo.com/members/edge/), which allows your AWS Lambda functions to execute the authentication process in AWS locations closer to the users.**
    
    **–**  **Implement an origin failover by creating an origin group that includes two origins. Assign one as the primary origin and the other as secondary, which enables CloudFront to automatically switch to if the primary origin encounters specific HTTP status code failure responses.**
    
    The option that says: **Establish multiple Amazon VPCs in different AWS regions and configure a transit VPC to interconnect all of your resources. To handle the requests faster, set up AWS Lambda functions in each region with the AWS Serverless Application Model (SAM) service** is incorrect because of the same reason provided above. Although setting up multiple VPCs across various regions which are just connected with a transit VPC is valid, this solution still entails higher setup and maintenance costs. A more cost-effective option would be to use Lambda[@Edge](https://portal.tutorialsdojo.com/members/edge/) instead.
    
    The option that says: **Configure your origin to add a `Cache-Control max-age` directive to your objects, and specify the longest practical value for `max-age` to increase the cache hit ratio of your CloudFront distribution** is incorrect because improving the cache hit ratio for the CloudFront distribution is irrelevant in this scenario. You can only improve your cache performance by increasing the proportion of your viewer requests that are served from CloudFront edge caches instead of going to your origin servers for content. However, take note that the problem in the scenario is the sluggish authentication process of your global users and not just the caching of the static objects.
    
    The option that says: **Deploy your application to multiple AWS regions to accommodate your users around the world. Set up a Route 53 record with latency routing policy to route incoming traffic to the region that provides the best latency to the user** is incorrect. Although this may resolve the performance issue, this solution entails a significant implementation cost since you have to deploy your application to multiple AWS regions. Remember that the scenario asks for a solution that will improve the performance of the application with **minimal cost**.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html)
    
    [https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html)
    
    **Check out these Amazon CloudFront and AWS Lambda Cheat Sheets:**
    
    [https://tutorialsdojo.com/amazon-cloudfront/](https://tutorialsdojo.com/amazon-cloudfront/)
    
    [https://tutorialsdojo.com/aws-lambda/](https://tutorialsdojo.com/aws-lambda/)
    
93. ##### 54. QUESTION
    
    A popular social network is hosted in AWS and is using a Amazon DynamoDB table as its database. There is a requirement to implement a ‘follow’ feature where users can subscribe to certain updates made by a particular user and be notified via email.
    
    Which of the following is the most suitable solution to implement to meet the requirement?
    
    - Create an AWS Lambda function that uses DynamoDB Streams Amazon Kinesis Adapter which will fetch data from the DynamoDB Streams endpoint. Set up an Amazon SNS Topic that will notify the subscribers via email when there is an update made by a particular user.
    - Set up a DAX cluster to access the source DynamoDB table. Create a new DynamoDB trigger and an AWS Lambda function. For every update made in the user data, the trigger will send data to the Lambda function which will then notify the subscribers via email using Amazon SNS. Set up a DAX cluster to access the source DynamoDB table. Create a new DynamoDB trigger and a Lambda function. For every update made in the user data, the trigger will send data to the Lambda function which will then notify the subscribers via email using SNS.
    - Using the Amazon Kinesis Client Library (KCL), write an application that leverages on DynamoDB Streams Kinesis Adapter that will fetch data from the DynamoDB Streams endpoint. When there are updates made by a particular user, notify the subscribers via email using Amazon SNS.
    - Enable DynamoDB Stream and create an AWS Lambda trigger, as well as the IAM role which contains all of the permissions that the Lambda function will need at runtime. The data from the stream record will be processed by the Lambda function which will then publish a message to Amazon SNS Topic that will notify the subscribers via email.
    
    Incorrect
    
    A **DynamoDB stream** is an ordered flow of information about changes to items in an Amazon DynamoDB table. When you enable a stream on a table, DynamoDB captures information about every modification to data items in the table.
    
    Whenever an application creates, updates, or deletes items in the table, DynamoDB Streams writes a stream record with the primary key attribute(s) of the items that were modified. A _stream record_ contains information about a data modification to a single item in a DynamoDB table. You can configure the stream so that the stream records capture additional information, such as the “before” and “after” images of modified items.
    
    Amazon DynamoDB is integrated with AWS Lambda so that you can create _triggers_—pieces of code that automatically respond to events in DynamoDB Streams. With triggers, you can build applications that react to data modifications in DynamoDB tables.
    
    If you enable DynamoDB Streams on a table, you can associate the stream ARN with a Lambda function that you write. Immediately after an item in the table is modified, a new record appears in the table’s stream. AWS Lambda polls the stream and invokes your Lambda function synchronously when it detects new stream records. The Lambda function can perform any actions you specify, such as sending a notification or initiating a workflow.
    
    ![Streams and Triggers](https://media.tutorialsdojo.com/StreamsAndTriggers.png)
    
    Hence, the correct answer is: **Enable DynamoDB Stream and create an AWS Lambda trigger, as well as the IAM role which contains all of the permissions that the Lambda function will need at runtime. The data from the stream record will be processed by the Lambda function which will then publish a message to Amazon SNS Topic that will notify the subscribers via email**.
    
    The option that says: **Using the Amazon Kinesis Client Library (KCL), write an application that leverages on DynamoDB Streams Kinesis Adapter that will fetch data from the DynamoDB Streams endpoint. When there are updates made by a particular user, notify the subscribers via email using Amazon SNS** is incorrect. Although this is a valid solution, it is missing a vital step which is to enable DynamoDB Streams. With the DynamoDB Streams Kinesis Adapter in place, you can begin developing applications via the KCL interface, with the API calls seamlessly directed at the DynamoDB Streams endpoint. Remember that the DynamoDB Stream feature is not enabled by default.
    
    The option that says: **Create an AWS Lambda function that uses DynamoDB Streams Amazon Kinesis Adapter which will fetch data from the DynamoDB Streams endpoint. Set up an Amazon SNS Topic that will notify the subscribers via email when there is an update made by a particular user** is incorrect because just like in the above, you have to manually enable DynamoDB Streams first before you can use its endpoint.
    
    The option that says: **Set up a DAX cluster to access the source DynamoDB table. Create a new DynamoDB trigger and an AWS Lambda function. For every update made in the user data, the trigger will send data to the Lambda function which will then notify the subscribers via email using Amazon SNS. Set up a DAX cluster to access the source DynamoDB table. Create a new DynamoDB trigger and a Lambda function. For every update made in the user data, the trigger will send data to the Lambda function which will then notify the subscribers via email using SNS** is incorrect because the DynamoDB Accelerator (DAX) feature is primarily used to significantly improve the in-memory read performance of your database, and not to capture the time-ordered sequence of item-level modifications. You should use DynamoDB Streams in this scenario instead.
    
    **References:**
    
    [https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)
    
    [https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.Lambda.Tutorial.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.Lambda.Tutorial.html)
    
    **Check out this Amazon DynamoDB Cheat Sheet:** 
    
    [https://tutorialsdojo.com/amazon-dynamodb/](https://tutorialsdojo.com/amazon-dynamodb/)
    
94. ##### 57. QUESTION
    
    A car dealership website hosted in Amazon EC2 stores car listings in an Amazon Aurora database managed by Amazon RDS. Once a vehicle has been sold, its data must be removed from the current listings and forwarded to a distributed processing system.
    
    Which of the following options can satisfy the given requirement?
    
    - Create an RDS event subscription and send the notifications to AWS Lambda. Configure the Lambda function to fanout the event notifications to multiple Amazon SQS queues to update the target groups.
    - Create an RDS event subscription and send the notifications to Amazon SQS. Configure the SQS queues to fan out the event notifications to multiple Amazon SNS topics. Process the data using AWS Lambda functions.
    - Create an RDS event subscription and send the notifications to Amazon SNS. Configure the SNS topic to fan out the event notifications to multiple Amazon SQS queues. Process the data using AWS Lambda functions.
    - Create a native function or a stored procedure that invokes an AWS Lambda function. Configure the Lambda function to send event notifications to an Amazon SQS queue for the processing system to consume. 
    
    Incorrect
    
    You can invoke an AWS Lambda function from an Amazon Aurora MySQL-Compatible Edition DB cluster with a native function or a stored procedure. This approach can be useful when you want to integrate your database running on Aurora MySQL with other AWS services. For example, you might want to capture data changes whenever a row in a table is modified in your database.
    
    ![Amazon Aurora Native Functions](https://media.tutorialsdojo.com/amazon-aurora-native-functions.png)
    
    In the scenario, you can trigger a Lambda function whenever a listing is deleted from the database. You can then write the logic of the function to send the listing data to an SQS queue and have different processes consume it.
    
    Hence, the correct answer is: **Create a native function or a stored procedure that invokes an AWS Lambda function. Configure the Lambda function to send event notifications to an Amazon SQS queue for the processing system to consume.**
    
    The option that says: **Create an RDS event subscription and send the notifications to Amazon SQS. Configure the SQS queues to fan out the event notifications to multiple Amazon SNS topics. Process the data using AWS Lambda functions** is incorrect because RDS event subscriptions typically notify about operational changes rather than data modifications. This method does not capture database modifications like `INSERT, DELETE,` or `UPDATE`.
    
    The option that says: **Create an RDS event subscription and send the notifications to AWS Lambda. Configure the Lambda function to fan out the event notifications to multiple Amazon SQS queues to update the processing system** is incorrect because RDS event subscriptions primarily focus on operational-level changes rather than capturing direct data modifications.
    
    The option that says: **Create an RDS event subscription and send the notifications to Amazon SNS. Configure the SNS topic to fan out the event notifications to multiple Amazon SQS queues. Process the data using AWS Lambda functions** is incorrect because RDS event subscriptions only track infrastructure-related events and not actual database changes.
    
    **References:**
    
    [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Integrating.Lambda.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Integrating.Lambda.html)
    
    [https://aws.amazon.com/blogs/database/capturing-data-changes-in-amazon-aurora-using-aws-lambda/](https://aws.amazon.com/blogs/database/capturing-data-changes-in-amazon-aurora-using-aws-lambda/)
    
    **Check out this Amazon Aurora Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-aurora/](https://tutorialsdojo.com/amazon-aurora/)
    
95. ##### 60. QUESTION
    
    A healthcare organization wants to build a system that can predict drug prescription abuse. The organization will gather real-time data from multiple sources, which include Personally Identifiable Information (PII). It’s crucial that this sensitive information is anonymized prior to landing in a NoSQL database for further processing.
    
    Which solution would meet the requirements?
    
    - Create a data lake in Amazon S3 and use it as the primary storage for patient health data. Use an S3 trigger to run an AWS Lambda function that performs anonymization. Send the anonymized data to Amazon DynamoDB.
    - Stream the data in an Amazon DynamoDB table. Enable DynamoDB Streams, and configure a function that performs anonymization on newly written items.
    - Deploy an Amazon Data Firehose stream to capture and transform the streaming data. Deliver the anonymized data to Amazon Redshift for analysis.
    - Ingest real-time data using Amazon Kinesis Data Stream. Use an AWS Lambda function to anonymize the PII, then store it in Amazon DynamoDB.
    
    Incorrect
    
    **Amazon Kinesis Data Streams (KDS)** is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources.
    
    ![How Amazon Kinesis Data Streams works](https://media.tutorialsdojo.com/product-page-diagram_Amazon-Kinesis-Data-Streams.074de94302fd60948e1ad070e425eeda73d350e7.png "How Amazon Kinesis Data Streams works")
    
    Kinesis Data Streams integrates seamlessly with AWS Lambda, which can be utilized to transform and anonymize the Personally Identifiable Information (PII) in transit prior to storage. This ensures that sensitive information is appropriately anonymized at the earliest opportunity, significantly reducing the risk of any data breaches or privacy violations. Finally, the anonymized data is stored in Amazon DynamoDB, a NoSQL database suitable for handling the processed data.
    
    Hence, the correct answer is: **Ingest real-time data using Amazon Kinesis Data Stream. Use an AWS Lambda function to anonymize the PII, then store it in Amazon DynamoDB.**
    
    The option that says: **Create a data lake in Amazon S3 and use it as the primary storage for patient health data. Use an S3 trigger to run an AWS Lambda function that performs anonymization. Send the anonymized data to Amazon DynamoDB** is incorrect. This approach only doesn’t guarantee the anonymization of data before it lands on DynamoDB. The data will first be stored in S3 and then anonymized, potentially exposing sensitive information. This violates the principle of ensuring PII is anonymized prior to storage.
    
    The option that says: **Stream the data in an Amazon DynamoDB table. Enable DynamoDB Streams, and configure a function that performs anonymization on newly written items** is incorrect. DynamoDB streams typically operate on changes to data that has already been written to the database. Therefore, the PII will be stored in DynamoDB before the anonymization function is triggered, which is a potential privacy concern.
    
    The option that says: **Deploy an Amazon Data Firehose stream to capture and transform the streaming data. Deliver the anonymized data to Amazon Redshift for analysis** is incorrect. The requirement was to store the data in a NoSQL database. Amazon Redshift is primarily a data warehousing solution built on a relational database model, not a NoSQL model, which makes this option unsuitable to meet the given requirements.
    
    **References:**
    
    [https://aws.amazon.com/kinesis/data-streams/](https://aws.amazon.com/kinesis/data-streams/)
    
    [https://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html](https://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html)
    
    **Check out this Amazon Kinesis Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-kinesis/](https://tutorialsdojo.com/amazon-kinesis/)
    
96. ##### 62. QUESTION
    
    A financial services company plans to migrate its trading application from on-premises Microsoft Windows Server to Amazon Web Services (AWS).  
    The solution must ensure high availability across multiple Availability Zones and offer low-latency access to block storage.
    
    Which of the following solutions will fulfill these requirements?
    
    - Deploy the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon Elastic File System (Amazon EFS) to provide shared storage between the instances. Configure Amazon EFS with cross-region replication to sync data across Availability Zones.
    - Deploy the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon FSx for Windows File Server for shared storage.
    - Configure the trading application on Amazon EC2 Windows instances across two Availability Zones. Use Amazon Simple Storage Service (Amazon S3) for storage and configure cross-region replication to sync data between S3 buckets in each Availability Zone.
    - Configure the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon FSx for NetApp ONTAP to create a Multi-AZ file system and access the data via iSCSI protocol.
    
    Incorrect
    
    **Amazon FSx for NetApp ONTAP** is a fully managed AWS service that provides high-performance, scalable file storage based on NetApp’s ONTAP file system. It offers versatile storage options, supporting both file (NFS, SMB) and block (iSCSI) protocols, making it compatible with Windows, Linux, and macOS environments.
    
    ![Amazon FSx for NetApp ONTAP](https://media.tutorialsdojo.com/public/td-amazon-FSx-for-NetApp-ONTAP-12-12-24.png)
    
    The Amazon FSx for NetApp ONTAP features Multi-AZ file systems designed to ensure continuous availability across AWS Availability Zones, providing high availability for your Windows Server workloads. It offers consistent sub-millisecond file operation latencies with SSD storage, essential for block storage workloads in Windows environments. FSx for NetApp ONTAP fully supports block storage protocols like iSCSI, commonly used in Windows Server settings, and it works seamlessly with the SMB protocol, ensuring compatibility with Windows Server and related applications.
    
    Moreover, FSx for NetApp ONTAP simplifies migrating from on-premises NetApp systems to AWS for users currently utilizing NetApp storage. It can scale to accommodate petabyte-scale datasets, making it suitable for large Windows Server environments.
    
    Hence, the correct answer is: **Configure the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon FSx for NetApp ONTAP to create a Multi-AZ file system and access the data via iSCSI protocol.**
    
    The option that says: **Deploy the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon FSx for Windows File Server for shared storage** is incorrect. Amazon FSx for Windows File Server only provides shared, **low-latency file storage** for Windows environments. It doesn’t support low-latency access to shared **block storage.**
    
    The option that says: **Deploy the trading application on Amazon EC2 Windows Server instances across two Availability Zones. Use Amazon Elastic File System (Amazon EFS) to provide shared storage between the instances. Configure Amazon EFS with cross-region replication to sync data across Availability Zones** is incorrect. While this option provides high availability across AZs, Amazon EFS is not optimized for Windows workloads and doesn’t offer low-latency block storage.
    
    The option that says: **Configure the trading application on Amazon EC2 Windows instances across two Availability Zones. Use Amazon Simple Storage Service (Amazon S3) for storage and configure cross-region replication to sync data between S3 buckets in each Availability Zone** is incorrect. While this option provides high availability across AZs, Amazon S3 is primarily an object storage, not block storage. It doesn’t offer the low-latency access required for a trading application.
    
    **References:**
    
    [https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/what-is-fsx-ontap.html](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/what-is-fsx-ontap.html)
    
    [https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/high-availability-AZ.html](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/high-availability-AZ.html)
    
    **Check out this Amazon FSx Cheat Sheet:**
    
    [https://tutorialsdojo.com/amazon-fsx/](https://tutorialsdojo.com/amazon-fsx/)
```


