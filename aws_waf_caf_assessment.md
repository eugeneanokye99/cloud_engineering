# AWS Well-Architected and Cloud Adoption Framework Assessment (Revised)

## Overview

This report documents the evaluation and redesign of a two-tier web application being migrated from on-premises infrastructure to Amazon Web Services (AWS). The goal is to align the solution with AWS best practices using the **AWS Well-Architected Framework (WAF)** and to assess organizational readiness with the **AWS Cloud Adoption Framework (CAF)**.

The workload consists of:

* A **frontend/web tier** (stateless application)
* A **backend relational database**

The analysis identifies architectural risks in the current state, recommends improvements, and proposes an AWS-native target design that is secure, reliable, scalable, cost-aware, and **sustainable**. The AWS Well-Architected Framework currently includes **six pillars**, including **Sustainability**. ([AWS Documentation][1])

---

## Task 1 – Review of the Existing Architecture

### Workload Components (Current State)

* Frontend web application hosted on a single server
* Backend relational database hosted on a separate server
* Flat on-premises network
* Manual deployment, scaling, and maintenance processes

### Identified Risks and Weaknesses

* **Single points of failure** (no redundancy for web or database)
* **No automated backup/disaster recovery** strategy
* **Limited security controls** (broad network access, limited segmentation)
* **Manual operations** (deployments, patching, monitoring) increase risk of human error
* **No elastic scaling** to respond to changes in demand

---

## Task 2 – AWS Well-Architected Framework Evaluation (6 Pillars)

The workload was evaluated against the **six pillars** of the AWS Well-Architected Framework: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, and Sustainability. ([AWS Documentation][1])

### WAF Findings Summary Table

| Pillar                 | Current Observation                            | Key Risk                                                            | Recommended Improvement                                                                             | Example AWS Services                                                                |
| ---------------------- | ---------------------------------------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Operational Excellence | Manual deployments/config                      | Inconsistent changes, slow recovery                                 | Adopt IaC + CI/CD + observability (metrics/logs/alarms/runbooks)                                    | CloudFormation, CodePipeline/CodeDeploy, CloudWatch, Systems Manager                |
| Security               | Flat network + broad access                    | Overexposure, weak segmentation                                     | Defense-in-depth: isolate tiers, least privilege, secrets management, edge protections              | VPC, Security Groups, IAM, KMS, Secrets Manager, AWS WAF, CloudTrail, GuardDuty     |
| Reliability            | Single-instance design                         | Downtime on failure, no failover                                    | Multi-AZ for web + DB, automated backups, define RTO/RPO                                            | ALB, Auto Scaling, RDS Multi-AZ, AWS Backup                                         |
| Performance Efficiency | Fixed-capacity servers                         | Under/over-provisioning                                             | Elastic scaling + caching and content acceleration                                                  | Auto Scaling, ALB, CloudFront, (optional) S3 for static assets                      |
| Cost Optimization      | Always-on resources                            | Wasted spend during low usage                                       | Rightsize, scale on demand, budgets + tagging governance                                            | Cost Explorer, Budgets, Compute Optimizer, Auto Scaling                             |
| Sustainability     | No impact tracking; potential overprovisioning | Higher energy use via low utilization and unnecessary data movement | Align supply/demand, reduce data transfer, choose efficient services/instances, measure and iterate | Auto Scaling, CloudFront caching, Graviton where suitable, S3 lifecycle, CloudWatch |

### Sustainability Pillar Addendum (Required)

The **Sustainability pillar** focuses on understanding and quantifying workload impacts across the lifecycle and applying design principles/best practices to reduce environmental impacts (especially energy consumption and efficiency). ([AWS Documentation][2])

**Sustainability improvements for this workload:**

* **Align capacity to demand:** Use Auto Scaling and right-sizing to avoid idle compute.
* **Reduce data movement:** Add CloudFront caching and keep data flows local where possible.
* **Prefer managed services where appropriate:** Managed services often reduce operational overhead and improve fleet efficiency.
* **Efficient compute choices:** Where compatible, consider newer generation instance families and/or Graviton-based instances for better performance per watt (validate compatibility and performance first).
* **Data lifecycle policies:** Apply retention and lifecycle rules for logs/backups/artifacts to avoid unnecessary storage growth.
* **Measure and improve:** Track utilization and request patterns (CloudWatch) and iterate.

---

## Task 3 – AWS Cloud Adoption Framework (CAF) Readiness Assessment (Aligned to the Diagram)

