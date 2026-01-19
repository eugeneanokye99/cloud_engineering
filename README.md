# AWS Well-Architected & Cloud Adoption Framework Assessment

## Project Overview

This repository contains a structured assessment and architectural redesign of a two-tier web application migrated from on-premises infrastructure to Amazon Web Services (AWS). The goal of the project is to evaluate the workload using the **AWS Well-Architected Framework (WAF)** and assess organizational readiness using the **AWS Cloud Adoption Framework (CAF)**.

The work demonstrates the ability to think and operate as a cloud engineer or junior cloud architect by critically evaluating an existing system, identifying risks, and proposing AWS-aligned improvements.

---

## Objectives

* Apply the five pillars of the AWS Well-Architected Framework to a real-world workload
* Assess organizational readiness using the six perspectives of the AWS Cloud Adoption Framework
* Design an improved AWS architecture aligned with best practices
* Communicate architectural decisions clearly and professionally

---

## Repository Structure

```
.
├── aws_waf_caf_assessment.md   # Main report covering Tasks 1–4 and reflection
├── README.md                  # Project overview and usage instructions
├── architecture-diagram.png   # AWS architecture diagram (draw.io)
```

---

## Contents Description

### aws_waf_caf_assessment.md

This markdown report includes:

* Review of the existing on-premises architecture
* AWS Well-Architected Framework evaluation table
* Cloud Adoption Framework readiness assessment
* Improved AWS architecture description
* Reflection on lessons learned

### Architecture Diagram

The architecture diagram visually represents the improved AWS design, including:

* Multi-AZ VPC layout
* Application Load Balancer
* Auto Scaling web tier
* Amazon RDS Multi-AZ database
* Security and monitoring components

---

## Approach

1. Analyzed the existing two-tier application and identified risks
2. Evaluated the workload using the AWS Well-Architected Framework
3. Assessed organizational readiness using the AWS Cloud Adoption Framework
4. Designed a resilient, secure, and scalable AWS architecture
5. Documented findings using structured reasoning and AWS best practices

---

## Tools and Services Referenced

* Amazon EC2
* Amazon VPC
* Elastic Load Balancing
* Amazon RDS
* AWS IAM
* Amazon CloudWatch
* AWS CloudFormation
* AWS Cost Explorer

---

## How to Use This Repository

* Review `aws_waf_caf_assessment.md` for the full technical and organizational analysis
* Refer to the architecture diagram for a visual understanding of the proposed solution
* Use this repository as a reference for AWS architecture reviews, lab submissions, or learning purposes

---

## Author Notes

This project was completed as part of an AWS architecture and cloud adoption lab. It reflects industry-aligned practices and demonstrates structured architectural thinking rather than simple service deployment.
