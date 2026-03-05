# GitLab CI/CD Basics — Riskonnect Prep (6b)

---

## 🦋 Our Example Project

- A **static website** — `butterfly.com`
- Hosted on a **Linux server running Nginx**
- Files live at `/var/www/html/butterfly.com/`
- Every time a developer pushes new HTML/CSS → GitLab automatically deploys it to the server

> 📍 No manual FTP, no manual SSH copy — GitLab handles it automatically.

---

## 🏗️ Full Architecture — How It All Connects

```
Developer edits HTML/CSS on laptop
            ↓
    git push to GitLab repo
            ↓
  GitLab detects the push (trigger)
            ↓
  GitLab reads .gitlab-ci.yml file
            ↓
    GitLab Runner picks up the job
            ↓
  Runner SSHs into the Nginx server
            ↓
  Copies new files to /var/www/html/butterfly.com/
            ↓
  Website is updated LIVE ✅
```

---

## 🧠 Key Concepts

### 📄 .gitlab-ci.yml
- The **brain of GitLab CI/CD** — lives in the root of your repo
- Defines: what stages to run, what commands, when to trigger
- GitLab reads this file EVERY time someone pushes code
- GitLab's version of Jenkins' Jenkinsfile

> 📍 No `.gitlab-ci.yml` = no pipeline. Period.

---

### 🏃 GitLab Runner
- The **machine that actually runs your pipeline jobs**
- Like Jenkins Agent — it's the worker
- Can be:
  - Your own server (self-hosted runner)
  - GitLab's shared cloud runners
- Communicates back to GitLab with results

| | GitLab | Jenkins |
|---|---|---|
| Pipeline file | `.gitlab-ci.yml` | `Jenkinsfile` |
| Worker machine | **Runner** | **Agent** |
| Trigger | Push / MR / Schedule | Webhook / Poll |
| UI | GitLab built-in | Separate Jenkins UI |

---

### 🔁 Stages
- Stages run **in order** — one after another
- If a stage fails → pipeline stops, next stage doesn't run
- For butterfly.com we have 3 stages:

```
Stage 1: validate   → check HTML files aren't broken
Stage 2: deploy     → copy files to Nginx server
Stage 3: notify     → report success/failure
```

---

## 📄 .gitlab-ci.yml — butterfly.com

```yaml
stages:
  - validate
  - deploy

# Stage 1 — Check HTML files exist and aren't empty
validate:
  stage: validate
  script:
    - echo "Checking files..."
    - test -f index.html
    - echo "Validation passed!"

# Stage 2 — Deploy to Nginx server via SSH
deploy:
  stage: deploy
  script:
    - apt-get install -y rsync
    - rsync -avz ./ user@nginx-server:/var/www/html/butterfly.com/
  only:
    - main        # only deploy when code is pushed to main branch
```

> 📍 `rsync` copies your repo files to the Nginx server
> 📍 `only: main` = only runs on the main branch, not every random branch

---

## 🌐 Nginx Server Side — What's There

```
/var/www/html/butterfly.com/
    ├── index.html         ← homepage
    ├── about.html
    ├── style.css
    └── images/
```

### Nginx Virtual Host Config for butterfly.com:

```nginx
server {
    listen 80;
    server_name butterfly.com www.butterfly.com;

    root /var/www/html/butterfly.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    access_log /var/log/nginx/butterfly_access.log;
    error_log  /var/log/nginx/butterfly_error.log;
}
```

> 📍 Config file lives at: `/etc/nginx/conf.d/butterfly.conf`
> 📍 After editing: `nginx -t` to test → `systemctl reload nginx` to apply

---

## 🔍 Important File Locations

| What | Where |
|---|---|
| Pipeline file | Root of GitLab repo → `.gitlab-ci.yml` |
| Website files | `/var/www/html/butterfly.com/` |
| Nginx site config | `/etc/nginx/conf.d/butterfly.conf` |
| Nginx access logs | `/var/log/nginx/butterfly_access.log` |
| Nginx error logs | `/var/log/nginx/butterfly_error.log` |
| Runner config | `/etc/gitlab-runner/config.toml` |

---

## 🔍 Where to Check Things in GitLab UI

| What you want | Where to go |
|---|---|
| See pipeline runs | Repo → **CI/CD → Pipelines** |
| Check job logs | Click pipeline → click the job name → **live logs** |
| See pass/fail per stage | Pipeline view → colored stage boxes |
| Manage Runners | Settings → **CI/CD → Runners** |
| Set secret SSH keys | Settings → **CI/CD → Variables** |

> 📍 Pipeline failed? → CI/CD → Pipelines → click the red X → read the logs

---

## 🔐 How SSH Works in the Pipeline (Concept)

- Runner needs to SSH into the Nginx server to copy files
- You store the **SSH private key as a GitLab CI Variable** (secret)
- Pipeline uses the key at runtime — never hardcoded in `.gitlab-ci.yml`

```
GitLab CI Variable: SSH_PRIVATE_KEY = (your private key)
                        ↓
          Runner loads it during pipeline
                        ↓
          SSH into Nginx server
                        ↓
          rsync files to /var/www/html/butterfly.com/
```

> 📍 Settings → CI/CD → Variables → Add variable → type: File, key: SSH_PRIVATE_KEY

---

## 💼 Interview Tips — GitLab CI/CD Edition

### 🔥 Tip 1 — "What is .gitlab-ci.yml?"
**Say this:** It's the pipeline configuration file stored in the root of the GitLab repository. It defines stages, jobs, and the commands to run. GitLab reads it automatically on every push — no separate configuration needed on a Jenkins-like server.

---

### 🔥 Tip 2 — "What is a GitLab Runner?"
**Say this:** A Runner is the machine that executes the jobs defined in .gitlab-ci.yml. It can be a self-hosted server or GitLab's shared infrastructure. It's equivalent to a Jenkins Agent — the actual worker.

---

### 🔥 Tip 3 — "Pipeline failed, what do you check?"
**Say this:** Go to CI/CD → Pipelines → click the failed pipeline → click the failed job → read the console log. It shows exactly which command failed and why — same as Jenkins Console Output.

---

### 🔥 Tip 4 — "How do you store secrets like SSH keys in GitLab CI?"
**Say this:** GitLab CI Variables — stored in Settings → CI/CD → Variables. They're injected as environment variables at runtime. Never hardcode secrets in the .gitlab-ci.yml file itself.

---

*Next up: Objective 7 — IIS Server, HAProxy Load Balancer, EMC Storage 🔥*
