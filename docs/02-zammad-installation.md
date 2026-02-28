# 02 - Zammad Helpdesk Installation (Ubuntu)

## Objective

Install and configure the Zammad helpdesk platform on the Ubuntu Server VM to serve as the centralized ticketing system integrated with Active Directory.

---

## Environment

- Host OS: Ubuntu Server 22.04 LTS
- Hypervisor: Proxmox VE
- Hostname: helpdesk1
- Static IP: (Your IP here)
- Domain: sarifim.local

---

## Repository Configuration

Added official Zammad repository:

```bash
curl -fsSL https://dl.packager.io/srv/zammad/zammad/key | sudo gpg --dearmor -o /usr/share/keyrings/zammad.gpg
```

Added repository source:
```bash
echo "deb [signed-by=/usr/share/keyrings/zammad.gpg] https://dl.packager.io/srv/zammad/zammad/stable/ubuntu 22.04 main" | sudo tee /etc/apt/sources.list.d/zammad.list
```

Updated package list:
```bash
sudo apt update
```

## Installation

Installed Zammad:
```bash
sudo apt install zammad -y
```

This installed: Nginx (web server), PostgreSQL (database), Elasticsearch, Zammad application services

## Service Verification

Checked service status
```bash
sudo systemctl status zammad
sudo systemctl status nginx
sudo systemctl status postgre
```

Verified listening ports:
```bash
sudo ss -tulnp
```

## Initial Web Access

Accessed Zammad via:
```bash
http://<server-ip>
```

Complete initial setup wizard:
• Created admin account
• Configured database automatically
• Verified application loaded successfully

## Firewall Configuration

Ensure Http Access allowed:
```bash
sudo ufw allow 80/tcp
```

If using HTTPS:
```bash
sudo ufw allow 443/tcp
```

## Troubleshooting 

Containers were up, but Windows was trying port 80 and Zammad is currently listening on port 8080 leading to connection refused. In order to map container to port 80 I opened nano docker-composed.yml to find the nginx service and changed ports from 8080 to 80. I then restarted the containers and verified it bound to port 80. Now Windows can use http://helpdesk1.sarifim.local.

<img width="1283" height="803" alt="image" src="https://github.com/user-attachments/assets/57dda067-52b8-413b-83f5-2c320512bfe4" />

<img width="1284" height="801" alt="Screenshot 2026-02-27 at 1 20 59 AM" src="https://github.com/user-attachments/assets/7acd2aea-553e-4926-a173-5b8efdb1f5ee" />

<img width="1289" height="806" alt="Screenshot 2026-02-27 at 1 26 07 AM" src="https://github.com/user-attachments/assets/d4531e1b-eeb5-44e1-9fc3-d9153877d558" />

<img width="1282" height="801" alt="Screenshot 2026-02-27 at 9 45 43 PM" src="https://github.com/user-attachments/assets/b2d4645d-dbaf-4dc6-95a5-546b6afd55c6" />

<img width="1281" height="802" alt="Screenshot 2026-02-27 at 9 45 32 PM" src="https://github.com/user-attachments/assets/bc7e805c-49ec-4919-95e4-b7038518cdea" />

## Comments

This demonstrates the deployment and configuration of an open-source enterprise helpdesk system on Ubuntu, validating service dependencies (database, web server, search engine), and preparation for integration with centralized identity services via LDAP.
