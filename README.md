# ☁️ AWS GuardDuty Threat Detection — Capstone

This project defines, with AWS CloudFormation (Infrastructure as Code), a vulnerable web app stack (OWASP Juice Shop) fronted by CloudFront and instrumented with **GuardDuty** to detect suspicious behavior (e.g., SQL injection → credential abuse → data exfiltration).  
It is part of my **Cloud Security training**; the template can be deployed in a personal AWS account (charges may apply).  

## 🏗️ Architecture (intended)
- **CloudFront** → **ALB** → **EC2 (Juice Shop + Nginx)**
- **S3 Secure Bucket** (private, SSE-S3, TLS-only policy)
- **VPC + 2 public subnets + IGW + RT**
- **IAM Role + Instance Profile** for EC2 (least-privileged S3 access)
- **GuardDuty** enabled with multiple features
- **EventBridge + Step Functions** example to auto-remediate access key abuse

## 📂 Files
- `template.yaml` — Full CloudFormation stack (VPC, ALB, EC2, S3, GuardDuty, etc.)
- `.gitignore` — Avoids committing secrets
- (optional) `assets/` — diagrams and screenshots

## �� How to deploy (when account is fully enabled)
1. Log in as IAM admin → Region closest to you.
2. Open **CloudFormation → Create stack → With new resources (standard)**.
3. Upload `template.yaml` → Next → keep defaults → Create stack.
4. When `CREATE_COMPLETE`, check **Outputs → JuiceShopURL** to access the app.

> Note: Brand-new AWS accounts may block CloudFront/ALB until verification.  
> If you can’t create those resources, skip deployment and review the template as IaC.

## 🧪 Attack demo (Juice Shop)
1. Open Juice Shop → *Account → Login*  
2. Try classic SQLi:
   - **Email:** `' or 1=1;--`
   - **Password:** `.`  
3. App should grant a session (vulnerable flow).  
4. With GuardDuty enabled (and S3 actions from UserData), findings related to suspicious behavior and S3 data events will appear under **GuardDuty → Findings**.

## 🛡️ Detection & Auto-Response (example)
- **GuardDuty** raises a finding on credential misuse.
- **EventBridge Rule** (pattern for access-key exfiltration) triggers a **Step Function**.
- The State Machine calls `iam:PutRolePolicy` to deny old tokens via a conditional policy.

## ⚠️ Cost & Safety
- Stack uses public subnets and intentionally vulnerable application (for learning).  
  **Do not expose real data.**  
- Tear down with **CloudFormation → Delete stack**.

## 📜 License
MIT
