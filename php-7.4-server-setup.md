
# 🐘 PHP 7.4 Server Setup Guide (Ubuntu)

This guide provides step-by-step instructions for installing **PHP 7.4**, **Apache2**, **phpMyAdmin**, and **MySQL Server** on an Ubuntu server.

---

## 🛠 Step 1: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 📦 Step 2: Add PHP 7.4 Repository

```bash
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
```

---

## 📥 Step 3: Install PHP 7.4 and Common Modules

```bash
sudo apt install php7.4 php7.4-cli php7.4-common php7.4-mbstring php7.4-xml php7.4-mysql php7.4-curl php7.4-zip php7.4-gd php7.4-bcmath php7.4-json -y
php -v
```

---

## 🌐 Step 4: Install Apache2

```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

---

## ⚙️ Step 5: Configure Apache to Use PHP 7.4

```bash
sudo apt install libapache2-mod-php7.4 -y
sudo a2enmod php7.4
sudo systemctl restart apache2
```

---

## 🛡 Step 6: Install phpMyAdmin (Compatible with PHP 7.4)

```bash
cd /tmp
wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-all-languages.tar.gz
tar xzf phpMyAdmin-5.1.1-all-languages.tar.gz
sudo mv phpMyAdmin-5.1.1-all-languages /usr/share/phpmyadmin

sudo mkdir /usr/share/phpmyadmin/tmp
sudo chown -R www-data:www-data /usr/share/phpmyadmin
sudo chmod 777 /usr/share/phpmyadmin/tmp
```

### 🔧 Create Apache Config for phpMyAdmin

```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

Add the following:

```apacheconf
Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

```bash
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

Visit: `http://your-server-ip/phpmyadmin`

---

## 🐬 Step-by-Step: Install MySQL Server

```bash
sudo apt update
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

### ✔️ Secure MySQL Installation Prompts

- Set root password → Yes  
- Remove anonymous users → Yes  
- Disallow root login remotely → Yes  
- Remove test database → Yes  
- Reload privilege tables → Yes  

---

## 🔎 Check MySQL Status

```bash
sudo systemctl status mysql
sudo systemctl start mysql
sudo systemctl enable mysql
```

---

## 🔐 Login to MySQL

```bash
sudo mysql -u root -p
```

---

## 🧩 Change MySQL Root Authentication (Optional)

```bash
sudo mysql
```

Then run:

```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'root';

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_new_password';
FLUSH PRIVILEGES;
EXIT;
```

```bash
sudo systemctl restart mysql
```

---

> 🛠 Maintained by Thinksurfmedia DevOps Team
