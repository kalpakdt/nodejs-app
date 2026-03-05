# Security Audits & Compliance — Riskonnect Prep

---

## 🔐 What Even IS a Security Audit?

- Someone external comes and checks: **"Is your infra secure and well-documented?"**
- You don't just SAY you're secure — you **prove it with evidence**
- Evidence = logs, policies, architecture docs, access reviews
- Your job: **prepare and maintain that evidence**

> 📍 Think of it like an exam — you study (prepare docs) before the examiner (auditor) arrives.

---

## 📋 The 3 Audits You Need to Know

| Audit | What it checks | Who cares |
|---|---|------|
| **SSAE18 / SOC2** | Is customer data handled securely? | SaaS companies, cloud providers |
| **ISO 27001** | Is there a full Information Security Management System? | Enterprise, global clients |

> These aren't the same thing — but they overlap a LOT.

---

## 🗂️ SOC2 — What It Actually Is

- **Service Organization Control 2** — American standard
- Checks 5 Trust Service Criteria:

| Criteria | What it means |
|---|---|
| **Security** | Is the system protected from unauthorized access? |
| **Availability** | Is the system up when customers need it? |
| **Confidentiality** | Is sensitive data kept confidential? |
| **Processing Integrity** | Does the system do what it's supposed to? |
| **Privacy** | Is personal data handled correctly? |

- **SSAE18** is just the auditing *standard* used TO conduct a SOC2 audit
- So when someone says "SSAE18 audit" — they mean a SOC2 audit done under SSAE18 rules

> 📍 SOC2 = WHAT is checked | SSAE18 = HOW the auditor checks it

---

## 🌍 ISO 27001 — What It Actually Is

- International standard for **Information Security Management System (ISMS)**
- Not just a checklist — it's a **framework** your whole org runs on
- Covers: risk management, access control, incident response, physical security, supplier security

### Key ISO 27001 Controls You'll Touch as Infra:

| Control Area | Your job |
|---|---|
| Access Control | Who has access to what servers/systems |
| Logging & Monitoring | Are logs being collected and reviewed |
| Patch Management | Are systems being patched regularly |
| Backup & Recovery | Are backups tested and working |
| Network Security | Firewalls, VPNs, segmentation docs |

---

## 📁 What YOU Actually Produce for Audits

As an infra/support person, you're responsible for:

- **Architecture diagrams** — network maps, server layouts, data flow diagrams
- **Access control lists** — who has admin access to which systems
- **Patch logs** — evidence that systems were patched on schedule
- **Change management records** — log of changes made to infrastructure
- **Runbooks / SOPs** — documented procedures for common tasks
- **Backup test records** — proof that backups were tested and restored successfully

> 📍 Auditors don't trust your word — they want **documents with dates and signatures**

---

## 🔍 Evidence Auditors Actually Ask For

| Auditor asks... | You show... |
|---|---|
| "Who has admin access?" | Access control spreadsheet / AD group report |
| "Is logging enabled?" | Screenshot of log settings + sample logs |
| "How do you handle patches?" | Patch schedule doc + Kaseya/WSUS reports |
| "What happens if a server dies?" | DR/backup runbook + last restore test result |
| "How is data encrypted in transit?" | SSL cert list + TLS config screenshots |

---

## 🔄 Audit Cycle — What Happens

```
Gap Assessment
(find what's missing)
      ↓
Fix Gaps
(update docs, configs, access)
      ↓
Internal Review
(check everything before auditor arrives)
      ↓
Auditor Visits / Reviews Evidence
      ↓
Report Issued
(Pass / Issues Found)
      ↓
Remediation
(fix anything flagged)
      ↓
Repeat next year
```

---

## 🧰 Tools That Help With Compliance at Riskonnect

| Tool | Compliance use |
|---|---|
| **Ansible** | Automated server hardening — proves consistency |
| **New Relic** | Monitoring logs — shows availability evidence |
| **Okta** | Access control — who logged in, when, from where |
| **Zscaler** | Network security logging |
| **AWS / Azure** | Cloud audit trails (CloudTrail, Activity Log) |

---

## 💼 Interview Tips — Compliance Edition

### 🔥 Tip 1 — "What's the difference between SOC2 and ISO 27001?"
**Say this:** SOC2 is an American standard focused on how a service provider protects customer data — it's audit-based. ISO 27001 is an international standard for building a full information security management system across the whole org — it's certification-based.
> Most people mix these up. Knowing the difference impresses interviewers.

---

### 🔥 Tip 2 — "What is SSAE18?"
**Say this:** SSAE18 is the auditing standard issued by the AICPA that defines HOW auditors conduct a SOC examination. It replaced the older SSAE16. So SOC2 reports are issued under SSAE18 guidelines.
> Don't overthink it — SSAE18 is the rulebook auditors follow to produce a SOC2 report.

---

### 🔥 Tip 3 — "How have you contributed to audit preparation?"
**Say this:** Talk about maintaining architecture documentation, updating access control lists, ensuring patch logs were up to date, and writing runbooks for common procedures.
> Even if you haven't done it — this is what the role expects. Know the answer cold.

---

### 🔥 Tip 4 — "What is a control in ISO 27001?"
**Say this:** A control is a specific measure or process put in place to reduce a security risk. For example, enforcing MFA on all admin accounts is a control for the access management risk.
> Tie it to something practical — shows you get it beyond buzzwords.

---

*Next up: Objective 4 — AWS Basics 🔥*
