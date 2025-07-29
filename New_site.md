
# WordPress Multisite Setup on Apache (Ubuntu 22.04) - Site 2

## 🔧 Site Details
- **Domain/Subdomain**: `test.thinksurfmedia.in`
- **DocumentRoot**: `/var/www/test.thinksurfmedia.in`
- **Database Name**: `wp_test`
- **Database User**: `wpuser_test`
- **phpMyAdmin Path**: `/var/www/test.thinksurfmedia.in/phpmyadmin`

---

## 📁 Step 1: Create Directory for WordPress Site

```bash
sudo mkdir -p /var/www/test.thinksurfmedia.in
```

## ⬇️ Step 2: Download and Move WordPress Files

```bash
cd /tmp
wget https://wordpress.org/latest.zip
unzip latest.zip
sudo mv wordpress/* /var/www/test.thinksurfmedia.in/
```

### Fix Permissions
```bash
sudo chown -R www-data:www-data /var/www/test.thinksurfmedia.in
sudo chmod -R 755 /var/www/test.thinksurfmedia.in
```

---

## 🗃️ Step 3: Create MySQL Database and User

Login to MySQL:
```bash
sudo mysql -u root -p
```

Run the following SQL:
```sql
CREATE DATABASE wp_test;
CREATE USER 'wpuser_test'@'localhost' IDENTIFIED BY 'StrongPassword123!';
GRANT ALL PRIVILEGES ON wp_test.* TO 'wpuser_test'@'localhost';
FLUSH PRIVILEGES;
```

---

## 🌐 Step 4: Configure Apache Virtual Host

```bash
sudo nano /etc/apache2/sites-available/test.thinksurfmedia.in.conf
```

Paste this config:

```apache
<VirtualHost *:80>
    ServerAdmin admin@thinksurfmedia.in
    ServerName test.thinksurfmedia.in
    DocumentRoot /var/www/test.thinksurfmedia.in

    <Directory /var/www/test.thinksurfmedia.in>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/test_error.log
    CustomLog ${APACHE_LOG_DIR}/test_access.log combined
</VirtualHost>
```

Save and exit.

---

## ✅ Step 5: Enable Site and Reload Apache

```bash
sudo a2ensite test.thinksurfmedia.in.conf
sudo systemctl reload apache2
```

---

## 🌍 Step 6: Add DNS Record

Go to your DNS provider and create:

- **Type**: A Record
- **Host**: `test`
- **Points To**: Your Server's Public IP

---

## 🛠️ Step 7 (Optional): Enable phpMyAdmin Access

```bash
sudo ln -s /usr/share/phpmyadmin /var/www/test.thinksurfmedia.in/phpmyadmin
```

---

## 🧠 Step 8: Install WordPress from Browser

Go to:
```
http://test.thinksurfmedia.in
```
Follow the WordPress installation wizard.

---

✅ All Done!
