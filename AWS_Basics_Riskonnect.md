# AWS Basics — Riskonnect Prep

---

## ☁️ Why AWS Matters at Riskonnect

- Riskonnect hosts client infrastructure on AWS — not in some dusty on-prem datacenter
- When a client's app is slow or down → **you're debugging AWS, not a physical server**
- Patching, deployments, scaling — all happen in AWS
- Security audits (SOC2, ISO27001) require AWS audit trails as evidence

> 📍 You don't need to be a cloud architect — you need to know enough to **support, troubleshoot and maintain** client infra on AWS.

---

## 🗺️ The Big Picture — How AWS Is Organized

```
AWS Account
    └── Region (e.g. ap-south-1 Mumbai)
            └── VPC (your private network)
                    ├── Subnet (slice of the network)
                    │       └── EC2 (your server)
                    ├── Security Group (firewall rules)
                    └── Internet Gateway (connects to internet)
```

> Everything lives inside a **Region** → inside a **VPC** → inside **Subnets**

---

## 🔑 Core Services You MUST Know

### 🖥️ EC2 — Elastic Compute Cloud
- Your **virtual server** in the cloud — like a Windows/Linux machine you SSH into
- You pick: OS, size (CPU/RAM), storage
- Can be started, stopped, rebooted, terminated

> 📍 Where to check: AWS Console → EC2 → Instances
> 📍 Status checks failing = instance health issue → reboot or check system logs

---

### 🌐 VPC — Virtual Private Cloud
- Your **private network** inside AWS
- Isolates your servers from other AWS customers
- Controls what traffic comes in/out

> 📍 Think of VPC = your office building | Subnets = floors | EC2 = desks

---

### 🔒 Security Groups — Cloud Firewall
- Controls **inbound and outbound traffic** to EC2 instances
- Rule-based: allow port 443 from anywhere, allow port 22 from your IP only
- **Stateful** — if you allow inbound, the response automatically goes out

| Rule type | Example |
|---|---|
| Inbound | Allow HTTPS (443) from 0.0.0.0/0 |
| Inbound | Allow SSH (22) from your IP only |
| Outbound | Allow all traffic out |

> 📍 If something can't connect → Security Group is almost always the first suspect

---

### 🪣 S3 — Simple Storage Service
- **Object storage** — store files, logs, backups, configs
- Not a file system — you upload/download objects via URL or CLI
- Used heavily for: **log archiving, backups, static file hosting**

> 📍 Buckets are containers | Objects are files inside buckets
> 📍 Check bucket permissions carefully — misconfigured S3 = public data breach

---

### 🔐 IAM — Identity & Access Management
- Controls **who can do what** in your AWS account
- Users, Groups, Roles, Policies
- **Principle of Least Privilege** — give only the permissions needed, nothing more

| IAM Thing | What it is |
|---|---|
| **User** | A person or service with AWS access |
| **Group** | Collection of users with same permissions |
| **Role** | Permissions attached to a service (e.g. EC2 can access S3) |
| **Policy** | JSON document defining what's allowed/denied |

> 📍 Audit question: "Who has admin access?" → IAM → Users → check attached policies

---

### ⚖️ ELB — Elastic Load Balancer
- Distributes traffic across **multiple EC2 instances**
- If one server dies → traffic automatically goes to healthy ones
- Types you'll hear:
  - **ALB (Application Load Balancer)** → Layer 7, routes by URL path/host
  - **NLB (Network Load Balancer)** → Layer 4, raw TCP/UDP speed

> 📍 If a site is partially down (some users affected, not all) → check ELB target group health

---

### 🗺️ Route 53 — DNS
- AWS's DNS service — maps domain names to IP addresses / AWS resources
- Also does health checks and failover routing

> 📍 Site unreachable by domain but reachable by IP? → Route 53 / DNS issue

---

## 🔍 Troubleshooting With AWS — Quick Cheat Sheet

| Problem | Where to look |
|---|---|
| EC2 instance unreachable | Security Group inbound rules, check instance status |
| App slow / timing out | ELB target group health, EC2 CPU/memory metrics |
| Can't SSH into server | Security Group port 22, Key pair, instance running? |
| Storage full | EC2 → EBS volume size, check disk usage inside instance |
| Who changed what in AWS | **CloudTrail** → audit log of all API actions |
| App logs | EC2 instance directly, or CloudWatch Logs if configured |

---

## 📊 AWS Tools for Monitoring & Audit

| Tool | Purpose |
|---|---|
| **CloudWatch** | Metrics, logs, alarms — CPU, memory, disk |
| **CloudTrail** | Audit trail — who did what, when, from where |
| **AWS Config** | Tracks config changes to AWS resources over time |
| **Trusted Advisor** | Security & cost recommendations |

> 📍 SOC2 auditors will ask for CloudTrail logs as evidence of access control

---

## 💼 Interview Tips — AWS Edition

### 🔥 Tip 1 — "EC2 instance is unreachable, what do you check?"
**Say this:** First check instance state (running?), then Security Group inbound rules for the port, then check if there's a Network ACL blocking traffic, then check if the route table has an Internet Gateway attached.
> Walk them through the layers — shows structured troubleshooting.

---

### 🔥 Tip 2 — "What's the difference between Security Group and Network ACL?"
**Say this:** Security Groups are stateful and attached to instances — allow inbound and the response automatically goes out. Network ACLs are stateless and attached to subnets — you need explicit inbound AND outbound rules.
> This trips up most people. Nail this answer.

---

### 🔥 Tip 3 — "How do you audit who made changes in AWS?"
**Say this:** AWS CloudTrail — it logs every API call made in the account. You can see who did what, from which IP, at what time. It's the primary evidence source for security audits.

---

### 🔥 Tip 4 — "What is IAM Role vs IAM User?"
**Say this:** IAM User is for humans or long-lived programmatic access with static credentials. IAM Role is for AWS services or temporary access — no static keys, uses temporary tokens. Always prefer Roles over Users for services.
> Best practice answer — shows you know secure AWS architecture.

---

*Next up: Objective 5 — Apache & Tomcat Basics 🔥*
