# 01 - Ubuntu Server Deployment (Proxmox VM)

## Objective

Deploy an Ubuntu Server virtual machine within a Proxmox hypervisor environment to host the Zammad helpdesk platform.

This VM will serve as the Linux application server integrated with Active Directory via LDAP.

---

## Lab Infrastructure Context

- Hypervisor: Proxmox VE
- Host Hardware: (Specify your mini PC or lab hardware)
- Network Segment: (e.g., 192.168.0.0/24)
- Domain Controller IP: (e.g., 192.168.0.10)
- AD Domain: sarifim.local

---

## Virtual Machine Configuration

- VM Name: helpdesk1
- OS: Ubuntu Server 22.04 LTS
- CPU: 2 Cores
- RAM: 4 GB
- Disk: 40 GB (VirtIO)
- Network Bridge: vmbr0
- Network Adapter: VirtIO (paravirtualized)

### Why These Settings?

- 2 CPU cores sufficient for small helpdesk deployment
- 4GB RAM recommended baseline for Zammad
- VirtIO for optimized performance in Proxmox
- Bridged networking to allow direct LAN communication with Domain Controller

---

## Ubuntu Installation Steps

1. Downloaded Ubuntu Server 22.04 LTS ISO.
2. Uploaded ISO to Proxmox storage.
3. Created new VM with above configuration.
4. Mounted ISO.
5. Completed guided Ubuntu installation.
6. Created administrative user (non-root).
7. Enabled OpenSSH server during install.

---

## Network Configuration

Configured static IP to ensure consistent LDAP connectivity.

Example:

IP Address: 192.168.0.20  
Subnet Mask: 255.255.255.0  
Gateway: 192.168.0.1  
DNS Server: 192.168.0.10 (Domain Controller)

### Why Static IP?

LDAP integrations require predictable addressing.
Using DHCP could break integration if IP changes.

---

## Post-Installation Hardening

Updated system:

```bash
sudo apt update && sudo apt upgrade -y
```

Additional packages installed:
```bash
sudo apt install curl gnupg apt-transport-https ca-certificates
```

Enabled UFW firewall (if applicable):
```bash
sudo ufw allow ssh
sudo ufw enable
```

## Connectivity Validation

Verified connectivity to Domain Controller:
```bash
ping 192.168.0.10
```

Verified DNS resolution:
```bash
nslookup sarifim.local
```
Confirmed outbound internet connectivity for package installation.

## Challenges Encountered

Docker pulls were failing during deployment of containerized helpdesk server. I traced it to DNS resolution through my domain controller. The DC didn't have forwarders configured, so external lookups failed. After configuring forwarders and flushing caches, name resolution worked and Docker pulled successfully.

<img width="1290" height="811" alt="Pasted Graphic" src="https://github.com/user-attachments/assets/80815636-dfcc-4fe8-bcbf-75b3be59bd38" />

<img width="1283" height="804" alt="Screenshot 2026-02-27 at 12 40 14 AM" src="https://github.com/user-attachments/assets/90c501b3-56fa-40f8-8bfd-8395ab509600" />

<img width="1282" height="801" alt="Pasted Graphic 1" src="https://github.com/user-attachments/assets/f68a526e-8e59-4413-8ed9-ba0579947d17" />

<img width="1284" height="805" alt="Pasted Graphic 2" src="https://github.com/user-attachments/assets/f0af7d6c-0535-4978-ae43-f1e5b0391877" />

## Comment
This demonstrates a provisioned Linux server in a Proxmox virtualized environment with configured static networking to support enterprise LDAP intergration with Active Directory. This demonstrates hybrid infrastructure deployment and cross-platform systems administration.
