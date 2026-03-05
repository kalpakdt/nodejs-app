# Jenkins Basics — Architecture & Concepts — Riskonnect Prep (6a)

---

## 🤖 What Even IS Jenkins?

- An **automation server** — it runs tasks automatically so humans don't have to
- Main job: **Build → Test → Deploy** code automatically when someone pushes changes
- Open source, runs on Java, lives on a server
- The most widely used CI/CD tool in the world

> 📍 Without Jenkins: Dev pushes code → someone manually builds, tests, deploys.
> 📍 With Jenkins: Dev pushes code → Jenkins does all of it automatically.

---

## 🏗️ Jenkins Architecture — The Big Picture

```
Developer pushes code
        ↓
    GitHub / GitLab
        ↓
   Jenkins Master
  (receives the trigger)
        ↓
  Assigns job to Agent
        ↓
  Agent runs the build/test/deploy
        ↓
  Results reported back to Master
        ↓
  Notify developer (pass/fail)
```

---

## 🧠 Master — The Brain

- The **central Jenkins server** that:
  - Manages the web UI
  - Schedules jobs
  - Monitors agents
  - Stores all config and job history
- Does NOT run builds itself (in production setups) — delegates to agents

> 📍 Only one Master (unless HA setup)
> 📍 Access Jenkins UI: `http://<server-ip>:8080`

---

## 🤝 Agent (aka Node / Slave) — The Worker

- A separate machine that **actually runs the build jobs**
- Can be Linux, Windows, Docker containers
- Master connects to Agent via **SSH** (Linux) or **JNLP** (Java)
- You can have many agents — run jobs in parallel

| | Master | Agent |
|---|---|---|
| **Job** | Schedule & manage | Actually run the build |
| **Count** | One | Many |
| **UI** | Yes | No |
| **Runs builds** | Avoided in prod | Yes |

> 📍 Why separate agents? So a broken build doesn't crash your Jenkins server.

---

## 🔁 What is a Pipeline?

- A **pipeline** is the full automated process — written as code
- Defines every step: pull code → build → test → deploy
- Written in a file called **Jenkinsfile** — lives in your code repo

```
Stage 1: Checkout   → Pull code from Git
Stage 2: Build      → Compile / package the app
Stage 3: Test       → Run automated tests
Stage 4: Deploy     → Push to server / cloud
```

> 📍 Jenkinsfile = the instructions Jenkins follows. No file = no automation.

---

## 📄 Jenkinsfile — What It Looks Like (Concept Only)

```groovy
pipeline {
    agent any          // run on any available agent

    stages {
        stage('Build') {
            steps {
                echo 'Building the app...'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to server...'
            }
        }
    }
}
```

- `pipeline` → the whole thing
- `agent` → which machine runs it
- `stages` → the steps in order
- `steps` → actual commands inside each stage

---

## 🔔 How Jenkins Knows When to Run

| Trigger Type | What it means |
|---|---|
| **Webhook** | GitHub/GitLab pings Jenkins the moment code is pushed |
| **Poll SCM** | Jenkins checks Git every X minutes for new commits |
| **Manual** | Someone clicks "Build Now" in the UI |
| **Scheduled** | Runs at a fixed time (like a cron job) |

> 📍 Webhook = instant trigger. Best option in production.

---

## 🗂️ Key Jenkins Concepts — Cheat Sheet

| Term | What it means |
|---|---|
| **Job / Project** | A single automated task configured in Jenkins |
| **Build** | One run/execution of a job |
| **Pipeline** | Multi-stage automated process (code → deploy) |
| **Jenkinsfile** | Code file that defines the pipeline |
| **Node / Agent** | Machine that runs the build |
| **Workspace** | Folder on agent where build files are stored |
| **Plugin** | Add-ons that extend Jenkins (Git, Slack, AWS, etc.) |
| **Artifact** | Output of a build (e.g. a .jar or .war file) |
| **Console Output** | Live logs of what Jenkins is doing during a build |

---

## 🔍 Where to Check Things in Jenkins UI

| What you want | Where to go |
|---|---|
| See all jobs | Jenkins Home → Dashboard |
| Check build logs | Job → Build History → Console Output |
| Add a new agent | Manage Jenkins → Nodes |
| Install a plugin | Manage Jenkins → Plugins |
| Check system health | Manage Jenkins → System Information |
| View pipeline stages | Job → Stage View (visual pipeline) |

---

## 💼 Interview Tips — Jenkins Edition

### 🔥 Tip 1 — "What is the difference between Master and Agent?"
**Say this:** Master is the Jenkins server that manages everything — UI, scheduling, job history. Agents are the worker machines that actually run the builds. In production you never run builds on the master — too risky.

---

### 🔥 Tip 2 — "What is a Jenkinsfile?"
**Say this:** A Jenkinsfile is a text file stored in the code repository that defines the entire pipeline as code — what stages to run, on which agent, and what commands to execute. Pipeline-as-code means it's version controlled just like the app.

---

### 🔥 Tip 3 — "A Jenkins build failed, what do you check first?"
**Say this:** Console Output of that build — it shows exactly which stage failed and the error message. Then check if it's a code issue, an agent connectivity issue, or a missing dependency.

---

### 🔥 Tip 4 — "What triggers a Jenkins pipeline?"
**Say this:** Most commonly a webhook from GitHub or GitLab — when code is pushed, Git pings Jenkins immediately. You can also use Poll SCM, scheduled triggers, or manual builds.

---

*Next up: Objective 6b — GitLab CI/CD (when Boss says so) 🔥*
*Then: Objective 7 — IIS, HAProxy Load Balancer, EMC Storage 🔥*
