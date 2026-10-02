# ☁️ AWS Cloud Engineering Project

A hands-on AWS Cloud Engineering project focused on building a secure, monitored, and reliable cloud infrastructure using Amazon S3, CloudFront, EC2, IAM, CloudWatch, SNS, and AWS CloudFormation.

This project demonstrates how cloud storage, secure access, content delivery, compute, monitoring, alerting, and security can work together as a single AWS architecture.


══════════════════════════════════════════════════════════════════════
🏗️ ARCHITECTURE
══════════════════════════════════════════════════════════════════════


                              INTERNET
                                  │
                                  │ HTTPS
                                  ▼
                        ┌───────────────────┐
                        │    CloudFront     │
                        │       CDN         │
                        │       OAC         │
                        └─────────┬─────────┘
                                  │
                                  │ Secure Access
                                  ▼
                        ┌───────────────────┐
                        │     Amazon S3     │
                        │   Private Bucket  │
                        │                   │
                        │   elitechgorup/   │
                        │    ├── Images     │
                        │    └── Files      │
                        └───────────────────┘


             ┌─────────────────────────────┐
             │          Amazon EC2         │
             │                             │
             │          IAM Role           │
             └──────────────┬──────────────┘
                            │
                            │ IAM Authorization
                            ▼
                     ┌──────────────┐
                     │  Amazon S3   │
                     └──────────────┘


             ┌─────────────────────────────┐
             │          Amazon EC2         │
             └──────────────┬──────────────┘
                            │
                            ▼
                     Amazon CloudWatch
                            │
                 ┌──────────┴──────────┐
                 │                     │
             Dashboard             CPU Alarm
                                       │
                                       ▼
                                      SNS
                                       │
                                       ▼
                               Email Notification


══════════════════════════════════════════════════════════════════════
🔐 SECURE CLOUD STORAGE
══════════════════════════════════════════════════════════════════════

Amazon S3 was configured as a private cloud storage layer.

Security Configuration:

- Block Public Access enabled
- Server-Side Encryption enabled
- Bucket Versioning enabled
- Private object storage
- IAM-based access control


Storage Structure:

S3 Bucket
│
└── elitechgorup/
    ├── Images
    └── Files


S3 Bucket:

elite-storagebucket-s79aqvmsdv5y


Prefix:

elitechgorup/


══════════════════════════════════════════════════════════════════════
📦 S3 OBJECT STORAGE
══════════════════════════════════════════════════════════════════════

Objects were uploaded into the elitechgorup/ prefix.

elitechgorup/
├── Screenshot 2026-09-25 170257.png
└── ec2-test.txt


Upload from EC2:

aws s3 ls s3://elite-storagebucket-s79aqvmsdv5y/elitechgorup/

echo "Hello from EC2" > ec2-test.txt

aws s3 cp ec2-test.txt s3://elite-storagebucket-s79aqvmsdv5y/elitechgorup/


══════════════════════════════════════════════════════════════════════
🌐 CLOUDFRONT + ORIGIN ACCESS CONTROL
══════════════════════════════════════════════════════════════════════

CloudFront was configured in front of the private S3 bucket.

User
 │
 │ HTTPS
 ▼
CloudFront
 │
 │ Origin Access Control
 ▼
Private S3


CloudFront Distribution:

https://d1ubef2h5bk1qs.cloudfront.net/elitechgorup/Screenshot%202026-09-25%20170257.png


OAC allows CloudFront to access the private S3 bucket without making
the bucket public.


══════════════════════════════════════════════════════════════════════
🖼️ SECURE CONTENT DELIVERY
══════════════════════════════════════════════════════════════════════

Browser
   │
   ▼
CloudFront
   │
   ▼
Origin Access Control
   │
   ▼
Private S3


Example Object:

/elitechgorup/Screenshot%202026-09-25%20170257.png


Direct S3 Access:

Public Internet ──X──> Private S3


CloudFront Access:

User → CloudFront → OAC → S3


The S3 bucket remains private while CloudFront delivers the content.


══════════════════════════════════════════════════════════════════════
🔑 IAM LEAST PRIVILEGE
══════════════════════════════════════════════════════════════════════

EC2 accesses S3 through an IAM Role instead of storing long-term
AWS access keys.

EC2
 │
 ▼
IAM Role
 │
 ▼
S3


Required Permissions:

s3:ListBucket
s3:GetObject
s3:PutObject


This follows the principle of least privilege.


══════════════════════════════════════════════════════════════════════
🖥️ EC2 → S3 INTEGRATION
══════════════════════════════════════════════════════════════════════

