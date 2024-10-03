# Zabbix_Installation_Guide
This repo contains a step by step guide on how to install Zabbix on Debian/Ubuntu linux-based systems.

Here's a step-by-step guide on how to install Zabbix on **Debian 12**. Zabbix is a powerful open-source monitoring solution, and we will install it along with a MySQL database and the NGINX web server.

### **Step 1: Update System Packages**
Before you begin, it's essential to ensure that your system is up-to-date.

```bash
sudo apt update
sudo apt upgrade -y
```

### **Step 2: Install MySQL/MariaDB Database Server**
Zabbix uses a database to store data. Here, we’ll install MariaDB (an open-source MySQL fork).

```bash
sudo apt install mariadb-server mariadb-client -y
```

After installation, secure your MariaDB setup by running:

```bash
sudo mysql_secure_installation
```

Follow the prompts to set a root password and configure other security options.

### **Step 3: Create a Zabbix Database**
Log into MySQL as the root user:

```bash
sudo mysql -u root -p
```

Then create the Zabbix database and user:

```sql
CREATE DATABASE zabbix character set utf8mb4 collate utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Replace `'password'` with a strong password for the Zabbix database user.

### **Step 4: Install Zabbix Server and Agent**
Zabbix provides its own repository for installation. First, download and install the Zabbix repository:

```bash
 wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-2+debian12_all.deb
 dpkg -i zabbix-release_7.0-2+debian12_all.deb

```

Update the package list:

```bash
sudo apt update
```

Now, install the Zabbix server, web frontend, and agent:

```bash
 sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y 
```

### **Step 5: Import Zabbix Database Schema**
To set up the Zabbix database, import the default schema provided by Zabbix. Run the following command:

```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

Provide the `zabbix` database password when prompted.

### **Step 6: Configure the Zabbix Server**
Edit the Zabbix server configuration file to add the database connection details.

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

Find and set the following parameters:

```bash
DBName=zabbix
DBUser=zabbix
DBPassword=password
```

Replace `'password'` with the actual password you set earlier.

### **Step 7: Start and Enable Services**
Start and enable Zabbix and NGINX services to run on boot.

```bash
 sudo systemctl restart zabbix-server zabbix-agent apache2
 sudo systemctl enable zabbix-server zabbix-agent apache2
```

### **Step 8: Configure Firewall** (optional, this is when you're using ufw)
If you have a firewall running, allow HTTP and HTTPS traffic.

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### **Step 9: Access Zabbix Web Interface**
Open a browser and navigate to the following URL:

```
http://your_server_ip_or_domain/zabbix
```

You should see the Zabbix setup screen. Follow the on-screen instructions to complete the installation.

- **Default Username**: `Admin`
- **Default Password**: `zabbix`

You can change the admin password after logging in.

### **Step 10: Test and Verify Installation**
After logging in, verify that Zabbix is collecting data and that the web interface is functioning properly.

---

This guide covers the installation of Zabbix with a MySQL/MariaDB database, Apache2 web server, and the Zabbix agent on **Debian 12**. Let me know if you need further assistance!
