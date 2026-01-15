# Lab 03 – AWS Account Baseline

## Goal
Establish a secure baseline for the AWS account before deploying any services or workloads.

This lab focuses on account-level security controls that reduce blast radius and improve visibility.

## Baseline Controls Implemented

### 1. Root Account Protection
- Root account is secured with a strong password
- Multi-Factor Authentication (MFA) enabled
- Root credentials are not used for daily operations

### 2. AWS CloudTrail
- CloudTrail enabled to log all API activity
- Logs retained for security investigation and auditing
- Provides visibility into identity and API misuse

### 3. AWS Config (Awareness)
- AWS Config is acknowledged as an account governance service
- Configuration drift awareness is part of the security mindset
- (Detailed implementation may be added in future stages)

### 4. Amazon GuardDuty (Preparation)
- GuardDuty identified as the primary threat detection service
- Will be enabled and exercised in a later lab
- Used to detect credential compromise and malicious activity

## Security Rationale
Most cloud security incidents involve identity misuse rather than infrastructure exploits. Establishing logging, visibility, and root account protection significantly reduces risk and improves incident response capability.

## Evidence to Capture
- Screenshot of CloudTrail enabled
- Screenshot of root account MFA status
- Screenshot of AWS account security summary

## Next Lab
➡️ [Lab 04 – IAM Identity Foundation](04-iam-identity-foundation.md)

---

**Status:** This project is actively in progress and will be updated as the next stages are completed.
