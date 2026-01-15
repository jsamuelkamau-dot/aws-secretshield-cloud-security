# Lab 04 – IAM Identity Foundation

## Goal
Design a secure and scalable IAM identity foundation that separates human access from workload access and enforces least privilege.

This lab focuses on identity design, not application deployment.

## Identity Design Principles
The following principles guide IAM usage throughout this project:

- Avoid long-lived IAM user access keys
- Prefer IAM roles with temporary credentials
- Separate human access from application and service identities
- Grant only the permissions required to perform a task

## Identity Categories

### 1. Human Identities
Human users (administrators, operators) access AWS through:
- AWS IAM Identity Center (SSO) or
- Federated access with temporary credentials

Direct use of IAM users with access keys is avoided.

### 2. Workload Identities
Applications and services use:
- IAM roles attached to AWS services (EC2, Lambda, etc.)
- Temporary credentials provided by AWS

No application stores static credentials.

## Permission Boundaries
- Permissions are scoped narrowly using IAM policies
- Future stages may include permission boundaries or SCPs
- IAM design aims to reduce blast radius if compromised

## Security Rationale
Most cloud attacks succeed through identity misuse rather than network exploitation. Designing IAM correctly from the start significantly reduces the impact of credential compromise.

## Evidence to Capture
- Screenshot of IAM users (showing minimal usage)
- Screenshot of IAM roles created or planned
- Screenshot of IAM account summary

## Next Lab
➡️ [Lab 05 – Secrets Management Design](05-secrets-management-design.md)

---

**Status:** This project is actively in progress and will be updated as the next stages are completed.