The EC2 instance was configured with an IAM Role and used to
interact with the private S3 bucket.


List Objects:

aws s3 ls s3://elite-storagebucket-s79aqvmsdv5y/elitechgorup/


Create Test File:

echo "Hello from EC2" > ec2-test.txt


Upload File:

aws s3 cp ec2-test.txt s3://elite-storagebucket-s79aqvmsdv5y/elitechgorup/


Verify:

aws s3 ls s3://elite-storagebucket-s79aqvmsdv5y/elitechgorup/


Access Flow:

EC2
 │
 ▼
IAM Role
 │
 ▼
Private S3


══════════════════════════════════════════════════════════════════════
📊 CLOUDWATCH MONITORING
══════════════════════════════════════════════════════════════════════

Amazon CloudWatch was used to monitor the EC2 instance.

Monitoring Flow:

EC2
 │
 ▼
CloudWatch
 │
 ▼
Dashboard


Metric:

CPUUtilization


CloudWatch provides visibility into EC2 infrastructure metrics.


══════════════════════════════════════════════════════════════════════
🚨 CPU UTILIZATION ALARM
══════════════════════════════════════════════════════════════════════

A CloudWatch alarm was configured for EC2 CPU utilization.


Configuration:

Metric: CPUUtilization
Statistic: Average
Threshold: 70%
Evaluation: 1 datapoint within 5 minutes


Alarm Flow:

EC2
 │
 ▼
CPUUtilization
 │
 ▼
CloudWatch Alarm
 │
 ▼
SNS
 │
 ▼
Email Notification


══════════════════════════════════════════════════════════════════════
📧 SNS EMAIL NOTIFICATION
══════════════════════════════════════════════════════════════════════

Amazon SNS was integrated with the CloudWatch alarm.

Notification Flow:

EC2
 │
 ▼
CloudWatch
 │
 ▼
CPU Alarm
 │
 ▼
SNS Topic
 │
 ▼
Email Notification


SNS provides automated email notifications when the configured
CloudWatch alarm is triggered.


══════════════════════════════════════════════════════════════════════
🛡️ CLOUD SECURITY
══════════════════════════════════════════════════════════════════════

The project implements multiple AWS security controls.

S3 Security:

- Block Public Access
- Server-Side Encryption
- Versioning
- Private bucket
- IAM-based access


CloudFront Security:

- HTTPS delivery
- Origin Access Control
- Private S3 origin


IAM Security:

- IAM Role
- Temporary credentials
- Least-privilege permissions
- No hardcoded AWS access keys


EC2 Security:

- Security Group
- Controlled network access
- IAM Role integration


Monitoring Security:

- CloudWatch monitoring
- CPU alarms
- SNS notifications


══════════════════════════════════════════════════════════════════════
🧪 SECURITY & INFRASTRUCTURE TESTING
══════════════════════════════════════════════════════════════════════

Test: EC2 → S3 using IAM Role
Result: PASSED


Test: S3 Object Upload from EC2
Result: PASSED


Test: S3 Public Access
Result: BLOCKED


Test: Direct S3 Object URL
Result: AccessDenied


Test: CloudFront → S3
Result: CONFIGURED


Test: CloudWatch CPU Monitoring
Result: CONFIGURED


Test: CPU Utilization Alarm
Result: CONFIGURED


Test: SNS Email Notification
Result: CONFIGURED


══════════════════════════════════════════════════════════════════════
🏗️ INFRASTRUCTURE AS CODE
══════════════════════════════════════════════════════════════════════

AWS CloudFormation was used to define AWS infrastructure as code.

CloudFormation
      │
      ├── S3 Bucket
      │
      ├── IAM Role
      │
      └── EC2 Instance Profile


CloudFormation Security Configuration:

- S3 encryption
- S3 versioning
- Block Public Access
- IAM Role
- IAM permissions
- EC2 Instance Profile


CloudFormation File:

cloudformation/
└── s3-infrastructure.yaml


══════════════════════════════════════════════════════════════════════
🔄 END-TO-END WORKFLOW
══════════════════════════════════════════════════════════════════════

                         USER
                           │
                           │ HTTPS
                           ▼
                     CLOUDFRONT
                           │
                           │ OAC
                           ▼
                     PRIVATE S3
                           ▲
                           │
                      IAM ROLE
                           ▲
                           │
                          EC2
                           │
                           ▼
                      CLOUDWATCH
                           │
                           ▼
                       CPU ALARM
                           │
                           ▼
                          SNS
                           │
                           ▼
                    EMAIL ALERT


Complete Workflow:

