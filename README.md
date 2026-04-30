# Splunk-SOC-Lab-DVWA-Attack-Detection
webapplication attack detection using splunk 

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
Payload:
```spl ' OR '1'='1 ```
*Observe manipulated query results.
*Confirm entry in /var/log/apache2/access.log.
