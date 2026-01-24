# Lab 04 – IAM Identity Foundation

## Goal

Implement least privilege IAM policies and strong identity controls to prevent unauthorized access and credential compromise.

In this lab we will:
- Enable MFA on root account
- Create IAM password policy
- Create least privilege IAM policies
- Create IAM roles for AWS services
- Set up IAM user with MFA
- Enable IAM Access Analyzer

## Prerequisites

- Lab 03 completed
- Access to AWS Management Console
- Access to root account email
- Smartphone with authenticator app (Google Authenticator, Authy, or Microsoft Authenticator)

---

## Step-by-Step (AWS Console)

### Step 1 — Verify Current Region

1. Sign in to the **AWS Management Console**
2. Verify region is set to the region used in Lab 03



---

### Step 2 — Enable MFA on Root Account

**IMPORTANT:** This is the single most important security step for your AWS account.

# MFA Authentication Flow

```
┌─────────────────────────────────────────────────────────────────┐
│              MFA AUTHENTICATION FLOW                            │
└─────────────────────────────────────────────────────────────────┘

┌──────────────┐
│    User      │
│  (Human)     │
└──────────────┘
       │
       │ 1. Enter username + password
       ▼
┌──────────────┐
│     AWS      │
│  Sign-in     │
│     Page     │
└──────────────┘
       │
       │ 2. Password validated ✓
       ▼
┌──────────────┐
│     MFA      │
│   Prompt     │◀────────────┐
└──────────────┘             │
       │                     │
       │ 3. Request code     │
       ▼                     │
┌──────────────┐             │
│    Phone     │             │
│ Authenticator│             │
│     App      │             │
└──────────────┘             │
       │                     │
       │ 4. Generate 6-digit │
       │    TOTP code        │
       │                     │
       │ 5. User enters code │
       └─────────────────────┘
       │
       │ 6. Code validated ✓
       ▼
┌──────────────┐
│  AWS Console │
│    Access    │
│   GRANTED    │
└──────────────┘
```

## Security Improvement Analysis

**BEFORE MFA**: Password compromise = full access  
**AFTER MFA**: Password + physical device required  
**Attack surface reduction**: ~80%

## Authentication Flow Steps

1. **Initial Login**: User enters username and password
2. **Password Validation**: AWS validates credentials against IAM
3. **MFA Challenge**: System prompts for second factor
4. **Code Generation**: Authenticator app generates 6-digit TOTP code
5. **Code Entry**: User inputs the time-based code
6. **Final Validation**: AWS verifies code and grants access

## MFA Benefits

### Security Enhancements
- **Two-Factor Protection**: Requires both knowledge (password) and possession (device)
- **Time-Based Codes**: TOTP codes expire every 30 seconds
- **Phishing Resistance**: Stolen passwords alone cannot grant access
- **Compliance**: Meets regulatory requirements for privileged access

### Attack Mitigation
- **Credential Stuffing**: Ineffective without second factor
- **Password Breaches**: Compromised passwords insufficient for access
- **Social Engineering**: Harder to obtain both factors simultaneously
- **Remote Attacks**: Physical device requirement adds significant barrier

1. Click your account name in the top-right corner
2. Click **Security credentials**
3. In the **Multi-factor authentication (MFA)** section, click **Assign MFA device**

**Configure MFA device:**

**Device name:**
- `root-account-mfa`

**MFA device:**
- Select **Authenticator app**

4. Click **Next**

**Set up device:**

5. Open your authenticator app on your smartphone (Google Authenticator, Authy, Microsoft Authenticator)
6. In the app, tap **Add account** or **+**
7. Scan the QR code displayed in AWS Console

**OR manually enter:**
- Account name: `AWS-Root-538784191640`
- Key: [the secret key shown below QR code]

8. Enter two consecutive MFA codes from your app:
   - **MFA code 1:** [6-digit code from app]
   - Wait 30 seconds for code to refresh
   - **MFA code 2:** [next 6-digit code from app]

9. Click **Add MFA**


## :** `lab04-screenshot02-root-mfa.png`
<img width="941" height="484" alt="lab04-screenshot02-root-mfa" src="https://github.com/user-attachments/assets/d42e0bcc-4560-473e-959c-f04684e80be0" />


**Why this matters:** Root account has unlimited permissions. MFA prevents attackers from accessing it even if they steal your password. This directly addresses Threat T-01.

**⚠️ CRITICAL:** Save your MFA device backup! If you lose your phone, you'll need to contact AWS Support to recover access.

---

### Step 3 — Create IAM Password Policy

1. In the AWS Console search bar, type: **IAM**
2. Click **IAM** from the results
3. In the left menu, click **Account settings**
4. In the **Password policy** section, click **Edit**

