
# 🧱 LAMP Environment with WordPress and Pure PHP on Ubuntu MATE

This repository documents step-by-step how to create a local LAMP environment supporting multiple projects on Ubuntu MATE. Ideal for testing, development, and future migrations to Docker or cloud platforms.

---

## 📦 How to clone this repository

```bash
git clone https://github.com/MarcioTomazoni/ambiente-lamp-wordpress.git
cd ambiente-lamp-wordpress
```

---

## ✅ LAMP Environment Installation Steps

### 1. Update the system
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Apache
```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
```

### 3. Install MySQL
```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

### 4. Install PHP and modules
```bash
sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-xml php-mbstring -y
```

### 5. Install phpMyAdmin
```bash
sudo apt install phpmyadmin -y
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

---

## ⚠️ Common Issue: phpMyAdmin access denied (root user)

If you encounter this error:

```
mysqli::real_connect(): (HY000/1698): Access denied for user 'root'@'localhost'
```

### 🔐 Recommended solution: create a new user for phpMyAdmin
```bash
sudo mysql
```
```sql
CREATE USER 'your_user'@'localhost' IDENTIFIED BY 'strong_password';
GRANT ALL PRIVILEGES ON *.* TO 'your_user'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

---

## 🗂️ Project Directory Structure

```bash
/var/www/projetos/
├── wp001/         → WordPress site accessible at wp001.local
├── wp002/         → WordPress site accessible at wp002.local
└── php001/        → Pure PHP project accessible at php001.local
```

---

## 📝 Project `wp001.local` - Main WordPress Site

### Create directory
```bash
sudo mkdir -p /var/www/projetos/wp001
sudo chown -R $USER:$USER /var/www/projetos/wp001
```

### Download WordPress
```bash
cd ~/Downloads
wget https://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
mv wordpress/* /var/www/projetos/wp001
```

### Set permissions
```bash
sudo chown -R www-data:www-data /var/www/projetos/wp001
sudo chmod -R 755 /var/www/projetos/wp001
```

### Create Virtual Host
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName wp001.local
    DocumentRoot /var/www/projetos/wp001

    <Directory /var/www/projetos/wp001>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/wp001_error.log
    CustomLog ${APACHE_LOG_DIR}/wp001_access.log combined
</VirtualHost>
```

---

## 📝 Project `wp002.local` - Second WordPress Site

Repeat the same steps from `wp001`, changing the folder name, database, and domain to `wp002`.

---

## 💡 Project `php001.local` - Pure PHP

### Create directory and test file
```bash
sudo mkdir -p /var/www/projetos/php001
echo "<?php phpinfo(); ?>" | sudo tee /var/www/projetos/php001/index.php
sudo chown -R www-data:www-data /var/www/projetos/php001
```

### Create Virtual Host
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName php001.local
    DocumentRoot /var/www/projetos/php001

    <Directory /var/www/projetos/php001>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/php001_error.log
    CustomLog ${APACHE_LOG_DIR}/php001_access.log combined
</VirtualHost>
```

---

## 🖥️ Hosts file entries

```plaintext
127.0.0.1    wp001.local
127.0.0.1    wp002.local
127.0.0.1    php001.local
```

---

## 🧭 Coming soon:

- 🔁 Automation script for new projects
- 🐳 Docker & Docker Compose version
- ☁️ Cloud migration (Google Cloud / Lightsail)

---

## ✍️ Author

Documentation by **Márcio Tomazoni**  
📍 Ubuntu MATE + Apache + MySQL + PHP + WordPress  
