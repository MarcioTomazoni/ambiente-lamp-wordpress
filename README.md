
# 🧱 Ambiente LAMP com WordPress e PHP puro no Ubuntu MATE

Este repositório documenta passo a passo a criação de um ambiente LAMP local com suporte a múltiplos projetos no Ubuntu MATE. Ideal para testes, desenvolvimento e futuras migrações para Docker ou nuvem.

---

## 📦 Como clonar este repositório

```bash
git clone https://github.com/MarcioTomazoni/ambiente-lamp-wordpress.git
cd ambiente-lamp-wordpress
```

---

## ✅ Etapas de instalação do ambiente LAMP

### 1. Atualizar o sistema
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Instalar Apache
```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
```

### 3. Instalar MySQL
```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

### 4. Instalar PHP e módulos
```bash
sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-xml php-mbstring -y
```

### 5. Instalar phpMyAdmin
```bash
sudo apt install phpmyadmin -y
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

---

## ⚠️ Problema comum: acesso negado no phpMyAdmin (usuário root)

Se você encontrar este erro:

```
mysqli::real_connect(): (HY000/1698): Access denied for user 'root'@'localhost'
```

### 🔐 Solução recomendada: criar novo usuário para o phpMyAdmin
```bash
sudo mysql
```
```sql
CREATE USER 'nome_usuario'@'localhost' IDENTIFIED BY 'senha_forte';
GRANT ALL PRIVILEGES ON *.* TO 'nome_usuario'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

---

## 🗂️ Organização dos projetos

Estrutura de diretórios:
```bash
/var/www/projetos/
├── wp001/         → WordPress acessível em wp001.local
├── wp002/         → WordPress acessível em wp002.local
└── php001/        → Projeto PHP puro acessível em php001.local
```

---

## 📝 Projeto `wp001.local` - Site WordPress principal

### Criar diretório
```bash
sudo mkdir -p /var/www/projetos/wp001
sudo chown -R $USER:$USER /var/www/projetos/wp001
```

### Baixar WordPress
```bash
cd ~/Downloads
wget https://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
mv wordpress/* /var/www/projetos/wp001
```

### Permissões
```bash
sudo chown -R www-data:www-data /var/www/projetos/wp001
sudo chmod -R 755 /var/www/projetos/wp001
```

### Criar Virtual Host
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

## 📝 Projeto `wp002.local` - Segundo site WordPress

Repita as mesmas etapas do `wp001`, alterando o nome da pasta, banco e domínio para `wp002`.

---

## 💡 Projeto `php001.local` - Projeto PHP puro

### Criar diretório e arquivo de teste
```bash
sudo mkdir -p /var/www/projetos/php001
echo "<?php phpinfo(); ?>" | sudo tee /var/www/projetos/php001/index.php
sudo chown -R www-data:www-data /var/www/projetos/php001
```

### Criar Virtual Host
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

## 🖥️ Entradas no `/etc/hosts`

```plaintext
127.0.0.1    wp001.local
127.0.0.1    wp002.local
127.0.0.1    php001.local
```

---

## 🧭 Em breve:

- 🔁 Script de automação para novos projetos
- 🐳 Versão com Docker e Docker Compose
- ☁️ Migração para a nuvem (Google Cloud / Lightsail)

---

## ✍️ Autor

Documentação criada por **Marcio Tomazoni**  
📍 Ubuntu MATE + Apache + MySQL + PHP + WordPress  
