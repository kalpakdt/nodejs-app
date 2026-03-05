# Active Directory, Group Policy & LDAP Basics — Riskonnect Prep (Objective 8)

---

## 🏢 What is Active Directory?

- Microsoft's system for managing **users, computers and permissions** across an organization
- One place to control: who can login, what they can access, what policies apply
- Lives on a **Windows Server** called a **Domain Controller (DC)**
- Every corporate Windows machine is "joined" to the domain — AD controls it

> 📍 Without AD: every PC manages its own users locally
> 📍 With AD: one central place manages ALL users across ALL machines

---

## 🗂️ AD Structure — The Ugly Hierarchy

```
Forest (top level — whole company)
    └── Domain (e.g. riskonnect.local)
            ├── OU (Organizational Unit)
            │     ├── Users
            │     │     ├── john.doe
            │     │     └── jane.smith
            │     ├── Computers
            │     │     ├── LAPTOP-001
            │     │     └── SERVER-01
            │     └── Groups
            │           └── IT-Admins
            └── Domain Controllers
                  ├── DC01 (Primary)
                  └── DC02 (Backup)
```

---

## 🔑 Key Terms — Cheat Sheet

| Term | What it means |
|---|---|
| **Domain** | Your organization's AD boundary (e.g. `riskonnect.local`) |
| **Domain Controller (DC)** | The server that runs Active Directory |
| **OU (Organizational Unit)** | A folder inside AD to organize users/computers |
| **User Account** | A login identity in AD |
| **Computer Account** | A machine joined to the domain |
| **Group** | Collection of users — assign permissions to group not individuals |
| **Forest** | One or more domains — top of the AD hierarchy |
| **Trust** | Allows users from one domain to access another |

---

## 👤 Users & Groups — How Access Works

- Never assign permissions to individual users — assign to **Groups**
- User joins a Group → gets all Group permissions automatically
- User leaves → remove from Group → access gone instantly

```
IT-Admins Group
    ├── john.doe      ← has admin access because of group
    ├── jane.smith
    └── kalpak.dt

HR-Staff Group
    ├── alice.hr
    └── bob.hr        ← only HR access, nothing else
```

> 📍 Where to manage: `dsa.msc` (Active Directory Users and Computers)

---

## 📁 Important AD Tools

| Tool | What it does | Open with |
|---|---|---|
| **ADUC** | Manage users, groups, OUs, computers | `dsa.msc` |
| **GPMC** | Manage Group Policies | `gpmc.msc` |
| **AD Sites & Services** | Manage DC replication | `dssite.msc` |
| **DNS Manager** | AD relies heavily on DNS | `dnsmgmt.msc` |

---

## 📋 Group Policy — What It Is

- A way to **push settings/rules to all computers** in the domain automatically
- Settings apply when user logs in or computer starts
- Examples of what Group Policy can do:

| What you can enforce | Example |
|---|---|
| Password rules | Min 8 chars, expire every 90 days |
| Lock screen | Auto-lock after 5 mins idle |
| Software install | Push software to all machines silently |
| Restrict access | Block USB drives, block Control Panel |
| Map network drives | Auto-map \\server\share on login |
| Wallpaper | Force company wallpaper (annoying but real) |

---

## 🏗️ Group Policy Structure

```
Group Policy Object (GPO)
        ↓
Linked to → Domain / OU / Site
        ↓
Applies to → All users/computers in that OU
```

- **GPO** = the container holding all the settings
- You **link** a GPO to an OU → everyone in that OU gets those settings
- Multiple GPOs = processed in order (LSDOU: Local → Site → Domain → OU)

> 📍 Create/edit GPOs: `gpmc.msc`
> 📍 Force immediate apply on a machine: `gpupdate /force` in cmd

---

## 🌐 LDAP — The Protocol Under AD

---

## 🤔 What is LDAP?

- **Lightweight Directory Access Protocol**
- The language/protocol used to **talk to Active Directory**
- Apps use LDAP to: authenticate users, search for users, pull group memberships
- AD is Microsoft's directory — LDAP is how you query it