AWS CAF organizes readiness into **six perspectives**: **Business, People, Governance, Platform, Security, Operations**. ([Amazon Web Services, Inc.][3])

### 1) Business Perspective

**Current state:** Motivation to migrate is clear (reliability/scalability), but cloud value measurement is not formalized.

**Gaps:**

* Limited cost transparency and ROI tracking
* Success metrics not clearly defined

**Actions:**

* Define measurable outcomes (availability, deployment frequency, cost per transaction, performance targets)
* Implement chargeback/showback via tagging + cost allocation
* Establish budget guardrails and KPI reporting (monthly)

### 2) People Perspective

**Current state:** Strong on-prem experience; limited AWS hands-on expertise.

**Gaps:**

* Skills gap in cloud networking, IAM, operations, and automation

**Actions:**

* Training plan (Foundational AWS + role-based learning)
* Define cloud roles (cloud engineer, security owner, FinOps/cost owner)
* Encourage certifications and internal playbooks/runbooks

### 3) Governance Perspective

**Current state:** Minimal cloud governance policies.

**Gaps:**

* No defined account strategy, tagging standards, or policy enforcement

**Actions:**

* Define multi-account approach (at minimum: dev/test/prod separation)
* Enforce tagging standards, budgets, and baseline policies
* Use policy guardrails (e.g., AWS Organizations + SCPs) as maturity grows

### 4) Platform Perspective

**Current state:** No standardized landing zone or reusable platform.

**Gaps:**

* Networking patterns, identity baselines, and shared services not standardized

**Actions:**

* Establish a “landing zone” baseline:

  * Standard VPC pattern (public/private/db subnets across AZs)
  * Centralized logging and shared CI/CD approach
  * Reusable IaC templates for repeatable deployments

### 5) Security Perspective

**Current state:** Reactive security posture; limited centralized visibility.

**Gaps:**

* Weak identity governance, limited monitoring, unclear secrets handling

**Actions:**

* Enforce IAM least privilege with roles (avoid long-lived keys)
* Encrypt data at rest and in transit
* Centralize audit logs and add continuous threat detection
* Add edge protection for web entry points (WAF)

### 6) Operations Perspective

**Current state:** Monitoring/incident response/patching are manual.

**Gaps:**

* Limited automation, runbooks, and operational maturity

**Actions:**

* Implement dashboards/alarms, incident playbooks, and backup restore testing
* Adopt automation-first operations (patching, deployments, scaling policies)
* Use Infrastructure as Code for repeatable, low-risk changes

---

## Task 4 – Improved AWS Architecture Design (Corrected + CAF-Aligned)

### Target Architecture (What the “Corrected Diagram” Should Represent)

**Network foundation**

* One **VPC** spanning **two Availability Zones**
* **Public subnets** (per AZ): Application Load Balancer + NAT Gateway
* **Private app subnets** (per AZ): EC2 instances in Auto Scaling Group
* **Private DB subnets** (per AZ): Amazon RDS Multi-AZ

**Ingress and web delivery**

* **Route 53** for DNS
* (Recommended) **CloudFront** for caching/static acceleration
* **AWS WAF** in front of the public entry (protect from common web threats)
* **Application Load Balancer** across two AZs

**Application tier**

* **EC2 Auto Scaling Group** (private subnets, multi-AZ)
* Instance profile/role with least-privilege IAM permissions
* (Optional) Systems Manager for patching/automation

**Database tier**

* **Amazon RDS (Multi-AZ)** for high availability and automatic failover
* Encryption at rest (KMS)

**Security, monitoring, and audit**

* **CloudWatch** for metrics/logs/alarms
* **CloudTrail** for API auditing
* **GuardDuty** for threat detection
* **AWS Backup** for managed backup policies
* (Recommended) **Secrets Manager** for database credentials

**Governance/Platform enablement (CAF alignment)**

* Include a small “control plane” concept:

  * Account/budget/tagging governance (e.g., Organizations/SCPs as maturity grows)
  * CI/CD pipeline deploying via IaC

### Security & Access Model (Tiered)

* ALB Security Group: allow inbound HTTPS (443) from the internet (or CloudFront) only
* App Security Group: allow inbound only from ALB Security Group
* DB Security Group: allow inbound only from App Security Group (DB port)
* IAM: use roles for EC2; limit permissions to required services

---

## CAF-to-Diagram Alignment (Fixing the Mismatch)

Your CAF write-up implies capabilities that must be visible (at least as “shared services” boxes) in the architecture diagram.

