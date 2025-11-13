# 🌐 LibreNMS — The Ultimate Open-Source Network Monitoring Platform

> **Monitor Everything. Automate Everything. See Everything.**

LibreNMS is a **feature-rich**, **auto-discovering**, **SNMP-based** network monitoring system that supports **thousands of device types** out of the box — from routers and switches to servers, firewalls, IoT, and optical network units (ONUs). Built for **ISPs, enterprises, and DevOps teams**, it’s the most powerful **free alternative** to enterprise monitoring tools.

---

## 📚 Table of Contents

- [Why LibreNMS?](#-why-librenms)
- [Supported Devices & Features](#-supported-devices--features)
- [Commercial Alternatives](#-commercial-alternatives)
- [Open-Source Alternatives](#-open-source-alternatives)
- [Installation Methods](#-installation-methods)
  - [Docker (Recommended)](#-docker-recommended)
  - [Post-Installation Setup](#-post-installation-setup)
  - [Native Linux Install](#-native-linux-install)
  - [Web Access & Admin User (Docker)](#-web-access--admin-user-docker)
  - [Essential Settings](#-essential-settings)
- [Adding Devices](#-adding-devices)
  - [SNMP-Based Devices](#snmp-based-devices)
  - [Linux/Windows Servers](#linuxwindows-servers)
  - [Auto-Discovery](#auto-discovery)
- [Advanced Usage & Operations](#-advanced-usage--operations)
- [Troubleshooting & Validation](#-troubleshooting--validation)

---

## ❓ Why LibreNMS?

- ✅ **100% Open-Source** (GPLv3)
- ✅ **Auto-Discovery** of devices via SNMP, ARP, CDP, LLDP
- ✅ **Real-time graphs** for bandwidth, CPU, memory, disk, optical power, temperature
- ✅ **Built-in alerting** (Email, Telegram, Slack, Webhook)
- ✅ **API-first design** — integrate with any tool
- ✅ **Role-based access control (RBAC)**
- ✅ **Docker & Kubernetes ready**
- ✅ **Actively maintained** by a global community

> 💡 Ideal for **ISPs, MSPs, Data Centers, Campus Networks, and Home Labs**.

---

## 📡 Supported Devices & Features

### ✅ Supported Device Types
LibreNMS supports **5,000+ device models** via automatic OS detection:

- **Routers & Switches**: Cisco, Juniper, MikroTik, Ubiquiti, HP, Aruba  
- **Firewalls**: Palo Alto, Fortinet, pfSense, OPNsense  
- **Wireless**: Ruckus, Meraki, Aruba APs  
- **Servers**: Linux, Windows (via SNMP/Agent)  
- **Optical Networks**: OLTs & ONUs (EPON/GPON) from major vendors  
- **UPS, Printers, IoT Sensors**, and more

### 🔧 Key Monitoring Features
| Feature | Description |
|--------|-------------|
| **SNMP Polling** | v1, v2c, v3 with encryption |
| **Syslog & SNMP Traps** | Real-time event ingestion |
| **Performance Graphs** | RRDtool-based, customizable |
| **Alerting Engine** | Rule-based with escalation |
| **Network Maps** | Automatic topology visualization |
| **Inventory Tracking** | Serial, model, OS version |
| **Bill of Materials** | Track hardware/software changes |
| **API & CLI** | Full automation support |

---

## 💼 Commercial Alternatives

| Tool | License | Notes |
|------|--------|-------|
| **SolarWinds NPM** | Paid | Expensive, Windows-only, overkill for small setups |
| **PRTG Network Monitor** | Freemium | Limited free version (100 sensors) |
| **ManageEngine OpManager** | Paid | Enterprise-focused, complex setup |
| **Cisco Prime** | Paid | Vendor-locked, Cisco-only |

> 🏆 **LibreNMS = All features of paid tools + zero cost + full control**

---

## 🆓 Open-Source Alternatives

| Tool | Strengths | Weaknesses |
|------|----------|-----------|
| **LibreNMS** | SNMP-native, auto-discovery, ONU support | Web UI learning curve |
| **Zabbix** | Server/app monitoring, flexible | Complex SNMP setup, no auto ONU maps |
| **Cacti** | Graph-focused | No alerting, outdated UI |
| **Observium** | Beautiful UI | Community version limited, CE is paid |
| **Prometheus + Grafana** | Metrics/time-series | Not SNMP-native, needs exporters |

> ✅ **LibreNMS is the best for pure network & optical device monitoring**

---

## ⚙️ Installation Methods

### 🐳 Docker (Recommended)

Based on: [LibreNMS Docker Docs](https://docs.librenms.org/Installation/Docker/)

```bash
# 1. Setup directory
mkdir ~/librenms && cd ~/librenms

# 2. Download official compose files
wget https://github.com/librenms/docker/archive/refs/heads/master.zip
unzip master.zip
cd docker-master/examples/compose

# 3. Edit .env (set secure MYSQL_PASSWORD)
nano .env

# 4. (Optional) Change host port (e.g., 8001 → 8080 in compose.yml)

# 5. Start services
sudo docker compose -f compose.yml up -d

# 6. Access Web UI: http://<server-ip>:8001
```

> 🔐 **Security Tip**: Always change default passwords in `.env` and web UI.

---

## 🛠️ Post-Installation Setup

### 🔐 Web Access & Admin User (Docker)

After first launch, **create a real admin user** (don’t use default):

```bash
# Create admin user in Docker container
docker compose exec --user librenms librenms \
  lnms user:add admin@example.com --role=admin --password=YourSecurePassword123

# (Optional) Delete default user
docker compose exec --user librenms librenms \
  lnms user:delete admin@librenms.local
```

> 🌐 Now log in at: `http://<ip>:8001` with your new credentials.

---

### 🖥️ Native Linux Install

Based on: [LibreNMS Install Guide](https://docs.librenms.org/Installation/Install-LibreNMS/)

**Supported OS**: Ubuntu 22.04/24.04, Debian 12, CentOS 9/Rocky 9  
**Requirements**: PHP 8.2+, MariaDB, Nginx/Apache

#### Quick Steps (Ubuntu):
```bash
# 1. Install packages
sudo apt install software-properties-common
LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y acl curl fping git nginx-full mariadb-server php8.3-cli php8.3-fpm php8.3-mysql php8.3-snmp rrdtool snmpd python3-pymysql

# 2. Create user & download
sudo useradd librenms -d /opt/librenms -M -r -s "$(which bash)"
sudo git clone https://github.com/librenms/librenms.git /opt/librenms

# 3. Set permissions
sudo chown -R librenms:librenms /opt/librenms
sudo chmod 771 /opt/librenms

# 4. Install PHP deps
sudo su - librenms
./scripts/composer_wrapper.php install --no-dev
exit

# 5. Configure MariaDB, PHP-FPM, Nginx (follow official guide)

# 6. Access: http://<server-ip>/install
```

---

### ⚙️ Essential Settings (Web UI)

1. **Global Settings → Poller Settings**
   - Set **Default SNMP Community** (e.g., `my_secure_string`)
   - Enable **Auto-Discover New Devices**

2. **Alerting → Email Transport**
   - Configure SMTP (Gmail, SendGrid, etc.)

3. **System → Updates**
   - Enable auto-update or manual

4. **Users → Roles**
   - Create viewer/admin roles as needed

---

## ➕ Adding Devices

### SNMP-Based Devices (Routers, Switches, ONUs, etc.)

1. **Devices → Add Device**
2. Enter:
   - **Hostname/IP**
   - **SNMP Version**: `v2c` or `v3`
   - **Community** (for v2c) or Auth/Privacy (for v3)
3. Click **Add Device**

> ✅ LibreNMS auto-detects OS, ports, sensors, and starts polling.

---

### Linux/Windows Servers

#### On Target Server:
```bash
# Ubuntu/Debian
sudo apt install snmpd
echo "rocommunity public" | sudo tee /etc/snmp/snmpd.conf
sudo systemctl restart snmpd
```

#### In LibreNMS:
- Add device with **SNMP v2c** + your community string  
- CPU, RAM, Disk, and Network will auto-appear

---

### Auto-Discovery

1. **Devices → Discovery**
2. Set:
   - **IP Range**: `192.168.1.0/24`
   - **SNMP Community**: `my_secure_string`
   - ✅ **Auto-Add Devices**
3. Click **Start Discovery**

> 🕒 Devices will appear within minutes.

---

## 🚀 Advanced Usage & Operations

| Task | Command / Method |
|------|------------------|
| **Run Poller Manually** | `docker compose exec librenms lnms poller` |
| **Add Custom Alert Rule** | `sensors_temperature > 70` |
| **Enable Syslog** | Ports 514/UDP open + configure in UI |
| **Backup** | `docker compose exec librenms lnms backup` |
| **API Access** | `http://<ip>/api/v0/` (generate token in UI) |
| **Update** | `docker compose pull && docker compose up -d` |

---

## 🛠️ Troubleshooting & Validation

### Docker:
```bash
# Check logs
docker compose logs librenms

# Test SNMP from container
docker compose exec librenms snmpwalk -v2c -c public 192.168.1.1 system
```

### Native Install:
```bash
sudo su - librenms
./validate.php
```

> ✅ `validate.php` checks DB, filesystem, PHP, and permissions.

---

## 🙌 Final Notes

- LibreNMS is **free forever** — no hidden costs.
- Perfect for **monitoring any SNMP-capable device**.
- Use **Docker for simplicity**, **Native for performance**.
- Always **secure SNMP** (avoid `public`, use v3 or ACLs).
- Join the **community** at [https://www.librenms.org](https://www.librenms.org)

> 🌟 **You’re not just installing a tool — you’re gaining full visibility into your entire network.**
```

---
