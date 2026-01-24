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

## Threat Model: Attack Flow Diagrams

┌─────────────────────────────────────────────────────────────────┐
│         THREAT T-01: COMPROMISED IAM CREDENTIALS                │
└─────────────────────────────────────────────────────────────────┘

    ┌──────────────┐
    │  Developer   │
    │   Commits    │──────┐
    │ Credentials  │      │
    │  to GitHub   │      │
    └──────────────┘      │
                          ▼
                    ┌──────────────┐
                    │   GitHub     │
                    │  Public Repo │
                    └──────────────┘
                          │
                          │ Automated Scanner
                          ▼
                    ┌──────────────┐
                    │   Attacker   │
                    │   Finds &    │
                    │   Downloads  │
                    │  Credentials │
                    └──────────────┘
                          │
                          │ Authenticates
                          ▼
                    ┌──────────────┐
                    │  AWS Account │
                    │    Access    │
                    └──────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ List S3  │    │ Launch   │    │ Exfil    │
    │ Buckets  │    │ EC2      │    │ Data     │
    └──────────┘    └──────────┘    └──────────┘

    IMPACT: Complete account compromise
    MITIGATION: Lab 03 (Detection) + Lab 04 (Prevention)

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


##  Defense Layer Mapping Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│              SECRETSHIELD DEFENSE ARCHITECTURE                  │
└─────────────────────────────────────────────────────────────────┘

THREATS                   DEFENSE LAYERS              LABS
═══════                   ══════════════              ════

┌──────────┐             ┌──────────────┐
│   T-01   │────────────▶│  CloudTrail  │           Lab 03
│Compromised            │  (Logging)   │
│   IAM    │             └──────────────┘
└──────────┘                    │
                                │
                                ▼
                        ┌──────────────┐
        └─────────────────▶│  GuardDuty   │           Lab 03
                        │ (Detection)  │
                        └──────────────┘
                                │
                                ▼

┌──────────┐             ┌──────────────┐
│   T-01   │────────────▶│     MFA      │           Lab 04
│   T-03   │             │ (Prevention) │
└──────────┘             └──────────────┘
                                │
                                │
                                ▼
                        ┌──────────────┐
        └─────────────────▶│    Least     │           Lab 04
                        │  Privilege   │
                        └──────────────┘

┌──────────┐             ┌──────────────┐
│   T-02   │────────────▶│   Secrets    │           Lab 05
│  Secrets │             │   Manager    │
│ Exposure │             └──────────────┘
└──────────┘                    │
                                ▼
                        ┌──────────────┐
                        │   IAM Role   │           Lab 04
                        │  (EC2→SM)    │           (Created)
                        └──────────────┘           Lab 05
                                                   (Used)

┌──────────┐             ┌──────────────┐
│   T-04   │────────────▶│   Billing    │           Lab 03
│ Resource │             │    Alarms    │
│  Abuse   │             └──────────────┘
└──────────┘                    │
                                ▼
                        ┌──────────────┐
                        │  GuardDuty   │           Lab 06
                        │ Crypto Alert │
                        └──────────────┘
                                │
                                ▼
                        ┌──────────────┐
                        │   Lambda     │           Lab 07
                        │  Auto-Stop   │
                        └──────────────┘
```

## Architecture Overview

This SecretShield Defense Architecture demonstrates a comprehensive security approach with multiple layers of protection against common AWS threats.

### Threat Categories

- **T-01**: Compromised IAM credentials
- **T-02**: Secrets exposure in code/configuration
- **T-03**: Additional IAM-related threats
- **T-04**: Resource abuse and unauthorized usage

### Defense Layers

1. **Logging & Monitoring**: CloudTrail captures all API calls
2. **Detection**: GuardDuty identifies suspicious activities
3. **Prevention**: MFA and least privilege access controls
4. **Secrets Management**: AWS Secrets Manager for secure credential storage
5. **Cost Protection**: Billing alarms and automated response
6. **Automated Response**: Lambda functions for incident response

### Lab Mapping

- **Lab 03**: CloudTrail logging, GuardDuty detection, billing alarms
- **Lab 04**: MFA implementation, least privilege policies, IAM role creation
- **Lab 05**: Secrets Manager integration, IAM role usage
- **Lab 06**: GuardDuty cryptocurrency mining alerts
- **Lab 07**: Lambda auto-stop functionality

## Next Lab

In Lab 03, you will:
- Enable CloudTrail for API logging
- Deploy GuardDuty for threat detection
- Configure billing alarms
- Establish security baseline in AWS Console

➡️ [Lab 03 – AWS Account Baseline](03-aws-account-baseline.md)