| CAF Perspective | What the text claims                   | What must appear in the diagram to match                                           |
| --------------- | -------------------------------------- | ---------------------------------------------------------------------------------- |
| Governance      | Policies, cost controls, tagging       | “Governance/Control Plane” box: budgets/tagging + (optionally) Organizations/SCPs  |
| Platform        | Landing zone + standardized network    | VPC pattern across AZs with subnet tiers + reusable IaC (CloudFormation)           |
| Security        | Proactive monitoring, least privilege  | IAM roles, KMS, CloudTrail, GuardDuty, WAF, segmentation via subnets/SGs           |
| Operations      | Automation-first, monitoring, runbooks | CloudWatch alarms/dashboards + (optional) SSM automation + backups/restore testing |
| Business        | ROI and value realization              | Budgets/cost allocation note; tagging for reporting                                |
| People          | Upskilling, defined roles              | Short note in “Implementation Plan”/Reflection (not necessarily a diagram element) |

CAF defines six perspectives and their purpose; ensuring the diagram shows the enabling constructs keeps the narrative consistent. ([Amazon Web Services, Inc.][3])

---

## Reflection

This project reinforced that successful cloud migration is both a **technical** redesign and an **organizational** transformation. The AWS Well-Architected Framework provides a structured way to detect risks and improve design quality across six pillars, including Sustainability. ([AWS Documentation][1]) The AWS Cloud Adoption Framework complements this by highlighting readiness work across people, governance, platform foundations, security posture, and operations. ([Amazon Web Services, Inc.][3]) Together, they ensure the solution is not just “running on AWS,” but is operationally mature, well-governed, and continuously improvable.

---

## Appendix A – Lucidchart AI Prompt (AWS Icons)

Copy/paste into Lucidchart AI:

**Prompt:**

Create an AWS architecture diagram using official AWS icons. Title: “Two-tier Web App Migration – Well-Architected + CAF Aligned”.

**Layout:**

* Left-to-right flow. Show **Internet → DNS/CDN/WAF → ALB → App Tier → Database**.
* Draw one **VPC** containing **two Availability Zones (AZ-a and AZ-b)**.
* In each AZ, show three subnets: **Public Subnet**, **Private App Subnet**, **Private DB Subnet**.

**Ingress:**

1. User/Internet icon → **Amazon Route 53**
2. Route 53 → **Amazon CloudFront**
3. CloudFront → **AWS WAF**
4. WAF → **Application Load Balancer** (ALB spans both public subnets)

**App Tier (Private App Subnets):**
5. ALB → **EC2 Auto Scaling Group** with at least 2 EC2 instances split across AZ-a and AZ-b
6. Add labels:

* “ALB SG: inbound 443 from Internet/CloudFront”
* “App SG: inbound 80/443 from ALB SG only”

**Database Tier (Private DB Subnets):**
7. EC2 instances → **Amazon RDS Multi-AZ** (primary in AZ-a, standby in AZ-b)
8. Add label: “DB SG: inbound DB port from App SG only”

**Outbound:**
9. Place a **NAT Gateway** in each public subnet; private subnets route outbound through NAT in the same AZ.

**Ops & Security (outside VPC on the right, dotted-line integrations):**
10. **Amazon CloudWatch** connected to ALB, EC2, and RDS (metrics/logs/alarms)
11. **AWS CloudTrail** connected to the account/VPC (audit)
12. **Amazon GuardDuty** connected to CloudTrail/VPC (threat detection)
13. **AWS KMS** connected to RDS and EC2/EBS (encryption at rest)
14. **AWS Backup** connected to RDS (automated backups)
15. (Recommended) **AWS Secrets Manager** connected to application and RDS credentials

**CAF Alignment Overlay (top row “Control Plane”):**
16. Add a small box labeled “Governance & Platform Enablement” containing:

* **AWS Organizations (optional maturity)** + “Tagging & Budgets”
* **AWS CloudFormation** (IaC)
* **CI/CD: CodePipeline → CodeDeploy → EC2 Auto Scaling Group**

**Annotations:**

* Mark “Multi-AZ” clearly for ALB, ASG, and RDS.
* Add a small note: “Sustainability: autoscaling + caching + right-sizing + lifecycle policies”.

---

[1]: https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html?utm_source=chatgpt.com "The pillars of the framework - AWS Well-Architected Framework"
[2]: https://docs.aws.amazon.com/wellarchitected/latest/framework/sustainability.html?utm_source=chatgpt.com "Sustainability - AWS Well-Architected Framework"
[3]: https://aws.amazon.com/cloud-adoption-framework/?utm_source=chatgpt.com "AWS Cloud Adoption Framework"