**Configure password policy:**

**Password minimum length:**
- `14` characters

**Password requirements:**
- ✅ Require at least one uppercase letter
- ✅ Require at least one lowercase letter
- ✅ Require at least one number
- ✅ Require at least one non-alphanumeric character

**Password expiration:**
- ✅ Enable password expiration
- Password expiration period: `90` days

**Password reuse:**
- ✅ Prevent password reuse
- Number of passwords to remember: `5`

**Additional settings:**
- ✅ Allow users to change their own password
- ✅ Require administrator reset if password expired

5. Click **Save changes**


## :** `lab04-screenshot03-password-policy.png`
<img width="949" height="488" alt="lab04-screenshot03-password-policy" src="https://github.com/user-attachments/assets/4bbdf4d4-23ce-4ca2-91e1-d8771e607100" />


**Why this matters:** Strong password policies prevent easy password guessing and brute force attacks. Addresses Threat T-01.

---

### Step 4 — Create Least Privilege Read-Only Policy

We'll create a custom policy for read-only access to specific services.

1. In IAM console, click **Policies** in the left menu
2. Click **Create policy**
3. Click the **JSON** tab
4. Delete the existing JSON
5. Copy and paste this policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadOnlySecurityServices",
            "Effect": "Allow",
            "Action": [
                "cloudtrail:LookupEvents",
                "cloudtrail:GetTrailStatus",
                "guardduty:ListFindings",
                "guardduty:GetFindings",
                "config:DescribeConfigurationRecorders",
                "config:DescribeDeliveryChannels",
                "iam:GetAccountPasswordPolicy",
                "iam:ListUsers",
                "iam:ListMFADevices",
                "s3:ListAllMyBuckets",
                "s3:GetBucketLocation",
                "cloudwatch:DescribeAlarms",
                "sns:ListTopics",
                "sns:ListSubscriptions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "ViewOwnUserInfo",
            "Effect": "Allow",
            "Action": [
                "iam:GetUser",
                "iam:ListMFADevices",
                "iam:GetLoginProfile"
            ],
            "Resource": "arn:aws:iam::538784191640:user/${aws:username}"
        },
        {
            "Sid": "ManageOwnMFA",
            "Effect": "Allow",
            "Action": [
                "iam:CreateVirtualMFADevice",
                "iam:DeleteVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:ResyncMFADevice"
            ],
            "Resource": [
                "arn:aws:iam::538784191640:user/${aws:username}",
                "arn:aws:iam::538784191640:mfa/${aws:username}"
            ]
        },
        {
            "Sid": "ManageOwnPassword",
            "Effect": "Allow",
            "Action": [
                "iam:ChangePassword"
            ],
            "Resource": "arn:aws:iam::538784191640:user/${aws:username}"
        }
    ]
}
```

6. Click **Next**

**Policy details:**

**Policy name:**
- `SecretShield-SecurityAuditor-Policy`

**Description:**
- `Read-only access to security services with self-service MFA and password management`

**Tags:**
- Key: `Project`, Value: `SecretShield`
- Key: `Lab`, Value: `04`

7. Click **Create policy**



## :** `lab04-screenshot04-readonly-policy.png`
<img width="958" height="484" alt="lab04-screenshot04-readonly-policy" src="https://github.com/user-attachments/assets/653c292b-5a83-4971-a1ce-a05b0a2b7bd0" />


**Why this matters:** This demonstrates least privilege - users can view security services but can't make changes. They can manage their own MFA and password without admin help.

---

### Step 5 — Create IAM Role for EC2 Instances

This role will allow EC2 instances to access secrets without hardcoded credentials.

1. In IAM console, click **Roles** in the left menu
2. Click **Create role**

**Select trusted entity:**
- **Trusted entity type:** AWS service
- **Use case:** EC2
- Select **EC2** (under Common use cases)

3. Click **Next**

**Add permissions:**

4. In the search box, type: `SecretsManager`
5. Check the box next to: **SecretsManagerReadWrite**

6. In the search box, type: `CloudWatch`
7. Check the box next to: **CloudWatchAgentServerPolicy**

8. Click **Next**

**Name, review, and create:**

**Role name:**
- `SecretShield-EC2-SecretsAccess-Role`

**Description:**
- `Allows EC2 instances to read secrets from AWS Secrets Manager and send logs to CloudWatch`

**Tags:**
- Key: `Project`, Value: `SecretShield`
- Key: `Lab`, Value: `04`

9. Click **Create role**


## :** `lab04-screenshot05-ec2-role.png`
<img width="942" height="491" alt="lab04-screenshot05-ec2-role" src="https://github.com/user-attachments/assets/f63ce667-579e-4b05-aa1d-64d20901c181" />


**Why this matters:** EC2 instances can now access secrets without storing credentials in code. This directly addresses Threat T-02 (Secrets Exposure). We'll use this in Lab 05.

---

### Step 6 — Create IAM User for Security Monitoring

1. In IAM console, click **Users** in the left menu
2. Click **Create user**

**User details:**

**User name:**
- `security-auditor`

**Provide user access to AWS Management Console:**
- ✅ Check this box
- Select **I want to create an IAM user**

**Console password:**
- Select **Custom password**
- Enter a strong password (14+ characters, meets policy requirements)
- Example: `SecretShield#2026!Audit`