1. User requests content through CloudFront.
2. CloudFront receives the HTTPS request.
3. CloudFront uses OAC to access the private S3 origin.
4. S3 remains private.
5. EC2 accesses S3 through an IAM Role.
6. CloudWatch monitors EC2 metrics.
7. CPU Alarm monitors CPU utilization.
8. SNS sends an email notification.


══════════════════════════════════════════════════════════════════════
🔐 ACCESS & SECURITY MODEL
══════════════════════════════════════════════════════════════════════

S3 Public Access:

Public Internet ──X──> S3


EC2 Access:

EC2 → IAM Role → S3


CloudFront Access:

User → CloudFront → OAC → Private S3


The architecture keeps the S3 bucket private while allowing
authorized AWS services to access required resources.


══════════════════════════════════════════════════════════════════════
📚 KEY CLOUD CONCEPTS
══════════════════════════════════════════════════════════════════════

Amazon S3
Private object storage with encryption, versioning, and access control.


Amazon CloudFront
Content delivery through a CDN while keeping the S3 bucket private.


Origin Access Control
Secure authorization between CloudFront and a private S3 origin.


AWS IAM
Identity-based access control using roles and least-privilege permissions.


Amazon EC2
Compute environment integrated with AWS services through IAM.


Amazon CloudWatch
Infrastructure monitoring and metric-based alerting.


Amazon SNS
Notification service used to deliver CloudWatch alarm notifications.


AWS CloudFormation
Infrastructure as Code for repeatable AWS resource deployment.


══════════════════════════════════════════════════════════════════════
💡 WHAT I LEARNED
══════════════════════════════════════════════════════════════════════

Through this project, I gained practical experience with:

- AWS Cloud Architecture
- Amazon S3
- Amazon CloudFront
- Origin Access Control
- Amazon EC2
- AWS IAM
- Amazon CloudWatch
- Amazon SNS
- AWS CloudFormation
- Cloud Security
- IAM-based Authentication
- Monitoring and Alerting
- Infrastructure as Code
- AWS Troubleshooting
- Secure Cloud Storage
- CDN-based Content Delivery


══════════════════════════════════════════════════════════════════════
🚀 FUTURE IMPROVEMENTS
══════════════════════════════════════════════════════════════════════

- Multi-cloud integration with Google Cloud
- AWS CloudTrail for API auditing
- Auto Scaling
- Application Load Balancer
- Route 53 custom domain
- HTTPS certificate using ACM
- CI/CD pipeline
- Memory and disk monitoring
- Centralized logging
- Automated backup
- Disaster recovery
- Cost monitoring and optimization


══════════════════════════════════════════════════════════════════════
📁 PROJECT STRUCTURE
══════════════════════════════════════════════════════════════════════

aws-cloud-engineering-project/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── cloudformation/
│   └── s3-infrastructure.yaml
│
└── screenshots/
    ├── 01-s3-security.png
    ├── 02-s3-storage.png
    ├── 03-cloudfront-oac.png
    ├── 04-cloudfront-working.png
    ├── 05-iam-least-privilege.png
    ├── 06-ec2-s3-iam-test.png
    ├── 07-cloudwatch-dashboard.png
    ├── 08-cpu-alarm.png
    ├── 09-sns-notification.png
    └── 10-s3-access-denied.png


══════════════════════════════════════════════════════════════════════
📸 PROJECT SCREENSHOTS
══════════════════════════════════════════════════════════════════════

01. S3 Security
02. S3 Storage
03. CloudFront OAC
04. CloudFront Working
05. IAM Least Privilege
06. EC2 → S3 IAM Test
07. CloudWatch Dashboard
08. CPU Alarm
09. SNS Notification
10. S3 Access Denied


══════════════════════════════════════════════════════════════════════
👨‍💻 AUTHOR
══════════════════════════════════════════════════════════════════════

Tahir Chhipa

BCA Student | Cloud Engineering Learner


Areas of Interest:

- Cloud Engineering
- AWS
- DevOps
- Infrastructure as Code
- Cloud Security
- Automation


══════════════════════════════════════════════════════════════════════
📌 PROJECT STATUS
══════════════════════════════════════════════════════════════════════

Completed AWS Cloud Engineering Implementation


Project includes:

Cloud Storage
+
Compute
+
IAM
+
CDN
+
Monitoring
+
Alerting
+
Security
+
Infrastructure as Code


══════════════════════════════════════════════════════════════════════
☁️ AWS CLOUD ENGINEERING PROJECT
══════════════════════════════════════════════════════════════════════

Built and documented by Tahir Chhipa.