> 📍 AD = the database | LDAP = the query language to talk to it
> 📍 Like SQL is to a database — LDAP is to AD

---

## 🏗️ LDAP Structure — The DN (Distinguished Name)

Everything in LDAP has an address called a **DN**:

```
CN=John Doe,OU=IT-Staff,DC=riskonnect,DC=local
```

Breaking it down:

| Part | Means | Example |
|---|---|---|
| **CN** | Common Name — the object name | `CN=John Doe` |
| **OU** | Organizational Unit — folder it's in | `OU=IT-Staff` |
| **DC** | Domain Component — the domain | `DC=riskonnect,DC=local` |

> 📍 Read it right to left: domain → OU → object
> 📍 `riskonnect.local` becomes `DC=riskonnect,DC=local` in LDAP

---

## 🔌 How Apps Use LDAP (Real World)

```
User tries to login to Riskonnect app
            ↓
App sends LDAP query to Domain Controller
"Does user john.doe exist? Is password correct?"
            ↓
DC checks AD → responds YES/NO
            ↓
App allows or denies login
```

Common apps that use LDAP auth:
- Web apps (Riskonnect, Jira, Confluence)
- VPNs
- Email servers
- Jenkins, GitLab

> 📍 LDAP port: **389** (plain) | **636** (LDAPS — SSL encrypted)

---

## 🔍 LDAP Key Terms

| Term | Meaning |
|---|---|
| **DN** | Full address/path of an object in AD |
| **CN** | Common Name — name of user/group/computer |
| **OU** | Organizational Unit — folder in AD |
| **DC** | Domain Component — part of domain name |
| **Base DN** | Starting point for LDAP search (`DC=riskonnect,DC=local`) |
| **Bind** | LDAP login — app authenticates to AD with a service account |
| **Search Filter** | Query to find objects (`(sAMAccountName=john.doe)`) |
| **LDAPS** | LDAP over SSL — encrypted, port 636 |

---

## 🔥 Common AD/LDAP Issues You'll Face

| Problem | Cause | Fix |
|---|---|---|
| User can't login | Account locked/disabled | ADUC → find user → unlock account |
| Password expired | Password policy via GPO | Reset in ADUC or user self-service |
| App can't authenticate via LDAP | Wrong Base DN or service account password | Check LDAP config in app, verify service account in AD |
| Group Policy not applying | GPO not linked to correct OU | GPMC → check GPO links + run `gpresult /r` on machine |
| AD replication issue | DC not syncing | `repadmin /showrepl` on DC |

---

## 💼 Interview Tips — AD, GPO & LDAP Edition

### 🔥 Tip 1 — "What is an OU in Active Directory?"
**Say this:** An Organizational Unit is a container inside AD used to organize users, computers, and groups — like folders. You apply Group Policies to OUs, so everything inside automatically gets those settings.

---

### 🔥 Tip 2 — "What is LDAP and how does it relate to AD?"
**Say this:** LDAP is the protocol used to query Active Directory. AD is the directory database — LDAP is how applications talk to it. When an app does SSO or user authentication against AD, it's using LDAP under the hood on port 389 or 636 for encrypted.

---

### 🔥 Tip 3 — "User says they can't login to a domain machine, what do you check?"
**Say this:** Check in ADUC if the account is locked out or disabled. Check if the password is expired. Check if the computer is still joined to the domain. Then check if the DC is reachable — `ping riskonnect.local`.

---

### 🔥 Tip 4 — "What is a DN in LDAP?"
**Say this:** Distinguished Name — it's the full unique path of an object in Active Directory. Like `CN=John Doe,OU=IT-Staff,DC=riskonnect,DC=local`. Every object in AD has one — it's how LDAP uniquely identifies anything in the directory.

---

*🎉 All 8 Objectives Done Boss.*
*Windows Server → Ansible → Security Audits → AWS → Apache/Tomcat → Jenkins/GitLab → IIS/HAProxy/EMC → AD/GPO/LDAP*