**Users must create a new password at next sign-in:**
- ✅ Check this box (recommended)

3. Click **Next**

**Set permissions:**

**Permissions options:**
- Select **Attach policies directly**

**Search for and select:**
- Find and check: `SecretShield-SecurityAuditor-Policy` (the one we created)

4. Click **Next**

**Review and create:**

**Tags:**
- Key: `Project`, Value: `SecretShield`
- Key: `Lab`, Value: `04`
- Key: `Purpose`, Value: `SecurityMonitoring`

5. Click **Create user**

**Download credentials:**

6. Click **Download .csv file** and save it securely
   - This file contains the console sign-in URL, username, and password
   - **IMPORTANT:** Save this file in a secure location (NOT in your GitHub repo)

7. Click **Return to users list**

## :** `lab04-screenshot06-iam-user.png`
<img width="950" height="489" alt="lab04-screenshot06-iam-user png2" src="https://github.com/user-attachments/assets/88ec328c-c8a7-4819-a4e0-6e1f1914f55b" />


**Why this matters:** Demonstrates separation of duties. This user can monitor security but cannot make infrastructure changes.

---

### Step 7 — Enable MFA for IAM User

Now we'll add MFA to the security-auditor user.

1. In the **Users** list, click on **security-auditor**
2. Click the **Security credentials** tab
3. In the **Multi-factor authentication (MFA)** section, click **Assign MFA device**

**Device name:**
- `security-auditor-mfa`

**MFA device:**
- Select **Authenticator app**

4. Click **Next**

**Set up device:**

5. Open your authenticator app
6. Add a new account by scanning the QR code

**OR manually enter:**
- Account name: `AWS-SecurityAuditor-538784191640`
- Key: [the secret key shown]

7. Enter two consecutive MFA codes:
   - **MFA code 1:** [6-digit code]
   - Wait 30 seconds
   - **MFA code 2:** [next 6-digit code]

8. Click **Add MFA**


## :** `lab04-screenshot07-user-mfa.png`
<img width="947" height="487" alt="lab04-screenshot07-user-mfa" src="https://github.com/user-attachments/assets/e6e9578b-c7dd-474c-81b5-d52bc6cfea18" />


**Why this matters:** Even if the user's password is compromised, attackers cannot access the account without the MFA device.

---

### Step 8 — Test IAM User Login

Let's verify the user works correctly.

1. Open a **new incognito/private browser window**
2. Go to the sign-in URL from the CSV file
   - Format: `https://538784191640.signin.aws.amazon.com/console`

3. Enter credentials:
   - **IAM user name:** `security-auditor`
   - **Password:** [the password you set]

4. You'll be prompted to change password
5. Enter current password and set a new password
6. You'll be asked for MFA code
7. Enter the 6-digit code from your authenticator app

8. Once logged in, try to access GuardDuty:
   - Search for **GuardDuty**
   - You should be able to VIEW findings
   - Try to change a setting - you should get "Access Denied"

## :** `lab04-screenshot08-user-login-test.png`
<img width="959" height="488" alt="lab04-screenshot08-user-login-test" src="https://github.com/user-attachments/assets/a5d75caf-d73e-4a19-8094-d1971de3271b" />


9. Sign out from this window

**Why this matters:** Verifies least privilege is working - user can view but not modify.

---

### Step 9 — Enable IAM Access Analyzer

IAM Access Analyzer helps identify resources shared with external entities.

1. Return to your main AWS Console (logged in as admin/root)
2. In the search bar, type: **IAM Access Analyzer**
3. Click **Access Analyzer** from the results
4. Click **Create analyzer**

**Configure analyzer:**

**Name:**
- `SecretShield-AccessAnalyzer`

**Zone of trust:**
- **Type:** Organization
- If you don't have AWS Organizations, select **Account**
- This means: anything outside your account is flagged as external

**Tags:**
- Key: `Project`, Value: `SecretShield`
- Key: `Lab`, Value: `04`

