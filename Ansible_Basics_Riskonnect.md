# Ansible Basics — Riskonnect Prep

---

## 🤖 What Even IS Ansible?

- A tool that lets you **control multiple servers** from one machine
- You write what you want done → Ansible goes and does it on all servers
- **No agent needed** on remote servers — uses plain SSH
- Written in simple YAML — reads like English

> 📍 Your machine = **Control Node** | Remote servers = **Managed Nodes**

---

## 🗂️ The 3 Things You Always Need

| Thing | What it is |
|---|---|
| **Inventory** | List of servers you want to manage |
| **Playbook** | The YAML file with your tasks/instructions |
| **SSH Access** | How Ansible connects to remote servers |

> That's it. Inventory + Playbook + SSH = Ansible working.

---

## 📋 Inventory — Your Server List

- A simple file listing your servers (by IP or hostname)
- You can group servers together

```ini
[webservers]
192.168.1.10
192.168.1.11

[dbservers]
192.168.1.20
```

> 📍 Default inventory location: `/etc/ansible/hosts`
> 📍 Or pass your own: `ansible -i myinventory.ini`

---

## 📄 Playbook Structure — How It Looks

```yaml
---
- name: Do stuff on web servers
  hosts: webservers
  become: yes          # run as sudo

  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present
```

- `hosts` → which servers to run on
- `become: yes` → run as root/sudo
- `tasks` → list of things to do
- Each task has a **module** (yum, copy, file, service, etc.)

---

## 🔨 The Modules You Actually Need

### 📦 Install / Update a Package (yum)

```yaml
- name: Install or update httpd on 3 servers
  hosts: webservers
  become: yes
  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: present       # present = install, latest = update, absent = remove
```

> Run `yum update` on all 3 servers? Just change `state: latest`

---

### 📁 Copy a File to Servers

```yaml
- name: Push config file to all servers
  hosts: webservers
  become: yes
  tasks:
    - name: Copy nginx config
      copy:
        src: /local/path/nginx.conf
        dest: /etc/nginx/nginx.conf
        owner: root
        mode: '0644'
```

---

### ✏️ Edit/Change a Line in a File

```yaml
- name: Change a setting in a config file
  hosts: webservers
  become: yes
  tasks:
    - name: Set max connections
      lineinfile:
        path: /etc/myapp/app.conf
        regexp: '^MaxConnections'
        line: 'MaxConnections = 200'
```

> `lineinfile` = find a line matching pattern → replace it. Clean and simple.

---

### 🗑️ Delete a File

```yaml
- name: Delete a file from servers
  hosts: webservers
  become: yes
  tasks:
    - name: Remove old log file
      file:
        path: /var/log/oldapp.log
        state: absent        # absent = delete it
```

---

### 📂 Move / Rename a File

```yaml
- name: Move a file
  hosts: webservers
  become: yes
  tasks:
    - name: Move config to backup
      command: mv /etc/app/app.conf /etc/app/app.conf.bak
```

> Ansible has no native "move" module — `command` module runs shell commands directly.

---

### 🔄 Start / Stop / Restart a Service

```yaml
- name: Restart Apache on all servers
  hosts: webservers
  become: yes
  tasks:
    - name: Restart httpd
      service:
        name: httpd
        state: restarted     # started / stopped / restarted / reloaded
```

---

## 🚀 Running a Playbook

```bash
# Run a playbook
ansible-playbook myplaybook.yml

# Run on specific inventory
ansible-playbook -i inventory.ini myplaybook.yml

# Dry run — see what WOULD happen without doing it
ansible-playbook myplaybook.yml --check

# Run on just one server from the group
ansible-playbook myplaybook.yml --limit 192.168.1.10
```

---

## ✅ Quick Ad-Hoc Commands (No Playbook)

```bash
# Ping all servers to check connection
ansible all -m ping

# Run a shell command on all webservers
ansible webservers -m command -a "df -h"

# Restart a service on dbservers
ansible dbservers -m service -a "name=mysql state=restarted" --become
```

> 📍 Ad-hoc = quick one-liner. Playbook = reusable script.

---

## 📊 Ansible Flow — What Happens When You Run It

```
You run playbook
      ↓
Ansible reads Inventory → finds your 3 servers
      ↓
Connects via SSH to each server
      ↓
Pushes tiny Python scripts (modules) to run tasks
      ↓
Runs tasks one by one
      ↓
Reports back: OK / Changed / Failed
```

---

## 💼 Interview Tips — Ansible Edition

### 🔥 Tip 1 — "What is idempotency in Ansible?"
**Say this:** Running the same playbook 10 times gives the same result as running it once. If nginx is already installed, Ansible won't reinstall it — it just says OK and moves on.
> This is the #1 Ansible interview concept. Don't blank on it.

---

### 🔥 Tip 2 — "What's the difference between `command` and `shell` module?"
**Say this:** `command` runs a single command directly — no bash features. `shell` runs through `/bin/sh` so you can use pipes `|`, redirects `>`, etc.
> Use `command` when you can, `shell` only when you need bash tricks.

---

### 🔥 Tip 3 — "How do you run a playbook without making changes?"
**Say this:** `--check` flag — it's a dry run. Shows what WOULD change without touching anything.
> Interviewers love this — shows you won't blindly destroy prod.

---

### 🔥 Tip 4 — "What happens if one task fails in the middle of a playbook?"
**Say this:** Ansible stops execution on that host by default and marks it as failed. Other hosts in the play continue unless you use `--forks` or `ignore_errors: yes`.
> Shows you understand real-world failure handling.

---

*Next up: Objective 3 — Linux Basics 🔥*
