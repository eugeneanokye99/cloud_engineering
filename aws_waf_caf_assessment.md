# AWS Well-Architected and Cloud Adoption Framework Assessment (Aligned to Updated Diagram)

## Overview

This report documents the evaluation and redesign of a two-tier web application being migrated from on-premises infrastructure to Amazon Web Services (AWS). The goal is to align the solution with the **AWS Well-Architected Framework (WAF)** and to assess organizational readiness with the **AWS Cloud Adoption Framework (CAF)**.

The workload consists of:

- A **frontend/web tier** (stateless application on Amazon EC2)
- A **backend relational database** (Amazon RDS with Multi-AZ standby)

The target design below is **aligned to your updated architecture diagram** (Route 53 → CloudFront → Internet Gateway → ALB → EC2 in private app subnets → RDS primary/standby, plus security/ops and governance enablement components).

---

## Task 1 – Review of the Existing Architecture (On‑prem)

### Workload Components (Current State)

- Frontend web application hosted on a single server
- Backend relational database hosted on a separate server
- Flat on-premises network
- Manual deployment, scaling, and maintenance processes

### Identified Risks and Weaknesses

- **Single points of failure** (no redundancy for web or database)
- **No automated backup/disaster recovery** strategy
- **Limited security controls** (broad network access, limited segmentation)
- **Manual operations** (deployments, patching, monitoring) increase risk of human error
- **No elastic scaling** to respond to changes in demand

---

## Task 2 – AWS Well-Architected Framework Evaluation (6 Pillars)

The workload was evaluated against the **six pillars** of the AWS Well-Architected Framework: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, and Sustainability.

### WAF Findings Summary Table (Updated to Match the Diagram)

| Pillar | Key Risk (Current State) | Diagram-aligned Improvements | Services in the Diagram |
|---|---|---|---|
| Operational Excellence | Manual deployments and limited observability | Standardize infrastructure changes with IaC and automate deployments; add metrics/logs/alarms | CloudFormation, CodePipeline/CodeDeploy, CloudWatch |
| Security | Flat network and limited auditing | Segment tiers in a VPC with private subnets; encrypt data; centralized audit logging and detection; manage secrets | KMS, CloudTrail, GuardDuty, Secrets Manager |
| Reliability | Single-instance design | Multi-AZ load balancing for the app tier + Multi-AZ database standby + backups | Application Load Balancer, Amazon RDS (Primary/Standby), AWS Backup |
| Performance Efficiency | Fixed capacity and higher latency to users | Use CDN caching to reduce origin load and improve response times | CloudFront |
| Cost Optimization | Always-on resources and limited governance | Apply tagging and budgets to control/track spend; optimize over time based on monitoring | Tagging & Budgets (governance enablement), CloudWatch |
| Sustainability | Overprovisioning and unnecessary data transfer | Reduce data movement via CDN caching; right-size and continuously improve via utilization metrics | CloudFront, CloudWatch |

### Sustainability Pillar Addendum

Sustainability improvements reflected by the diagram and operating model:

- **Reduce data movement:** Cache content at the edge with CloudFront.
- **Measure and improve:** Use CloudWatch to track utilization/traffic patterns and drive right-sizing.
- **Data hygiene:** Use AWS Backup policies and retention to avoid uncontrolled storage growth (right-size retention to business needs).

---

## Task 3 – AWS Cloud Adoption Framework (CAF) Readiness Assessment

AWS CAF organizes readiness into **six perspectives**: **Business, People, Governance, Platform, Security, Operations**.

### 1) Business Perspective
- Define measurable outcomes (availability, latency, deployment frequency, recovery targets).
- Track cost per environment/service using tagging, and report using budgets.

### 2) People Perspective
- Build role-based AWS skills (networking, IAM, RDS operations, CI/CD).
- Assign clear ownership for cloud security, platform, and operations.

### 3) Governance Perspective (Shown in the Diagram)
- Establish **tagging standards** and enforce **budgets/alerts**.
- (Optional maturity) Use **AWS Organizations** for multi-account structure and guardrails as adoption grows.

### 4) Platform Perspective (Shown in the Diagram)
- Standardize foundational builds with **Infrastructure as Code (IaC)** and **CloudFormation**.
- Reuse templates to deploy VPC, subnets, ALB, EC2, and RDS consistently across environments.

### 5) Security Perspective (Shown in the Diagram)
- Centralize audit logging (**CloudTrail**) and threat detection (**GuardDuty**).
- Encrypt sensitive data (**KMS**) and store credentials in **Secrets Manager**.
- Continue enforcing least-privilege IAM and security group-based tier isolation.

### 6) Operations Perspective (Shown in the Diagram)
- Use **CloudWatch** for metrics/logs/alarms and operational dashboards.
- Standardize backups with **AWS Backup** and periodically test restores.

---

## Task 4 – Target AWS Architecture Design (Diagram-Aligned)

### What the Updated Diagram Represents

**Edge / Ingress**
1. **User/Internet → Route 53** (DNS)
2. **Route 53 → CloudFront** (content delivery / caching)
3. **CloudFront → Internet Gateway → Application Load Balancer (ALB)**

**Network foundation**
- One **VPC spanning two Availability Zones**
- Each AZ contains:
  - **Private App Subnet** with **EC2** (web/app instances)
  - **Private DB Subnet** with **Amazon RDS** (primary in one AZ, standby in the other)

**Application tier**
- **Application Load Balancer (Multi-AZ)** distributes traffic to EC2 instances across both AZs.
- EC2 instances are placed in **private app subnets** to reduce exposure and enforce tiered access.

**Database tier**
- **Amazon RDS Primary/Standby (Multi-AZ)** for high availability and automatic failover.
- Replication between primary and standby is managed by RDS (diagram labels this linkage).

**Security / Ops integrations (shown as external supporting services)**
- **CloudWatch**: metrics, logs, alarms
- **CloudTrail**: audit logs
- **GuardDuty**: threat detection
- **KMS**: encryption keys for data at rest
- **Secrets Manager**: application/database credentials
- **AWS Backup**: backup policies and recovery

**Governance & Platform Enablement (control plane row)**
- **AWS Organizations (optional)** for maturity/scale
- **Tagging & Budgets** for cost governance
- **IaC → CloudFormation** for repeatable deployments
- **CI/CD** via **CodePipeline + CodeDeploy** to deliver changes to EC2 safely

---

## Security & Access Model (Tiered)

Recommended intent (matching the tier separation shown):

- **ALB Security Group**: inbound HTTPS (443) from the internet-facing entry point; outbound only to app tier.
- **App Security Group (EC2)**: inbound only from ALB SG (app ports); outbound only to DB and required AWS services.
- **DB Security Group (RDS)**: inbound DB port only from App SG.
- **IAM**: use roles for EC2 and CI/CD; avoid long-lived access keys.

---

## Reflection

This redesign emphasizes that successful migration is both a **technical** architecture improvement and an **organizational** capability shift. The **Well-Architected Framework** guides the technical decisions across six pillars, while **CAF** ensures governance, platform, security, and operations practices (as reflected in the diagram) are in place to run the workload reliably over time.

