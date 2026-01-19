# Lab 03 – AWS Account Baseline

## Goal

Establish a secure baseline for the AWS account before deploying workloads by enabling essential visibility and detection controls.

In this lab we will:
- Enable AWS CloudTrail for account-wide API logging
- Deploy Amazon GuardDuty for intelligent threat detection
- Configure billing alarms to detect unusual spending
- Enable AWS Config for compliance monitoring
- Create SNS topic for security notifications

## Prerequisites

- Lab 02 completed
- Access to AWS Management Console
- Ability to create resources in your AWS account
- Credit card on file for AWS account (Free Tier eligible)

---

## Step-by-Step (AWS Console)

### Step 1 — Select AWS Region

1. Sign in to the **AWS Management Console**
2. In the top-right corner, click the **Region** selector
3. Select: **Your Region**

## `lab03-screenshot01-region.png`
<img width="958" height="496" alt="lab03-screenshot01-region" src="https://github.com/user-attachments/assets/674387ab-de55-4f50-b243-54144da3dff1" />


**Why this matters:** All resources in this lab will be created in one region. CloudTrail will log events across all regions, but the trail itself lives in one region.

---

### Step 2 — Create S3 Bucket for CloudTrail Logs

1. In the AWS Console search bar, type: **S3**
2. Click **S3** from the results
3. Click **Create bucket**

**Configure bucket:**

**Bucket name:**
- `secretshield-cloudtrail-logs-[YOUR-ACCOUNT-ID]`
- Replace `[YOUR-ACCOUNT-ID]` with your 12-digit AWS account ID
- Example: `secretshield-cloudtrail-logs-123456789012`

**Region:**
- Select **Asia Pacific (Sydney) ap-southeast-2**

**Block Public Access settings:**
- Keep **Block all public access** ENABLED (checked)

**Bucket Versioning:**
- Select **Enable**

**Default encryption:**
- Select **Server-side encryption with Amazon S3 managed keys (SSE-S3)**

**Object Lock:**
- Leave DISABLED

4. Click **Create bucket**

## `lab03-screenshot02-s3-bucket.png`

<img width="949" height="494" alt="lab03-screenshot02-s3-bucket" src="https://github.com/user-attachments/assets/189693db-f0a0-4581-a260-becc44be2deb" />


**Why this matters:** CloudTrail needs a secure S3 bucket to store all API activity logs. Versioning protects against accidental deletion.

---

### Step 3 — Enable AWS CloudTrail

1. In the AWS Console search bar, type: **CloudTrail**
2. Click **CloudTrail** from the results
3. In the left menu, click **Trails**
4. Click **Create trail**

**Configure trail:**

**Trail name:**
- `secretshield-trail`

**Storage location:**
- Select **Use existing S3 bucket**
- Click **Browse**
- Select your bucket: `secretshield-cloudtrail-logs-[YOUR-ACCOUNT-ID]`