5. Click **Create analyzer**

**Wait 5-10 minutes for initial scan**

6. After scan completes, click **Findings** in the left menu
7. Review any findings (there may be none initially)


## :** `lab04-screenshot09-access-analyzer.png`
<img width="956" height="496" alt="lab04-screenshot09-access-analyzer" src="https://github.com/user-attachments/assets/4acaf801-bf3f-4381-9499-44b7c9546133" />


**Why this matters:** Access Analyzer continuously monitors IAM policies and S3 bucket policies to identify unintended external access. Addresses Threat T-03.

---

### Step 10 — Review IAM Security Status

Let's verify all IAM security controls are in place.

1. Go to **IAM Dashboard**
2. Review the **Security recommendations** section

You should see:
- ✅ Root account MFA enabled
- ✅ IAM password policy configured
- ✅ MFA enabled for IAM users
- ⚠️ May show warnings about access keys (ignore for now)

3. Click **Credential report** in the left menu
4. Click **Download credential report**
5. Open the CSV file and review:
   - Root account: `mfa_active = true`
   - security-auditor: `mfa_active = true`, `password_enabled = true`


## :** `lab04-screenshot10-iam-dashboard.png`
<img width="953" height="492" alt="lab04-screenshot10-iam-dashboard" src="https://github.com/user-attachments/assets/cad978de-d4bf-4e8b-89fe-830c9da495ca" />


**Why this matters:** The IAM dashboard provides a quick security posture overview.

---

---

## Evidence to Captured

1. `lab04-screenshot01-region.png` - Region verification
2. `lab04-screenshot02-root-mfa.png` - Root account MFA enabled
3. `lab04-screenshot03-password-policy.png` - Password policy configured
4. `lab04-screenshot04-readonly-policy.png` - Custom policy created
5. `lab04-screenshot05-ec2-role.png` - EC2 role for secrets access
6. `lab04-screenshot06-iam-user.png` - IAM user created
7. `lab04-screenshot07-user-mfa.png` - User MFA enabled
8. `lab04-screenshot08-user-login-test.png` - User login test successful
9. `lab04-screenshot09-access-analyzer.png` - Access Analyzer active
10. `lab04-screenshot10-iam-dashboard.png` - IAM dashboard security status

---

## Cost Estimate

**Monthly costs for this lab:**

- **IAM:** Free (no charges for users, roles, or policies)
- **IAM Access Analyzer:** Free (100,000 policy analyses/month free tier)
- **MFA:** Free (using authenticator app)

**Total estimated cost:** $0.00/month 

**Note:** IAM is one of the few AWS services that is completely free!

---

## Threat Mitigation Status

Based on Lab 02 threat model:

**Threat T-01: Compromised IAM Credentials**
- ✅ Root account MFA prevents root compromise
- ✅ IAM user MFA prevents user compromise
- ✅ Strong password policy prevents brute force
- ✅ Least privilege limits damage if compromised
- Status: **Significantly mitigated** 

**Threat T-02: Secrets Exposure**
- ✅ EC2 role created (will use in Lab 05)
- Status: **Foundation ready** (full mitigation in Lab 05)

**Threat T-03: Unauthorized API Activity**
- ✅ Access Analyzer monitors external access
- ✅ Least privilege limits unauthorized actions
- ✅ CloudTrail logs all activity (from Lab 03)
- Status: **Detection and prevention active** 

**Threat T-04: Cloud Resource Abuse**
- ✅ MFA prevents unauthorized login
- Status: **Access controls strengthened**

**Progress:** Major improvement! T-01 and T-03 are now significantly mitigated! 

---

## Security Best Practices Implemented

✅ **Defense in Depth:**
- MFA (something you know + something you have)
- Least privilege policies
- Separation of duties

✅ **Assume Breach Mindset:**
- Even if password stolen, MFA blocks access
- Even if user compromised, least privilege limits damage
- Even if role assumed, CloudTrail logs it

✅ **Self-Service Security:**
- Users can manage their own MFA
- Users can change their own passwords
- No admin intervention needed for basic security tasks

---

## Next Lab

➡️ [Lab 05 – Secrets Management Design](05-secrets-management-design.md)

In Lab 05, you will:
- Create secrets in AWS Secrets Manager
- Configure automatic secret rotation
- Update application to use IAM role (the one we created in Step 5!)
- Eliminate hardcoded credentials
- Test secret retrieval from EC2


---
**Status:** This project is actively in progress and will be updated as the next stages are completed.

**Updates:** Posted on Linkedin as soon as a lab is complete www.linkedin.com/in/samuel-jesse-8047b3123
