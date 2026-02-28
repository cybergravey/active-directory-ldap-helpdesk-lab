# 05 – LDAP Integration (Zammad + Active Directory)

---

## 🎯 Objective

Configure Zammad to authenticate users against Active Directory using LDAP for centralized identity management and role-based access control (RBAC).

This integration enables domain users to log in to the helpdesk system using their AD credentials.

---

## 🏗 Environment Overview

- **Domain:** sarifim.local  
- **Domain Controller:** Windows Server (AD DS)  
- **Zammad Host:** Ubuntu Server (Proxmox VM)  
- **LDAP Service Account:** svc_zammad_ldap  

---

## 1️⃣ Network Validation (Pre-Configuration)

Before configuring LDAP in Zammad, connectivity between the Ubuntu server and Domain Controller was verified.

From the Ubuntu (Zammad) server:

```bash
ping <DC-IP>
```

Optional validation using ldapsearch (if installed):
```bash
ldapsearch -x -H ldap://<DC-IP> -D "svc_zammad_ldap@sarifim.local" -W
```
## Why This Matters

Validating network communication ensures:

	•	DNS resolution is functioning
	•	Firewall rules are not blocking LDAP
	•	The service account can bind successfully
	•	The issue is not network-related before troubleshooting application-level problems

## 2️⃣ Zammad LDAP Configuration

Navigation:

Admin Panel → System → Integrations → LDAP

<img width="1278" height="799" alt="image" src="https://github.com/user-attachments/assets/176d632b-f275-45eb-89bb-ca8979d4e66a" />

A new LDAP source was configured with the following settings.

## 🔐 Connection Settings
	•	Host: <DC-IP>
	•	Port: 389
	•	SSL: Disabled (lab environment)
	•	Bind User: svc_zammad_ldap@sarifim.local
	•	Bind Password: (Service account password)
	•	Base DN: DC=sarifim,DC=local

<img width="1281" height="800" alt="image" src="https://github.com/user-attachments/assets/f444ac37-f40b-4cd6-aedc-ec6d5eddc8d5" />

<img width="1279" height="799" alt="image" src="https://github.com/user-attachments/assets/1318242f-a74c-409f-8600-38108825fc8d" />


Configuration Notes

	•	The UPN format (user@domain) was used successfully for the bind account.
	•	A dedicated service account was used instead of Administrator.
	•	Base DN defines the search scope within the directory.

## 3️⃣ LDAP Attribute Mapping

The following LDAP attributes were mapped to Zammad user fields:

<img width="1283" height="800" alt="image" src="https://github.com/user-attachments/assets/f78b586e-0ce7-46d3-a6a0-ef4d55048a5e" />

## Attribute Selection Notes
	•	sAMAccountName was used as the login identifier.
	•	Testing revealed that authentication may require full UPN format (user@domain) depending on configuration.
	•	This demonstrates real-world differences between login identifiers and authentication formats.

## 4️⃣ Role Mapping (RBAC Integration)

LDAP group-to-role mapping was configured to enforce access control.

Example:

	•	LDAP Group: IT-Helpdesk
	•	Mapped Zammad Role: Agent

<img width="1284" height="800" alt="image" src="https://github.com/user-attachments/assets/4d771338-019f-416c-b4a0-4b7e21bae394" />

Why This Matters

This enables:

	•	Centralized identity management via AD
	•	Role-based access control tied directly to group membership
	•	Administrative control without modifying Zammad locally

This reflects enterprise RBAC design principles.

## 5️⃣ LDAP Synchronization

After configuration:

	•	LDAP synchronization was enabled
	•	Initial sync was executed
	•	Domain users were successfully created in Zammad
	•	No failed or skipped objects

<img width="1281" height="802" alt="image" src="https://github.com/user-attachments/assets/619cd63b-d0a2-4b32-ae33-2fcd0db4639d" />



This confirmed successful LDAP bind and attribute mapping.

## 6️⃣ Authentication Testing

Authentication testing confirmed:

	•	Domain users could log into Zammad
	•	Role assignments were applied automatically via AD group membership
	•	Login behavior differed depending on use of UPN vs sAMAccountName

This validated:

	•	Directory connectivity
	•	Credential validation
	•	Proper RBAC enforcement

## 🛠 Troubleshooting Observations

During integration, the following behaviors were observed:

	•	Active Directory Users and Computers (ADUC) console may cache object changes (resolved with F5 refresh)
	•	UPN format (user@domain) may be required for successful login
	•	LDAP bind failures can occur if incorrect Base DN is specified
	•	Service account permissions must allow directory read access

## 🔐 Security Considerations
	•	A dedicated LDAP service account was used
	•	Password expiration was disabled for stability
	•	No Domain Admin privileges were granted
	•	Least privilege principle enforced

This reduces credential abuse risk and limits blast radius in case of compromise.

## Comment 
Implemented LDAP integration between Active Directory and a self-hosted Zammad helpdesk platform, including secure bind configuration, attribute mapping, and AD group-based RBAC enforcement.