**Log file SSE-KMS encryption:**
- Leave DISABLED (we'll use SSE-S3 from bucket settings)

**Log file validation:**
- Select **Enabled** (this is important for integrity)

**SNS notification delivery:**
- Leave DISABLED for now

**CloudWatch Logs:**
- Leave DISABLED for now

**Tags:**
- Key: `Project`, Value: `SecretShield`
- Key: `Lab`, Value: `03`

5. Click **Next**

**Choose log events:**

**Event type:**
- Select **Management events**
- Select **Data events** (optional, leave unchecked to save costs)
- Select **Insights events** (leave unchecked for now)

**Management events:**
- API activity: **Read** and **Write** (both checked)
- Exclude AWS KMS events: **Unchecked** (we want to log these)
- Exclude Amazon RDS Data API events: **Unchecked**

**Apply trail to:**
- Select **All regions** (IMPORTANT - this logs activity in all regions)

6. Click **Next**
7. Review settings
8. Click **Create trail**

## `lab03-screenshot03-cloudtrail.png`
<img width="953" height="493" alt="lab03-screenshot03-cloudtrail" src="https://github.com/user-attachments/assets/26154884-79bc-4bdf-b4df-c06860368520" />


**Why this matters:** CloudTrail logs every API call in your AWS account - who did what, when, and from where. This is essential for security investigations and addresses Threat T-03.

---

### Step 4 — Verify CloudTrail is Logging

1. Stay in **CloudTrail** console
2. Click **Event history** in the left menu
3. You should see recent events (API calls you just made)
4. Click on any event to see details


## `lab03-screenshot04-cloudtrail-events.png`
<img width="956" height="495" alt="lab03-screenshot04-cloudtrail-events" src="https://github.com/user-attachments/assets/c31a7587-a9a1-494c-8167-1d3127b98f3e" />


**Why this matters:** This confirms CloudTrail is actively logging. Every action in AWS is now being recorded.

---

### Step 5 — Enable Amazon GuardDuty

1. In the AWS Console search bar, type: **GuardDuty**
2. Click **GuardDuty** from the results
3. Click **Get Started**
4. Review the service permissions
5. Click **Enable GuardDuty**

**Note:** GuardDuty will start analyzing:
- VPC Flow Logs
- DNS logs
- CloudTrail event logs
- S3 data events (if enabled)

 ## `lab03-screenshot05-guardduty.png`
 <img width="944" height="498" alt="lab03-screenshot05-guardduty" src="https://github.com/user-attachments/assets/4129dfd7-a4aa-443f-a293-356b4825a4b3" />


**Why this matters:** GuardDuty uses machine learning to detect threats like compromised instances, reconnaissance, and cryptocurrency mining (addresses Threats T-01, T-03, T-04).

---

### Step 6 — Create SNS Topic for Security Alerts

1. In the AWS Console search bar, type: **SNS**
2. Click **Simple Notification Service** from the results
3. In the left menu, click **Topics**
4. Click **Create topic**

**Configure topic:**

**Type:**
- Select **Standard**

**Name:**
- `secretshield-security-alerts`

**Display name:**
- `SecretShield Alerts`

**Tags:**
- Key: `Project`, Value: `SecretShield`
- Key: `Lab`, Value: `03`

5. Click **Create topic**

**Copy the Topic ARN** (you'll need this later)

Example: `arn:aws:sns:ap-southeast-2:123456789012:secretshield-security-alerts`


## `lab03-screenshot06-sns-topic.png`
<img width="955" height="496" alt="lab03-screenshot06-sns-topic" src="https://github.com/user-attachments/assets/6190560d-95ca-4b25-a0a1-1042c0616ff2" />


---

### Step 7 — Create Email Subscription for SNS Topic

1. Stay in the SNS topic details page
2. Click **Create subscription**

**Configure subscription:**

**Protocol:**
- Select **Email**

**Endpoint:**
- Enter your email address (use one you check regularly)

3. Click **Create subscription**

**Status will show:** "Pending confirmation"

4. Check your email inbox
5. Open the email from **AWS Notifications**
6. Click **Confirm subscription**

## `lab03-screenshot07-sns-subscription.png`
<img width="956" height="491" alt="lab03-screenshot07-sns-subscription" src="https://github.com/user-attachments/assets/64bba57d-b330-4eb3-b14f-0560b2a10788" />


**Why this matters:** You'll receive email alerts when GuardDuty detects threats or when billing exceeds thresholds.

---

### Step 8 — Create Billing Alarm

1. In the AWS Console search bar, type: **CloudWatch**
2. Click **CloudWatch** from the results
3. In the left menu, click **Alarms**
4. Click **Create alarm**
5. Click **Select metric**

**Select metric:**

6. Click **Billing** (if you don't see Billing, see note below)
7. Click **Total Estimated Charge**
8. Check the box next to **EstimatedCharges** (USD)
9. Click **Select metric**

**Note:** If you don't see Billing metrics:
- Go to **Billing Dashboard** → **Billing Preferences**
- Enable **Receive Billing Alerts**
- Wait 15 minutes and try again
- **Change region to US East (N. Virginia) us-east-1** - Billing metrics only appear in this region

**Specify metric and conditions:**

**Metric name:** EstimatedCharges

**Statistic:** Maximum

**Period:** 6 hours

**Threshold type:** Static

**Whenever EstimatedCharges is:**
- Select **Greater**
- **than...** enter: `10`

This means: Alert when estimated monthly charges exceed $10 USD.

10. Click **Next**

**Configure actions:**

**Alarm state trigger:**
- Select **In alarm**

**Select an SNS topic:**
- Select **Select an existing SNS topic**
- **Send notification to:** `secretshield-security-alerts`

11. Click **Next**

**Add name and description:**

**Alarm name:**
- `secretshield-billing-alert-10usd`

**Alarm description:**
- `Alert when monthly AWS charges exceed $10 USD`

12. Click **Next**
13. Review settings
14. Click **Create alarm**

## `lab03-screenshot08-billing-alarm.png`
<img width="958" height="498" alt="lab03-screenshot08-billing-alarm" src="https://github.com/user-attachments/assets/a57505da-485f-4bf0-b166-e5f07381610f" />


**Why this matters:** Detects unauthorized resource usage from compromised credentials (addresses Threat T-04). You'll know immediately if someone launches expensive instances.

---

### Step 9 — Enable AWS Config

1. In the AWS Console search bar, type: **Config**
2. Click **AWS Config** from the results
3. Click **Get started** (or **Settings** if already partially configured)

**Configure AWS Config:**

**Resource types to record:**
- Select **Record all resources supported in this region**
- Include global resources: **Checked** (important for IAM)

**Amazon S3 bucket:**
- Select **Create a bucket**
- Bucket name will be auto-generated: `config-bucket-[account-id]`

**Amazon SNS topic:**
- Select **Stream configuration changes and notifications to an Amazon SNS topic**
- Select **Create a topic**
- Topic name: `config-topic`

**AWS Config role:**
- Select **Create AWS Config service-linked role**

4. Click **Next**

**AWS Config rules:**
- Skip this for now (click **Next** without selecting rules)
- We'll add rules in a future lab

5. Click **Confirm**

**Note:** It may take a few minutes for Config to start recording.

## `lab03-screenshot09-config.png`
<img width="950" height="499" alt="lab03-screenshot09-config" src="https://github.com/user-attachments/assets/bbd7bfcc-fbc4-4423-b512-cd6610e60c34" />


**Why this matters:** AWS Config continuously monitors your resource configurations and tracks changes. Essential for compliance and detecting misconfigurations.

---

---



---

## Evidence Captured

1. `lab03-screenshot01-region.png` - Region set to Sydney
2. `lab03-screenshot02-s3-bucket.png` - S3 bucket created
3. `lab03-screenshot03-cloudtrail.png` - CloudTrail trail logging
4. `lab03-screenshot04-cloudtrail-events.png` - Event history
5. `lab03-screenshot05-guardduty.png` - GuardDuty enabled
6. `lab03-screenshot06-sns-topic.png` - SNS topic created
7. `lab03-screenshot07-sns-subscription.png` - Subscription confirmed
8. `lab03-screenshot08-billing-alarm.png` - CloudWatch billing alarm
9. `lab03-screenshot09-config.png` - AWS Config recording


---

## Cost Estimate

**Monthly costs for this lab:**

- **CloudTrail:** First trail is free; $2.00/month for log storage (minimal logs)
- **GuardDuty:** 30-day free trial; then ~$4-5/month for small account
- **SNS:** First 1,000 emails free; $0.50/month after
- **CloudWatch:** First 10 alarms free; this is within free tier
- **AWS Config:** ~$2-3/month for basic recording
- **S3:** ~$0.50/month for log storage

**Total estimated cost:** $0.00/month (during free trials), then ~$8-10/month

**Note:** These are foundational security services. The cost is minimal compared to the security value.

---

## Threat Mitigation Status

Based on Lab 02 threat model, Lab 03 provides:

**Threat T-01: Compromised IAM Credentials**
- CloudTrail logs all API activity
- GuardDuty detects credential misuse
- Status: **Detection enabled** (prevention in Lab 04)

**Threat T-02: Secrets Exposure**
- Status: **Not yet addressed** (Lab 05)

**Threat T-03: Unauthorized API Activity**
- CloudTrail provides audit trail
- GuardDuty detects suspicious API calls
- Status: **Detection enabled**

**Threat T-04: Cloud Resource Abuse**
- Billing alarm detects unusual spending
- GuardDuty detects cryptomining
- Status: **Detection enabled**

**Progress:** 3 of 4 threats now have detection capabilities! 


-----

## Next Lab

➡️ [Lab 04 – IAM Identity Foundation](04-iam-identity-foundation.md)

In Lab 04, you will:
- Create least privilege IAM policies
- Implement IAM roles for applications
- Enable MFA for IAM users
- Set up password policies
- Conduct access review

- 


---

**Status:** This project is actively in progress and will be updated as the next stages are completed.

**Updates:** Posted on Linkedin as soon as a lab is complete www.linkedin.com/in/samuel-jesse-8047b3123
