
# 🚀 WordPress + phpMyAdmin Setup on Azure Ubuntu 22.04

This guide walks you through deploying a WordPress website with phpMyAdmin access on an Ubuntu 22.04 VM hosted on Azure, using Apache and MySQL. It includes remote database access setup for Windows systems.

---

## 🌐 Subdomain Setup

- **Domain:** luxcapenew.thinksurfmedia.in
- **DNS:** A record should point to your Azure VM's public IP

---

## 📌 Prerequisites

- Ubuntu 22.04 VM on Azure
- Apache, MySQL, PHP (LAMP stack)
- Firewall ports open: `80`, `443`, `3306` (for remote DB access)

---

## ⚙️ Step 0: Install Apache, PHP, and Dependencies (LAMP Stack)

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 -y
sudo apt install php libapache2-mod-php php-mysql php-cli php-common php-mbstring php-xml php-curl php-zip php-gd php-bcmath php-json unzip -y
sudo systemctl enable apache2
sudo systemctl start apache2
php -v
```

---

## 🐬 Step 1: Create WordPress MySQL Database & User

```bash
sudo mysql -u root -p
```

In MySQL shell:

```sql
CREATE DATABASE wp_luxcape;
CREATE USER 'wpuser_luxcape'@'localhost' IDENTIFIED BY 'India@123';
GRANT ALL PRIVILEGES ON wp_luxcape.* TO 'wpuser_luxcape'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 📦 Step 2: Download and Configure WordPress

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

## 🌍 Step 3: Apache Virtual Host for WordPress

```bash
sudo nano /etc/apache2/sites-available/luxcapenew.thinksurfmedia.in.conf
```

Paste:

```apacheconf
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

Then:

```bash
sudo a2ensite luxcapenew.thinksurfmedia.in.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

---

## 🔐 Step 4: HTTPS with Let's Encrypt (Optional)

```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d luxcapenew.thinksurfmedia.in
```

---

## 🌐 Step 5: Enable Remote MySQL Access

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

Then:

```bash
sudo systemctl restart mysql
```

---

## 🧑‍💻 Step 6: Create Global Remote MySQL User

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

## 🔥 Step 7: Open Firewall (UFW & Azure NSG)

```bash
sudo ufw allow 3306/tcp
sudo ufw reload
```

> In Azure NSG:  
Add Inbound Rule → Port: `3306`, Source: `your IP`, Action: `Allow`

---

## 🛡 Step 8: Install phpMyAdmin

```bash
sudo apt update
sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl -y
sudo phpenmod mbstring
sudo systemctl restart apache2
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
sudo systemctl restart apache2
```

---

## 🌐 Step 9: Access URLs

- WordPress: https://luxcapenew.thinksurfmedia.in  
- phpMyAdmin: https://luxcapenew.thinksurfmedia.in/phpmyadmin  

Remote MySQL via Workbench:
- Host: `luxcapenew.thinksurfmedia.in`
- Port: `3306`
- User: `adminremote`
- Pass: `StrongPassword@123`

---

## 🧩 Step 10: Increase PHP Upload Limits

Edit PHP config:

```bash
sudo nano /etc/php/8.x/apache2/php.ini  # Replace 8.x with your PHP version
```

Update values:

```ini
upload_max_filesize = 200M
post_max_size = 200M
memory_limit = 256M
max_execution_time = 300
max_input_time = 300
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

## 🔒 Optional: Secure phpMyAdmin with Password

```bash
sudo apt install apache2-utils
sudo htpasswd -c /etc/phpmyadmin/.htpasswd admin
```

Edit config:

```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

Inside `<Directory>` block, add:

```apacheconf
AuthType Basic
AuthName "Restricted Access"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user
```

Then:

```bash
sudo systemctl reload apache2
```

---

✅ **You're all set!**
