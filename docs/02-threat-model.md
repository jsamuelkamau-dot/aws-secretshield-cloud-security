# Lab 02 – Threat Model

## Goal

Identify and document the cloud security threats this project will address.

In this lab we will:
- Document four primary cloud security threats
- Create a threat matrix with risk assessments
- Map threats to mitigation labs


---

## Step-by-Step

### Step 1 — Understand Cloud Attack Vectors

Cloud environments face four primary attack vectors:

**1. Identity and Access Attacks**
- Stolen AWS access keys
- Compromised IAM credentials
- Session token hijacking

**2. Data Exposure**
- Public S3 buckets
- Hardcoded secrets in code
- Unencrypted data

**3. Misconfiguration**
- Overly permissive security groups
- Disabled logging
- Missing encryption

**4. Resource Abuse**
- Unauthorized EC2 instances
- Cryptomining attacks
- Service quota exhaustion

---

### Step 2 — Document Threat T-01: Compromised IAM Credentials

**Threat ID:** T-01

**Threat Name:** Compromised IAM Credentials

**Category:** Identity and Access

**Attack Scenario:**

```
1. Developer commits AWS credentials to public GitHub repository
2. Automated scanner detects credentials within minutes
3. Attacker downloads credentials
4. Attacker authenticates to AWS account
5. Attacker enumerates resources (S3, EC2, RDS)
6. Attacker exfiltrates data or launches cryptomining instances
```

**Risk Assessment:**
- **Likelihood:** HIGH
- **Impact:** CRITICAL
- **Overall Risk:** 🔴 CRITICAL

**Mitigation Labs:** Lab 03, Lab 04, Lab 06

---

### Step 3 — Document Threat T-02: Secrets Exposure

**Threat ID:** T-02

**Threat Name:** Secrets Exposure and Misuse

**Category:** Data Exposure

**Attack Scenario:**

```
1. Database password hardcoded in application configuration
2. Attacker gains EC2 instance access
3. Attacker reads configuration files
4. Attacker extracts database credentials
5. Attacker connects directly to RDS database
6. Attacker exfiltrates customer data
```

**Risk Assessment:**
- **Likelihood:** MEDIUM
- **Impact:** HIGH
- **Overall Risk:** 🟠 HIGH

**Mitigation Labs:** Lab 05

---

### Step 4 — Document Threat T-03: Unauthorized API Activity

**Threat ID:** T-03

**Threat Name:** Unauthorized API Activity

**Category:** Identity and Access

**Attack Scenario:**

```
1. Attacker obtains temporary session credentials
2. Attacker calls AWS APIs for reconnaissance
3. Attacker creates backdoor IAM user
4. Attacker modifies security groups
5. Attacker establishes persistent access
6. Attacker disables CloudTrail to cover tracks
```

**Risk Assessment:**
- **Likelihood:** MEDIUM
- **Impact:** HIGH
- **Overall Risk:** 🟠 HIGH

**Mitigation Labs:** Lab 03, Lab 04, Lab 06, Lab 07

---

### Step 5 — Document Threat T-04: Cloud Resource Abuse

**Threat ID:** T-04

**Threat Name:** Cloud Resource Abuse

**Category:** Misconfiguration / Resource Abuse

**Attack Scenario:**

```
1. Attacker gains access to AWS account
2. Attacker launches maximum number of large EC2 instances
3. Instances configured for cryptocurrency mining
4. Mining operates for days before detection
5. Legitimate owner receives bill for $50,000+
6. Account flagged for abuse
```

**Risk Assessment:**
- **Likelihood:** MEDIUM
- **Impact:** MEDIUM
- **Overall Risk:** 🟡 MEDIUM

**Mitigation Labs:** Lab 03, Lab 06, Lab 07

---

### Step 6 — Create Threat Matrix


| Threat ID | Threat Name | Attack Vector | Likelihood | Impact | Risk Level | Mitigation Labs |
|-----------|-------------|---------------|------------|--------|------------|-----------------|
| T-01 | Compromised IAM Credentials | Identity & Access | HIGH | CRITICAL | 🔴 CRITICAL | Lab 03, Lab 04, Lab 06 |
| T-02 | Secrets Exposure | Data Exposure | MEDIUM | HIGH | 🟠 HIGH | Lab 05 |
| T-03 | Unauthorized API Activity | Identity & Access | MEDIUM | HIGH | 🟠 HIGH | Lab 03, Lab 04, Lab 06, Lab 07 |
| T-04 | Cloud Resource Abuse | Misconfiguration | MEDIUM | MEDIUM | 🟡 MEDIUM | Lab 03, Lab 06, Lab 07 |


---

## Next Lab

In Lab 03, you will:
- Enable CloudTrail for API logging
- Deploy GuardDuty for threat detection
- Configure billing alarms
- Establish security baseline in AWS Console

➡️ [Lab 03 – AWS Account Baseline](03-aws-account-baseline.md)





---

**Status:** This project is actively in progress and will be updated as the next stages are completed.
