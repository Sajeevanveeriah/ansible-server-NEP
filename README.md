# Ansible Web Server Automation

> **Technical Homework - Broadcast Engineer - NEP Australia**

An enterprise-ready Ansible automation project for deploying and configuring web servers (Apache2 or Nginx) on Ubuntu/Debian hosts with firewall configuration, custom landing pages, and complete infrastructure-as-code practices.

[![Ansible](https://img.shields.io/badge/Ansible-2.9%2B-red.svg)](https://www.ansible.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-20.04%20%7C%2022.04-orange.svg)](https://ubuntu.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Adding New Hosts](#adding-new-hosts)
- [Verification](#verification)
- [Troubleshooting](#troubleshooting)
- [Advanced Usage](#advanced-usage)
- [Contributing](#contributing)

---

## 🎯 Overview

This Ansible automation project provides a production-ready solution for:
- **Automated web server deployment** (Nginx or Apache2)
- **Firewall configuration** using UFW (allow HTTP/HTTPS)
- **Custom HTML landing page** deployment with responsive design
- **Service management** with handlers for restarts/reloads
- **Multi-host support** with inventory management

**Use Cases:**
- Rapid deployment of web server infrastructure
- Consistent configuration across multiple environments
- Automated server provisioning for development/staging/production
- Infrastructure-as-code practices for web hosting platforms

---

## ✨ Features

### Core Functionality
- ✅ **Multi-server deployment** - Deploy to single or multiple hosts simultaneously
- ✅ **Web server choice** - Support for Nginx (default) or Apache2
- ✅ **Firewall automation** - UFW configuration with HTTP (80), HTTPS (443), and SSH (22)
- ✅ **Custom landing page** - Professional HTML page with system information
- ✅ **Service management** - Automatic start, enable, and health checks
- ✅ **Idempotent operations** - Safe to run multiple times

### Technical Features
- 🔧 **Ansible best practices** - Uses handlers, tags, and proper task organization
- 🔧 **Responsive design** - Mobile-friendly HTML landing page
- 🔧 **Dynamic content** - Landing page shows hostname, IP, server type, timestamp
- 🔧 **SSH key-based authentication** - Secure, password-less connections
- 🔧 **Group variables** - Flexible configuration per host group

---

## 📁 Project Structure

```
ansible-webserver-automation/
├── ansible.cfg              # Ansible configuration (SSH, defaults)
├── inventory.ini            # Host inventory with group variables
├── webserver.yml           # Main playbook for web server deployment
├── files/
│   └── index.html          # Custom HTML landing page template
├── README.md               # This documentation
└── .gitignore              # Git ignore rules
```

### File Descriptions

| File | Purpose |
|------|---------|
| `ansible.cfg` | Ansible configuration: inventory path, SSH settings, privilege escalation |
| `inventory.ini` | Defines target hosts, groups, and variables (web_server type, ports) |
| `webserver.yml` | Main playbook: installs web server, configures firewall, deploys HTML |
| `files/index.html` | Jinja2 template with dynamic system info and responsive CSS |

---

## 🔧 Prerequisites

### 1. Control Node (Your Machine)

**Install Ansible:**

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install ansible -y

# macOS (using Homebrew)
brew install ansible

# Python pip (any OS)
pip install ansible

# Verify installation
ansible --version
```

**Required:** Ansible 2.9 or higher

### 2. Target Hosts (Web Servers)

**Operating System:**
- Ubuntu 20.04 LTS, 22.04 LTS, or Debian 10/11

**Requirements:**
- Python 3 installed (`sudo apt install python3 -y`)
- SSH access enabled
- User account with sudo privileges

### 3. SSH Key Setup

**Generate SSH key (if not exists):**

```bash
# Generate RSA key pair
ssh-keygen -t rsa -b 4096 -C "ansible@automation"

# Default location: ~/.ssh/id_rsa
```

**Copy SSH key to target hosts:**

```bash
# Replace USER and HOST_IP with actual values
ssh-copy-id ansible@10.0.0.10
ssh-copy-id ansible@10.0.0.11

# Test SSH connection (should not prompt for password)
ssh ansible@10.0.0.10
```

### 4. Ansible Collection (for UFW module)

```bash
# Install community.general collection for UFW support
ansible-galaxy collection install community.general
```

---

## 🚀 Installation

### 1. Clone Repository

```bash
git clone <your-repo-url>
cd ansible-webserver-automation
```

### 2. Verify File Structure

```bash
tree
# Should match the structure shown in "Project Structure" section
```

### 3. Test Ansible Configuration

```bash
# Verify Ansible can read configuration
ansible --version

# Should show inventory = inventory.ini
```

---

## ⚙️ Configuration

### 1. Edit Inventory File

Open `inventory.ini` and configure your hosts:

```ini
[webservers]
web1 ansible_host=10.0.0.10 ansible_user=ansible
web2 ansible_host=10.0.0.11 ansible_user=ansible

[webservers:vars]
web_server=nginx          # Options: nginx or apache2
http_port=80
https_port=443
ansible_python_interpreter=/usr/bin/python3
```

**Key Variables:**
- `ansible_host` - IP address of target server
- `ansible_user` - SSH user (must have sudo privileges)
- `web_server` - Choose `nginx` (default) or `apache2`
- `http_port` / `https_port` - Custom ports (default 80/443)

### 2. Customize Landing Page (Optional)

Edit `files/index.html` to modify the HTML template. Uses Jinja2 variables:
- `{{ ansible_hostname }}` - Server hostname
- `{{ ansible_default_ipv4.address }}` - Server IP address
- `{{ web_server }}` - Web server type (nginx/apache2)
- `{{ ansible_date_time.date }}` - Deployment date

---

## 🎯 Usage

### Basic Commands

#### 1. Test Connectivity

```bash
# Ping all hosts in webservers group
ansible webservers -m ping

# Expected output:
# web1 | SUCCESS => { "ping": "pong" }
# web2 | SUCCESS => { "ping": "pong" }
```

#### 2. Dry Run (Check Mode)

```bash
# Run playbook without making changes
ansible-playbook webserver.yml --check

# Shows what would be changed
```

#### 3. Run Full Deployment

```bash
# Deploy web servers to all hosts
ansible-playbook webserver.yml

# With verbose output
ansible-playbook webserver.yml -v

# Extra verbose (shows task details)
ansible-playbook webserver.yml -vvv
```

#### 4. Deploy to Specific Host

```bash
# Target single host
ansible-playbook webserver.yml --limit web1

# Target multiple specific hosts
ansible-playbook webserver.yml --limit web1,web2
```

#### 5. Use Tags (Partial Execution)

```bash
# Only run firewall configuration
ansible-playbook webserver.yml --tags firewall

# Only deploy HTML content
ansible-playbook webserver.yml --tags content

# Skip firewall configuration
ansible-playbook webserver.yml --skip-tags firewall
```

### Example Output

```
PLAY [Configure Web Servers] ***********************************

TASK [Gathering Facts] *****************************************
ok: [web1]
ok: [web2]

TASK [Update apt cache] ****************************************
changed: [web1]
changed: [web2]

TASK [Install web server package] ******************************
changed: [web1]
changed: [web2]

...

PLAY RECAP *****************************************************
web1 : ok=12 changed=8  unreachable=0  failed=0  skipped=0
web2 : ok=12 changed=8  unreachable=0  failed=0  skipped=0
```

---

## ➕ Adding New Hosts

### Method 1: Edit Inventory File

```bash
# Open inventory.ini
vim inventory.ini

# Add new host under [webservers]
web3 ansible_host=10.0.0.12 ansible_user=ansible
web4 ansible_host=10.0.0.13 ansible_user=ansible
```

### Method 2: Create New Group

```ini
# For different environments
[webservers_production]
prod1 ansible_host=192.168.1.10 ansible_user=ansible

[webservers_production:vars]
web_server=nginx
http_port=80

[webservers_staging]
staging1 ansible_host=10.0.0.20 ansible_user=ansible

[webservers_staging:vars]
web_server=apache2
http_port=8080
```

### Method 3: Dynamic Inventory (Advanced)

```bash
# Run playbook targeting new group
ansible-playbook webserver.yml --limit webservers_production
```

---

## ✅ Verification

### 1. Check Service Status

```bash
# Check if web server is running on all hosts
ansible webservers -m shell -a "systemctl status nginx"

# Or for Apache2
ansible webservers -m shell -a "systemctl status apache2"
```

### 2. Test HTTP Access

```bash
# Test from control node
curl http://10.0.0.10
curl http://10.0.0.11

# Should return HTML landing page
```

### 3. Verify Firewall Rules

```bash
# Check UFW status
ansible webservers -m shell -a "sudo ufw status"

# Expected output shows:
# 22/tcp    ALLOW
# 80/tcp    ALLOW
# 443/tcp   ALLOW
```

### 4. Access Landing Page

Open browser and navigate to:
- `http://10.0.0.10`
- `http://10.0.0.11`

**You should see:**
- Server hostname
- IP address
- Web server type (Nginx/Apache2)
- Deployment timestamp
- System information (OS, kernel, memory, CPU)

---

## 🐛 Troubleshooting

### Issue: SSH Connection Failed

**Error:** `Failed to connect to the host via ssh`

**Solution:**
```bash
# Test SSH manually
ssh ansible@10.0.0.10

# Copy SSH key again
ssh-copy-id ansible@10.0.0.10

# Check SSH config
cat ~/.ssh/config
```

### Issue: Permission Denied (Sudo)

**Error:** `FAILED! ... is not in the sudoers file`

**Solution:**
```bash
# SSH into host and add user to sudo group
ssh ansible@10.0.0.10
sudo usermod -aG sudo ansible

# Or edit sudoers file
sudo visudo
# Add: ansible ALL=(ALL) NOPASSWD:ALL
```

### Issue: UFW Module Not Found

**Error:** `The module community.general.ufw was not found`

**Solution:**
```bash
# Install community.general collection
ansible-galaxy collection install community.general

# Verify installation
ansible-galaxy collection list
```

### Issue: Web Server Not Accessible

**Check:**
```bash
# 1. Verify service is running
ansible webservers -m shell -a "systemctl status nginx"

# 2. Check firewall rules
ansible webservers -m shell -a "sudo ufw status"

# 3. Test local connection on server
ansible webservers -m shell -a "curl -I localhost"

# 4. Check if port is listening
ansible webservers -m shell -a "netstat -tulpn | grep :80"
```

---

## 🚀 Advanced Usage

### Change Web Server Type

```bash
# Edit inventory.ini and change:
web_server=apache2

# Or override at runtime:
ansible-playbook webserver.yml -e "web_server=apache2"
```

### Use Custom Ports

```bash
# Override HTTP/HTTPS ports
ansible-playbook webserver.yml -e "http_port=8080 https_port=8443"
```

### Run Playbook with Different User

```bash
# Use different SSH user
ansible-playbook webserver.yml -u ubuntu

# Or specify in command
ansible-playbook webserver.yml --user ubuntu --become
```

### Ansible Vault (Secure Secrets)

```bash
# Encrypt sensitive variables
ansible-vault create secrets.yml

# Add to playbook
ansible-playbook webserver.yml --ask-vault-pass
```

---

## 📊 Project Statistics

- **Playbook Tasks:** 14
- **Handlers:** 3
- **Supported OS:** Ubuntu 20.04+, Debian 10+
- **Web Servers:** Nginx, Apache2
- **Execution Time:** ~2-5 minutes (depending on network speed)

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Sajeevan Veeriah - NEP Australia**

- Project: Ansible Web Server Automation
- Date: 2025-11-17
- Version: 1.0.0

---

## 🙏 Acknowledgments

- [Ansible Documentation](https://docs.ansible.com/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Apache2 Documentation](https://httpd.apache.org/docs/)
- [UFW - Uncomplicated Firewall](https://help.ubuntu.com/community/UFW)

---

## 📞 Support

For issues or questions:
1. Check the issues section
2. Review Ansible logs: `./ansible.log` (if enabled)
3. Open an issue in the repository

---
