# Lab 02 – Threat Model

## Goal
Identify and document the most relevant cloud security threats this project is designed to address.

This lab does not deploy AWS resources.

## Threat Landscape (Cloud-Focused)
Modern cloud environments are commonly compromised through identity and credential-related attacks rather than infrastructure exploits.

This project focuses on realistic and frequently observed cloud attack scenarios.

## Primary Threats Considered

### 1. Compromised IAM Credentials
Attackers obtain access keys or credentials through phishing, leaked repositories, or insecure CI/CD pipelines, then use them to perform unauthorized actions.

### 2. Secrets Exposure and Misuse
Secrets such as API keys or database credentials are accidentally committed to repositories or exposed in configuration files, enabling lateral movement or data access.

### 3. Unauthorized API Activity
Stolen credentials are used to invoke AWS APIs to create, modify, or delete resources without authorization.

### 4. Abuse of Cloud Resources
Compromised identities are used to launch compute resources for malicious purposes, such as cryptomining.

## Defensive Focus Areas
Based on these threats, this project will emphasize:

- Strong IAM foundations and least privilege
- Secure secrets management practices
- Continuous threat detection using Amazon GuardDuty
- Logging and monitoring with CloudTrail and CloudWatch
- Automated incident response and containment



## Next Lab
➡️ [Lab 03 – AWS Account Baseline](03-aws-account-baseline.md)

---

**Status:** This project is actively in progress and will be updated as the next stages are completed.
