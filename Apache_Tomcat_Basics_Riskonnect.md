# Apache & Tomcat Basics — Riskonnect Prep

---

## 🌐 What is Apache HTTP Server?

- The most widely used **web server** in the world
- Serves static files (HTML, CSS, images) and acts as a **reverse proxy**
- Listens on **port 80** (HTTP) and **port 443** (HTTPS)
- Lives on Linux — config files, logs, everything in plain text

> 📍 Apache = the front door of your web app. Requests come in → Apache decides where they go.

---

## 🐱 What is Tomcat?

- A **Java application server** — runs Java web apps (.war files)
- Made by Apache Software Foundation but it's a DIFFERENT thing from Apache HTTP
- Listens on **port 8080** by default
- Apache HTTP sits in front → Tomcat runs the actual Java app behind it

```
User Browser
     ↓
Apache HTTP (port 80/443)   ← handles SSL, static files
     ↓
Tomcat (port 8080)          ← runs the Java app
     ↓
Database
```

> 📍 Apache = web server | Tomcat = app server. They work together, not the same thing.

---

## 📁 Apache — Important File Locations (Linux)

| What | Where |
|---|---|
| Main config file | `/etc/httpd/conf/httpd.conf` (RHEL) |
| Virtual host configs | `/etc/httpd/conf.d/*.conf` |
| Access logs | `/var/log/httpd/access_log` |
| Error logs | `/var/log/httpd/error_log` |
| Web root (files served) | `/var/www/html/` |
| Start/Stop service | `systemctl start/stop/restart httpd` |

---

## 📁 Tomcat — Important File Locations (Linux)

| What | Where |
|---|---|
| Main config | `$CATALINA_HOME/conf/server.xml` |
| Web app deployments | `$CATALINA_HOME/webapps/` |
| Tomcat logs | `$CATALINA_HOME/logs/catalina.out` |
| App-specific logs | `$CATALINA_HOME/logs/localhost.YYYY-MM-DD.log` |
| Start Tomcat | `$CATALINA_HOME/bin/startup.sh` |
| Stop Tomcat | `$CATALINA_HOME/bin/shutdown.sh` |

> 📍 `CATALINA_HOME` is usually `/opt/tomcat` or `/usr/share/tomcat`
> 📍 `catalina.out` = your best friend when Tomcat breaks

---

## ⚙️ Apache Virtual Hosts — How Sites Are Hosted

- One Apache server can host **multiple websites** using Virtual Hosts
- Each site gets its own config file in `/etc/httpd/conf.d/`

```apache
<VirtualHost *:80>
    ServerName myapp.riskonnect.com
    DocumentRoot /var/www/myapp
    ErrorLog /var/log/httpd/myapp_error.log
    CustomLog /var/log/httpd/myapp_access.log combined
</VirtualHost>
```

> 📍 After editing any config → always test before restarting:
> `apachectl configtest` or `httpd -t`

---

## 🔁 Apache as Reverse Proxy to Tomcat

- Apache receives the request → forwards it to Tomcat → sends response back to user
- Uses `mod_proxy` module

```apache
<VirtualHost *:80>
    ServerName myapp.riskonnect.com
    ProxyPass / http://localhost:8080/
    ProxyPassReverse / http://localhost:8080/
</VirtualHost>
```

> 📍 This is the standard setup at most companies — Apache in front, Tomcat behind.

---

## 🔥 Troubleshooting Apache — Common Issues

### ❌ Apache Won't Start

```bash
# Check what's wrong
systemctl status httpd
journalctl -xe

# Test config for syntax errors FIRST
apachectl configtest

# Check if port 80 is already in use
netstat -tulnp | grep :80
```

---

### ❌ 403 Forbidden Error

- Apache is running but **refusing to serve the files**
- Causes:
  - Wrong file permissions on `/var/www/html/`
  - `Options -Indexes` set (directory listing disabled, no index file)
  - SELinux blocking access

```bash
# Fix file permissions
chmod -R 755 /var/www/html/
chown -R apache:apache /var/www/html/

# Check SELinux
ls -Z /var/www/html/
restorecon -Rv /var/www/html/
```

---

### ❌ 502 Bad Gateway

- Apache is up but **can't reach Tomcat behind it**
- Tomcat is down, or wrong port in ProxyPass config

```bash
# Is Tomcat running?
systemctl status tomcat
ps aux | grep tomcat

# Is port 8080 listening?
netstat -tulnp | grep :8080

# Check Tomcat logs
tail -f /opt/tomcat/logs/catalina.out
```

---

### ❌ 504 Gateway Timeout

- Apache reached Tomcat but Tomcat **took too long to respond**
- Java app is slow, stuck, or out of memory

```bash
# Check Tomcat for Java errors / OOM
tail -200 /opt/tomcat/logs/catalina.out | grep -i "error\|exception\|outofmemory"

# Check system memory
free -h
top
```

---

## 🔥 Troubleshooting Tomcat — Common Issues

### ❌ Tomcat Won't Start

```bash
# Always check this first
tail -100 /opt/tomcat/logs/catalina.out

# Port 8080 already in use?
netstat -tulnp | grep :8080

# Java not found?
java -version
echo $JAVA_HOME
```

---

### ❌ App Deployed But Returns 404

- The `.war` file deployed but app isn't mapped correctly

```bash
# Check if war was unpacked
ls /opt/tomcat/webapps/

# Check app-specific log
tail -f /opt/tomcat/logs/localhost.$(date +%Y-%m-%d).log
```

---

### ❌ Tomcat Out of Memory (OOM)

```bash
# Edit Tomcat startup to give more heap memory
# In /opt/tomcat/bin/setenv.sh (create if not exists)
export JAVA_OPTS="-Xms512m -Xmx2048m"

# Restart Tomcat
/opt/tomcat/bin/shutdown.sh
/opt/tomcat/bin/startup.sh
```

---

## 📊 Apache vs Tomcat — Quick Reference

| | Apache HTTP | Tomcat |
|---|---|---|
| **Type** | Web Server | App Server |
| **Serves** | Static files, proxy | Java apps (.war) |
| **Default Port** | 80 / 443 | 8080 |
| **Config file** | `httpd.conf` | `server.xml` |
| **Log to check** | `error_log` | `catalina.out` |
| **Start command** | `systemctl restart httpd` | `startup.sh` |

---

## 💼 Interview Tips — Apache & Tomcat Edition

### 🔥 Tip 1 — "User gets 502, what do you check?"
**Say this:** 502 means Apache can't reach the backend. I'd check if Tomcat is running, verify it's listening on port 8080, then check catalina.out for errors. Then verify the ProxyPass config points to the right port.
> Layer by layer answer — exactly what they want.

---

### 🔥 Tip 2 — "Where are Apache error logs?"
**Say this:** `/var/log/httpd/error_log` on RHEL/CentOS. For app-specific virtual hosts, they're wherever `ErrorLog` is defined in the VirtualHost config.
> Specific answer beats "uh, somewhere in /var/log."

---

### 🔥 Tip 3 — "What's the first thing you do before restarting Apache after a config change?"
**Say this:** Run `apachectl configtest` — it checks for syntax errors without restarting. If you restart with a broken config, Apache goes down completely.
> This shows you won't take prod down with a typo.

---

### 🔥 Tip 4 — "Tomcat is running but the app returns 404, why?"
**Say this:** Either the .war file wasn't deployed to the webapps directory, or it failed to unpack due to an error. I'd check the webapps folder and tail the localhost date log for deployment errors.

---

*Next up: Objective 6 — Jenkins & GitLab CI/CD Basics 🔥*
