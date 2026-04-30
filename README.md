# DVWA Attack Simulation & Splunk Detection

## 📌 Overview
This repository demonstrates how to simulate **web application attacks** (Cross‑Site Scripting and SQL Injection) in a controlled lab using **Damn Vulnerable Web Application (DVWA)** on Kali Linux, and detect them using **Splunk Enterprise**.  

It showcases a SOC workflow: **attack → log → detection → dashboard → evidence**.  
The goal is to provide recruiter‑ready documentation of security monitoring skills.

---

## ⚙️ Environment Setup

### 1. Virtual Machines
- **Kali Linux**: Attacker machine + Splunk host
- **DVWA**: Installed on Apache2/PHP/MySQL stack

### 2. DVWA Setup
- Configure database via `setup.php`
- Enable vulnerabilities (XSS, SQL Injection, SQLi Blind)

### 3. Logging
- Apache logs stored at `/var/log/apache2/access.log`
- Verified with:
  ```bash
  tail -f /var/log/apache2/access.log


# 🧩 Step‑by‑Step Methods

1 Configure DVWA on Kali (Apache + PHP + MariaDB) 
Update packages:
```spl
sudo apt update && sudo apt upgrade -y
```
Install Apache, PHP, and MariaDB:
```spl
sudo apt install apache2 php php-mysqli mariadb-server git -y
```
Clone DVWA:
```spl
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chown -R www-data:www-data DVWA
```
Configure DVWA database:
```spl
sudo mysql -u root -p
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
```
Edit DVWA config file:
```spl
sudo nano /var/www/html/DVWA/config/config.inc.php
Set DB user/password accordingly.
```
Restart Apache:
```spl
sudo systemctl restart apache2
```


2 Simulate Web Attacks
*Navigate to: http://<kali-ip>/DVWA/vulnerabilities/xss_r/

Payload:
```spl
html
<script>alert('XSS')</script>
```
*Observe alert popup in browser.
*Confirm entry in /var/log/apache2/access.log.

SQL Injection
*Navigate to: http://<kali-ip>/DVWA/vulnerabilities/sqli/
```spl 
Payload:
' OR '1'='1 ```
```
*Observe manipulated query results.
*Confirm entry in /var/log/apache2/access.log.


3️ Configure Splunk to Monitor Local Logs
Since Splunk Enterprise is running on Kali:

1 Add data source via Splunk Web:
- Settings → Add Data → Files & Directories
- Path: /var/log/apache2/access.log
- Sourcetype: access_combined
- Index: dvwa_log
2 Save and start indexing.


4️ Detection in Splunk
XSS Detection SPL
```spl
index=dvwa_log ("<script>" OR "%3Cscript%3E" OR "onerror" OR "alert(" OR "prompt(")
```
SQL Injection Detection SPL
```spl
index=dvwa_log ("' OR '1'='1" OR "UNION SELECT" OR "SLEEP(" OR "updatexml(" OR "ASCII(")
```
- Run queries in Splunk Search.
- Verify detection events (payload → log entry → Splunk match).


6️ Dashboard Visualization
Create Splunk dashboard with panels for XSS and SQLi queries.
