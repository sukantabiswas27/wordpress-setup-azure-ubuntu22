
# 🚀 WordPress + phpMyAdmin Setup on Azure Ubuntu 22.04

This guide walks you through deploying a WordPress website with phpMyAdmin access on an Ubuntu 22.04 VM hosted on Azure, using Apache and MySQL. It includes remote database access setup for Windows systems.

---

## 🌐 Subdomain Setup: `luxcapenew.thinksurfmedia.in`

### 📌 Prerequisites
- Ubuntu 22.04 VM on Azure
- Subdomain DNS (A record) pointed to Azure VM IP
- Apache, MySQL, PHP installed (LAMP stack)
- Firewall rules open (port 80, 443, 3306 if remote DB access needed)

---

## 1️⃣ Create WordPress MySQL Database & User

```bash
sudo mysql -u root -p
```

Inside MySQL:
```sql
CREATE DATABASE wp_luxcape;
CREATE USER 'wpuser_luxcape'@'localhost' IDENTIFIED BY 'India@123';
GRANT ALL PRIVILEGES ON wp_luxcape.* TO 'wpuser_luxcape'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 2️⃣ Download and Configure WordPress

```bash
cd /var/www/
sudo curl -O https://wordpress.org/latest.zip
sudo apt install unzip -y
sudo unzip latest.zip
sudo mv wordpress luxcapenew.thinksurfmedia.in
sudo chown -R www-data:www-data /var/www/luxcapenew.thinksurfmedia.in
sudo chmod -R 755 /var/www/luxcapenew.thinksurfmedia.in
```

---

## 3️⃣ Apache Virtual Host for WordPress

```bash
sudo nano /etc/apache2/sites-available/luxcapenew.thinksurfmedia.in.conf
```

Add:
```apache
<VirtualHost *:80>
    ServerAdmin admin@thinksurfmedia.in
    ServerName luxcapenew.thinksurfmedia.in
    DocumentRoot /var/www/luxcapenew.thinksurfmedia.in

    <Directory /var/www/luxcapenew.thinksurfmedia.in>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/luxcapenew_error.log
    CustomLog ${APACHE_LOG_DIR}/luxcapenew_access.log combined
</VirtualHost>
```

Enable site:
```bash
sudo a2ensite luxcapenew.thinksurfmedia.in.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

---

## 4️⃣ (Optional) HTTPS with Let's Encrypt

```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d luxcapenew.thinksurfmedia.in
```

---

## 5️⃣ Enable Remote MySQL Access

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Change:
```
bind-address = 127.0.0.1
```
To:
```
bind-address = 0.0.0.0
```

Restart MySQL:
```bash
sudo systemctl restart mysql
```

---

## 6️⃣ Create Global Remote User

```bash
sudo mysql -u root -p
```

```sql
CREATE USER 'adminremote'@'%' IDENTIFIED BY 'StrongPassword@123';
GRANT ALL PRIVILEGES ON *.* TO 'adminremote'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

---

## 7️⃣ Open Firewall (UFW & Azure NSG)

```bash
sudo ufw allow 3306/tcp
sudo ufw reload
```

Azure NSG:
- Add inbound rule: TCP 3306, Source: your IP, Action: Allow

---

## 8️⃣ Install phpMyAdmin

```bash
sudo apt update
sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl -y
sudo phpenmod mbstring
sudo systemctl restart apache2
```

Link it:
```bash
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
sudo systemctl restart apache2
```

---

## 9️⃣ Access URLs

- WordPress: `https://luxcapenew.thinksurfmedia.in`
- phpMyAdmin: `https://luxcapenew.thinksurfmedia.in/phpmyadmin`
- Remote DB via MySQL Workbench:
  - Host: `luxcapenew.thinksurfmedia.in`
  - Port: `3306`
  - User: `adminremote`
  - Pass: `StrongPassword@123`

---
##🧩 Step 10 : Increase PHP Upload Limits
Edit your PHP config file (usually located at /etc/php/8.x/apache2/php.ini — replace 8.x with your actual PHP version).

```bash
sudo nano /etc/php/8.x/apache2/php.ini
```
## Update these values:
```
upload_max_filesize = 200M
post_max_size = 200M
memory_limit = 256M
max_execution_time = 300
max_input_time = 300

```
## Then restart Apache:
## 🔒 Optional: Secure phpMyAdmin

```bash
sudo apt install apache2-utils
sudo htpasswd -c /etc/phpmyadmin/.htpasswd admin
```

Then edit:
```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

Add inside `<Directory>` block:

```apache
AuthType Basic
AuthName "Restricted Access"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user
```

Reload Apache:
```bash
sudo systemctl reload apache2
```

---

✅ You’re all set!
