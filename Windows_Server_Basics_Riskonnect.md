# Windows Server Basics — Riskonnect Prep

---

## 🖥️ What Even IS Windows Server?

- It's just Windows — but built for running **services, not a desktop**
- You host stuff on it: websites, file shares, user accounts, apps
- Versions you care about: **2012, 2016, 2019**
- Managed via **Server Manager** (GUI) or **PowerShell** (terminal)

> 📍 Where to open Server Manager? → Starts automatically on login, or `Win + R` → `servermanager`

---

## ⚙️ Roles & Features

| Term | What it means |
|---|---|
| **Role** | A major service (e.g. Web Server, DNS, AD) |
| **Feature** | A small add-on (e.g. .NET Framework, Telnet) |
| **IIS** | Web server role — hosts websites/apps |
| **AD DS** | Active Directory role — manages users |

> 📍 Where to add/remove roles? → Server Manager → **Add Roles and Features**

---

## 📁 Important Locations to Know

| What | Where |
|---|---|
| System logs | `Event Viewer` → Windows Logs → System / Application |
| App crash logs | `Event Viewer` → Windows Logs → **Application** |
| IIS logs | `C:\inetpub\logs\LogFiles\` |
| IIS website files | `C:\inetpub\wwwroot\` |
| Certificates | `certmgr.msc` (current user) / `certlm.msc` (local machine) |
| Services list | `services.msc` |
| Hosts file | `C:\Windows\System32\drivers\etc\hosts` |
| Temp files | `C:\Windows\Temp\` |

> 📍 How to open any `.msc` tool? → `Win + R` → type the name → Enter

---

## 🔐 certmgr — Certificate Manager

- Stores **SSL/TLS certificates** on the machine
- Two stores matter:
  - **Personal** → certs assigned to THIS machine/service
  - **Trusted Root CAs** → who the machine trusts
- `certmgr.msc` = current user certs
- `certlm.msc` = **local machine** certs (this is what IIS uses)

> 📍 IIS needs certs from `certlm.msc` → Personal store

---

## 📋 Event Viewer — Where Shit Goes Wrong

- **Windows Logs → System** → OS-level crashes, driver failures
- **Windows Logs → Application** → App errors, service failures
- **Custom Views → Administrative Events** → quick summary of all errors/warnings

> 📍 Open it: `Win + R` → `eventvwr.msc`

---

## 🔧 Services — Start / Stop / Restart

- Every background process runs as a **Service**
- Key services you'll touch:
  - `W3SVC` → IIS Web Server
  - `WinRM` → Remote management
  - `wuauserv` → Windows Update

> 📍 Where: `services.msc` → right-click → Start / Stop / Restart

---

## 🌐 IIS — Internet Information Server (Basics Only)

- Microsoft's built-in **web server**
- Hosts `.NET` apps, static sites, APIs
- Each website = a **Site** with a port binding (default: port 80/443)
- App runs inside an **Application Pool** (think: isolated process)

> 📍 Open IIS Manager: `Win + R` → `inetmgr`
> 📍 Logs: `C:\inetpub\logs\LogFiles\`

---

## 💼 Interview Tips — Don't Get Caught With Your Pants Down

> These are the questions interviewers LOVE to throw at you.

### 🔥 Tip 1 — "A website is down, what do you check first?"
**Say this:** Event Viewer → Application logs, then check IIS Manager if the site/app pool is stopped, then check `services.msc` if W3SVC is running.
> Never say "I restart the server" first. That's a red flag.

---

### 🔥 Tip 2 — "What's an Application Pool in IIS?"
**Say this:** It's an isolated worker process (`w3wp.exe`) that runs a web app. Isolation means one app crashing doesn't take down others.
> Bonus: mention you can set it to recycle automatically or run under a specific service account.

---

### 🔥 Tip 3 — "Where do you check SSL cert expiry?"
**Say this:** `certlm.msc` → Personal → Certificates → check the Expiration Date column.
> Interviewers LOVE this — most juniors say "I Google it."

---

### 🔥 Tip 4 — "Difference between local admin and domain admin?"
**Say this:** Local admin = admin only on that one machine. Domain admin = admin across ALL machines in the Active Directory domain.
> This ties into the AD/Group Policy objective — shows you connect the dots.

---

### 🔥 Tip 5 — "How do you check what's running on port 80?"
**Say this:** 
```
netstat -ano | findstr :80
```
Then match the PID in Task Manager or `tasklist`.
> Simple command, but most people blank on it in interviews.

---

## 🧠 Spaced Repetition — Check Yourself

**Answer these without looking:**

- [ ] Where do you check application crash logs on Windows Server?
- [ ] What's the difference between `certmgr.msc` and `certlm.msc`?
- [ ] Where are IIS website files stored by default?
- [ ] What `.msc` tool shows all running services?
- [ ] What Event Viewer log shows OS-level failures?
- [ ] Where do you add a new Role like IIS?
- [ ] What service name is IIS running under?
- [ ] Where are IIS access logs stored?
- [ ] What command finds what's running on a port?
- [ ] What's an Application Pool?

---

*Next up: Objective 2 — Ansible Basics 🔥*
