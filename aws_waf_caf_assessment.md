# AWS Well-Architected and Cloud Adoption Framework Assessment

## Overview

This report documents the evaluation and redesign of a two-tier web application being migrated from on-premises infrastructure to Amazon Web Services (AWS). The objective is to ensure the solution aligns with AWS best practices by applying the **AWS Well-Architected Framework (WAF)** and assessing organizational readiness using the **AWS Cloud Adoption Framework (CAF)**.

The workload consists of a frontend web application and a backend relational database. The analysis identifies architectural risks, recommends improvements, and proposes an AWS-native design that is secure, reliable, scalable, and cost-effective.

---

## Task 1 – Review of the Existing Architecture

### Workload Components

* Frontend web application hosted on a single server
* Backend relational database hosted on a separate server
* Flat on-premises network
* Manual deployment, scaling, and maintenance processes

### Identified Risks and Weaknesses

* Single points of failure due to lack of redundancy
* No automated backup or disaster recovery strategy
* Limited security controls and broad network access
* Manual operational processes prone to error
* Inability to scale dynamically based on demand

---

## Task 2 – AWS Well-Architected Framework Evaluation

The workload was evaluated against the five pillars of the AWS Well-Architected Framework.

| Pillar                 | Observation                          | Improvement Area             | Recommendation                                        | Supporting AWS Service                                   |
| ---------------------- | ------------------------------------ | ---------------------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| Operational Excellence | Manual deployments and configuration | Automation and observability | Use Infrastructure as Code and centralized monitoring | AWS CloudFormation, Amazon CloudWatch                    |
| Security               | Flat network with broad access       | Defense in depth             | Isolate resources and enforce least privilege         | Amazon VPC, IAM, Security Groups                         |
| Reliability            | Single-instance architecture         | Fault tolerance and recovery | Deploy across multiple Availability Zones             | Elastic Load Balancer, Auto Scaling, Amazon RDS Multi-AZ |
| Performance Efficiency | Fixed-capacity servers               | Elastic scaling              | Scale compute based on demand                         | EC2 Auto Scaling                                         |
| Cost Optimization      | Always-on resources                  | Usage-based spending         | Monitor and right-size resources                      | AWS Cost Explorer, AWS Budgets                           |

---

## Task 3 – AWS Cloud Adoption Framework (CAF) Readiness Assessment

### 1. Business Perspective

The organization has a clear motivation to migrate to AWS, primarily to improve system reliability and scalability. However, cloud value realization is not yet fully defined. Cost transparency and return-on-investment metrics need to be established. Key actions include defining cloud success metrics, aligning migration goals with business outcomes, and implementing financial governance practices such as cost allocation and budgeting.

### 2. People Perspective

The current team has limited hands-on AWS experience. While there is strong technical knowledge of the existing system, cloud-specific skills are lacking. To address this gap, the organization should invest in AWS training, define cloud-related roles and responsibilities, and encourage certification paths for engineers and operations staff.

### 3. Governance Perspective

There are minimal formal policies governing cloud usage, security, and cost management. Establishing a governance model is critical before scaling workloads in AWS. Recommended actions include defining account structures, implementing tagging standards, and enforcing policies using AWS Organizations and Service Control Policies.

### 4. Platform Perspective

The organization does not yet have a standardized cloud platform or landing zone. Networking, identity, and shared services must be designed upfront. Creating a secure AWS landing zone with standardized VPCs, centralized logging, and shared CI/CD pipelines will enable consistent and repeatable deployments.

### 5. Security Perspective

Security practices are currently reactive rather than proactive. There is limited identity management and no centralized monitoring. The organization should adopt AWS-native security services, implement IAM best practices, enable encryption by default, and integrate security monitoring using AWS CloudTrail and Amazon GuardDuty.

### 6. Operations Perspective

Operational processes such as monitoring, incident response, and patching are manual. To operate effectively in the cloud, the organization must adopt automation-first practices. Implementing monitoring dashboards, automated backups, and runbooks will improve system resilience and reduce operational overhead.

---

## Task 4 – Improved AWS Architecture Design

### Architecture Description

The proposed AWS architecture includes:

* A Virtual Private Cloud (VPC) spanning multiple Availability Zones
* Public subnets hosting an Application Load Balancer
* Private subnets hosting EC2 instances in an Auto Scaling Group for the web tier
* Amazon RDS (Multi-AZ) for the database tier
* IAM roles enforcing least-privilege access
* Amazon CloudWatch for logging and monitoring
* AWS Backup for automated data protection
* AWS Key Management Service (KMS) for encryption of data at rest (RDS and EBS volumes)
* AWS CloudTrail for logging and auditing all API activity
* Amazon GuardDuty for continuous threat detection and security monitoring
* AWS CloudFormation to provision and manage infrastructure using Infrastructure as Code


### Alignment with the Well-Architected Framework

* **Operational Excellence**: Infrastructure is provisioned using CloudFormation, enabling consistent, repeatable deployments, while CloudWatch provides automated monitoring and alerting.
* **Security**: Network isolation is enforced using VPC design, IAM roles implement least privilege, data is encrypted using KMS, API activity is audited with CloudTrail, and GuardDuty provides continuous threat detection.
* **Reliability**: Multi-AZ deployment across compute and database tiers ensures high availability and automated failover.
* **Performance Efficiency**: Elastic Load Balancing and Auto Scaling allow the application to adapt automatically to changing demand.
* **Cost Optimization**: Managed services, Auto Scaling, and AWS cost visibility tools support a pay-as-you-go model and reduce overprovisioning.

---

## Reflection

This lab reinforced the importance of evaluating cloud solutions holistically rather than focusing solely on service selection. Applying the AWS Well-Architected Framework provided a structured way to identify risks and improvements, while the Cloud Adoption Framework highlighted the organizational changes required for successful migration. Together, these frameworks demonstrate that effective cloud adoption is both a technical and organizational transformation. The exercise strengthened architectural reasoning and improved the ability to communicate design decisions clearly and professionally.
