# GitLab CI/CD — Your Real World Upgrade (6c)
### From: Manual SFTP Chaos → To: Automated GitLab Pipeline

---

## 😤 How It Was Before (Your Old Life)

```
Dev finishes changes on their laptop
            ↓
Dev SFTPs the files to you
            ↓
You manually copy to /var/www/html/siteX/
            ↓
You restart / reload Nginx
            ↓
You manually test if site is up
            ↓
Dev says "it's broken" at 11pm
            ↓
You SSH in again and fix it
            ↓
Repeat for 10-20 different servers
```

> 📍 You were the human CI/CD pipeline. GitLab replaces that grunt work.

---

## ✅ How It Works After GitLab (New Life)

```
Dev finishes changes on their laptop
            ↓
git push to GitLab repo
            ↓
GitLab automatically reads .gitlab-ci.yml
            ↓
GitLab Runner SSHs into the correct server
            ↓
Copies files to /var/www/html/siteX/
            ↓
Reloads Nginx automatically
            ↓
Pipeline shows GREEN = site is live ✅
            ↓
You didn't touch anything
```

> 📍 You set it up ONCE per site. After that → devs deploy themselves.

---

## 🏗️ Full Architecture — 3 Sites, 3 Servers

```
                    GitLab Server
                  (hosts all repos)
                        |
              ┌─────────┼─────────┐
              ↓         ↓         ↓
         Runner 1   Runner 2   Runner 3
         (or same runner, different jobs)
              ↓         ↓         ↓
         Server 1   Server 2   Server 3
      /var/www/html  /var/www/html  /var/www/html
       /site1.com/    /site2.com/    /site3.com/
```

- Each website has its **own GitLab repo**
- Each repo has its **own `.gitlab-ci.yml`**
- One GitLab Runner can serve ALL servers (registers once, works everywhere)
- Or each server has its own Runner — your call

> 📍 One GitLab installation manages ALL 10-20 sites. Not 10-20 separate tools.

---

## 🔑 The One Thing That Makes This Work — SSH Key Setup

Since Runner needs to SSH into each server to copy files:

```
Step 1: Generate SSH key pair on GitLab Runner machine
        ssh-keygen -t rsa -b 4096

Step 2: Copy public key to each web server
        ssh-copy-id user@server1-ip
        ssh-copy-id user@server2-ip
        (do this once per server)

Step 3: Store private key in GitLab as a CI Variable
        GitLab Repo → Settings → CI/CD → Variables
        Key: SSH_PRIVATE_KEY
        Value: (paste private key)
        Type: File

Step 4: Pipeline uses the key automatically — you never touch it again
```

---

## 📄 .gitlab-ci.yml — For Each Site

Every site's repo gets this file:

```yaml
stages:
  - deploy

deploy_to_server:
  stage: deploy
  before_script:
    - apt-get install -y rsync openssh-client
    - eval $(ssh-agent -s)
    - ssh-add "$SSH_PRIVATE_KEY"
    - mkdir -p ~/.ssh
    - ssh-keyscan YOUR_SERVER_IP >> ~/.ssh/known_hosts
  script:
    - rsync -avz --delete ./ user@YOUR_SERVER_IP:/var/www/html/yoursite.com/
    - ssh user@YOUR_SERVER_IP "sudo systemctl reload nginx"
  only:
    - main
```

> 📍 Change `YOUR_SERVER_IP` and `yoursite.com` per site — everything else stays the same
> 📍 `--delete` in rsync = removes old files that no longer exist in repo (clean deploy)

---

## 📁 Folder Structure On Each Server

```
/var/www/html/
    ├── site1.com/
    │     ├── index.html
    │     ├── style.css
    │     └── images/
    ├── site2.com/
    │     ├── index.html
    │     └── app.js
    └── site3.com/
          └── index.html
```

Each site has its own **Nginx virtual host config**:

```nginx
# /etc/nginx/conf.d/site1.conf
server {
    listen 80;
    server_name site1.com www.site1.com;
    root /var/www/html/site1.com;
    index index.html;
    access_log /var/log/nginx/site1_access.log;
    error_log  /var/log/nginx/site1_error.log;
}
```

> 📍 You create this config ONCE manually. After that GitLab handles all file updates.

---

## 🚨 When Something Still Goes Wrong — Your Job Now

Even with GitLab, you still handle:

| Issue | Where to check |
|---|---|
| Pipeline failed | GitLab → CI/CD → Pipelines → job logs |
| Files copied but site broken | SSH into server → `tail -f /var/log/nginx/site1_error.log` |
| Nginx not reloading | `systemctl status nginx` → `nginx -t` for config errors |
| SSH connection refused in pipeline | Check Security Group port 22, check SSH key in GitLab Variables |
| Dev pushed to wrong branch | Check `.gitlab-ci.yml` → `only: main` — only main branch deploys |

---

## 💡 Before vs After — Your Life in a Table

| | Before (Manual) | After (GitLab) |
|---|---|---|
| **How files arrive** | Dev SFTPs to you | Dev git pushes |
| **Who copies files** | You, manually | GitLab Runner |
| **Nginx reload** | You, manually | Pipeline does it |
| **Deploy time** | 10-30 mins per site | 2-3 mins automatic |
| **You involved?** | Every single time | Only when pipeline breaks |
| **Audit trail** | None | Full git history + pipeline logs |
| **3am emergency** | You wake up | Dev fixes and pushes |

---

## 💼 Interview Tips — Your Story Edition

### 🔥 Tip 1 — How to tell this story in interview
**Say this:** "I was managing 10-20 websites manually — devs would SFTP files, I'd deploy to Nginx servers. I introduced GitLab CI/CD pipelines so devs could deploy directly on git push. Reduced my deployment involvement from every deploy to only handling failures."
> This shows initiative. You didn't just do the job — you improved the process.

---

### 🔥 Tip 2 — "Why GitLab over Jenkins for this?"
**Say this:** GitLab CI/CD is built into the same platform where the code lives — no separate Jenkins server to maintain. For multiple small static sites, it's simpler, faster to set up per repo, and the pipeline file lives with the code.

---

### 🔥 Tip 3 — "How did you handle multiple servers?"
**Say this:** One GitLab Runner registered across all servers. Each repo had its own .gitlab-ci.yml pointing to its specific server IP. SSH keys stored as GitLab CI Variables per repo — isolated and secure.

---

*Done with Objective 6 (a, b, c) 🔥*
*Next up: Objective 7 — IIS Server, HAProxy Load Balancer, EMC Storage*
