# 03 - Active Directory Domain Setup

## Objective

Deploy and configure a Windows Server Domain Controller to provide centralized identity management for the lab environment.

The domain will later be integrated with the Zammad helpdesk platform using LDAP.

---

## Environment

- Windows Server Version: (e.g., Windows Server 2022)
- Hostname: (e.g., WIN-SRV-AD1)
- Static IP: 192.168.0.10
- Subnet: 255.255.255.0
- Gateway: 192.168.0.1
- DNS: Self (Domain Controller)
- Domain Name: sarifim.local

---

## Static Network Configuration

Configured static IP before domain promotion.

Why?

Active Directory requires a stable IP address for DNS and LDAP services.

---

## AD DS Role Installation

Installed Active Directory Domain Services role via:

Server Manager → Add Roles and Features

Selected:

- Active Directory Domain Services
- DNS Server

---

## Domain Promotion

Promoted server to Domain Controller:

1. Selected "Add a new forest"
2. Root domain name: sarifim.local
3. Set Directory Services Restore Mode (DSRM) password
4. Completed promotion
5. Rebooted server

---

## DNS Verification

Verified DNS configuration:

```powershell
ipconfig /all
```
Confirmed:
	•	DNS server pointing to itself
	•	Domain resolution working

Tested domain resolution from another VM:
```powershell
nslookup sarifim.local
```

## Organizational Structure Design

Created logical structure for enterprise simulation:

	•	Users
	•	IT-Helpdesk
	•	PrivilegedAccounts
	•	Departments (HR, IT, etc.)

Purpose:

To simulate role-based access control and realistic enterprise structure.

## User Account Creation

Created test users:

	•	Chris Graves
	•	Mary Smith
	•	John Doe

Configured:

	•	Password policies
	•	Account options
	•	UPN formatting

## Security Group Creation

Created:

•	IT-Helpdesk (Security Group)

Purpose:

Members of this group will receive Agent role inside Zammad via LDAP role mapping.

## Validation

Verified:

	•	Domain login from workstation VM
	•	Group membership assignments
	•	User visibility in Active Directory Users and Computers

## Comments

This demostrates the configuration of a Windows Active Directory Domain Controller, implemented DNS, structured organization units, created security groups, and preparation of the environment for centralized LDAP authentication integration with enterprise applications.
