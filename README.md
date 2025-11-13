# ☁️ AWS GuardDuty Threat Detection  Capstone Project 

This project defines, with AWS CloudFormation (Infrastructure as Code), a vulnerable web app stack (OWASP Juice Shop) fronted by CloudFront and instrumented with **GuardDuty** to detect suspicious behaviour (e.g., SQL injection → credential abuse → data exfiltration).  
It is part of my **Cloud Security training**; the template can be deployed in a personal AWS account (charges may apply).  

## 🏗️ Architecture (intended)
- **CloudFront** → **ALB** → **EC2 (Juice Shop + Nginx)**
- **S3 Secure Bucket** (private, SSE-S3, TLS-only policy)
- **VPC + 2 public subnets + IGW + RT**
- **IAM Role + Instance Profile** for EC2 (least-privileged S3 access)
- **GuardDuty** enabled with multiple features
- **EventBridge + Step Functions** example to auto-remediate access key abuse
<img width="547" height="570" alt="Captura de pantalla 2025-10-28 a la(s) 11 14 17" src="https://github.com/user-attachments/assets/37595272-d1c2-47f2-826d-ed253143968e" />


<img width="665" height="144" alt="Captura de pantalla 2025-10-28 a la(s) 11 11 05" src="https://github.com/user-attachments/assets/d1e63179-4150-458a-a64b-a19f2cb84e76" />




## 📂 Files
- `template.yaml` — Full CloudFormation stack (VPC, ALB, EC2, S3, GuardDuty, etc.)
- `.gitignore` — Avoids committing secrets


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
4. With GuardDuty enabled (and S3 actions from UserData), findings related to suspicious behaviour and S3 data events will appear under **GuardDuty → Findings**.

<img width="574" height="316" alt="Captura de pantalla 2025-11-06 a la(s) 0 04 15" src="https://github.com/user-attachments/assets/1f035b3c-b05c-46e9-8ce4-eaaafaf5dd17" />
<img width="574" height="408" alt="Captura de pantalla 2025-11-06 a la(s) 0 06 13" src="https://github.com/user-attachments/assets/5cc34b10-8cb6-4dfd-82ee-69cc4b1b1bbd" />



## 🛡️ Detection & Auto-Response (example)
- **GuardDuty** raises a finding on credential misuse.
- **EventBridge Rule** (pattern for access-key exfiltration) triggers a **Step Function**.
- The State Machine calls `iam: PutRolePolicy` to deny old tokens via a conditional policy.
<img width="856" height="295" alt="Captura de pantalla 2025-11-06 a la(s) 1 30 18" src="https://github.com/user-attachments/assets/7278fe88-6b3c-43a7-a773-7a4a74811989" />

<img width="629" height="379" alt="Captura de pantalla 2025-11-06 a la(s) 1 32 14" src="https://github.com/user-attachments/assets/f6bef6e8-591a-4833-87f5-5abcf39e3fad" />



## ⚠️ Cost & Safety
- Stack uses public subnets and an intentionally vulnerable application (for learning).  
  **Do not expose real data.**  
- Tear down with **CloudFormation → Delete stack**.

## 📜 License
MIT
