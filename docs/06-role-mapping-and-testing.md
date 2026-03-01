# 06 – Enterprise RBAC, LDAP Group Mapping, and Access Validation

This phase upgrades the lab from “LDAP sync works” to an enterprise-style **Identity & Access Management (IAM)** setup by implementing **role-based access control (RBAC)** using **Active Directory security groups** and validating access through repeatable test cases.

---

## Objectives

- Define a clear **RBAC model** for the helpdesk environment
- Use **AD security groups** as the source of truth for role assignment
- Map AD groups → Zammad roles (**Admin / Agent / Customer**)
- Validate **least privilege** and **separation of duties**
- Confirm access changes by testing membership updates and re-sync behavior

---

## RBAC Design (Planned Role Model)

### Role Definitions

| Zammad Role | Purpose | Typical Permissions |
|------------|---------|--------------------|
| **Admin** | System administration | Full configuration, integrations, user/role control |
| **Agent** | Helpdesk staff | Ticket handling, internal notes, assignments, escalations |
| **Customer** | End users | Create/view own tickets, limited portal access |

### AD Security Groups → Zammad Roles

| Active Directory Group | Zammad Role | Notes |
|------------------------|------------|------|
| **IT_Admins** | Admin | Full admin control for system owners |
| **Helpdesk_Agents** | Agent | Helpdesk staff managing tickets |
| **Domain Users** (default) | Customer | Standard end users submit tickets |

> ✅ Goal: Make **AD groups the authoritative source** for access and avoid manual role sprawl inside Zammad.

---

## Preconditions

- Phase 05 completed: LDAP integration is functional and users can sync from AD
- AD Security Groups exist (from Phase 04):
  - `IT_Admins`
  - `Helpdesk_Agents`
  - `HR_Group`
  - `Sales_Group`

 <img width="1282" height="801" alt="Screenshot 2026-02-28 at 9 47 08 PM" src="https://github.com/user-attachments/assets/c3967e6c-68e1-4293-b767-6efc3b3f5c0a" />

- Test users exist:
  - `chris.graves` (intended Admin/Agent)
  - `msmith` (intended Customer)
  - `jdoe` (intended Customer)
 
<img width="1280" height="803" alt="Screenshot 2026-02-28 at 9 34 49 PM" src="https://github.com/user-attachments/assets/5f251e2f-153a-4255-acba-2d8134426ced" />

---

## Step 1 — Confirm AD Group Membership

On the Domain Controller:

1. Open **Active Directory Users and Computers**
2. Verify group membership:

- `Chris Graves` ∈ `IT_Admins`, `Helpdesk_Agents`
- `Mary smith` ∈ `HR_Group`(not in privileged group; remains standard user)
- `John Doe` ∈ `Sales_Group`(not in privileged group; remains standard user)

<img width="1280" height="804" alt="Screenshot 2026-02-28 at 9 51 27 PM" src="https://github.com/user-attachments/assets/9e68a96b-3d68-4fcf-ac65-3e073025db73" />

<img width="1280" height="801" alt="Screenshot 2026-02-28 at 9 51 51 PM" src="https://github.com/user-attachments/assets/8db840a5-b7f2-4aca-8c9d-b6aaffba973b" />

<img width="1284" height="803" alt="Screenshot 2026-02-28 at 9 52 25 PM" src="https://github.com/user-attachments/assets/752b28f4-5c79-406a-80ec-50d491dfc5f1" />

<img width="1281" height="802" alt="Screenshot 2026-02-28 at 9 53 18 PM" src="https://github.com/user-attachments/assets/6c0d8de9-04ea-4f2a-accb-6fc5f18f21d1" />

---

## Step 2 — Configure Zammad LDAP Group-to-Role Mapping

In Zammad:

1. Navigate to:  
   **Settings → System → Integrations → LDAP**
2. Open your LDAP Source (or click **New Source** if needed)
3. Locate the section for **Role assignment / group mapping** (wording varies slightly by version)

Add mappings:

- `IT_Admins` → `Admin`
- `Helpdesk_Agents` → `Agent`
- Everyone else → `Customer`

> 📌 Tip: If Zammad supports it, use the full Distinguished Name (DN) of the group, e.g.:  
`CN=IT_Admins,OU=Groups,DC=sarifim,DC=local`

<img width="1282" height="803" alt="Screenshot 2026-02-28 at 10 11 26 PM" src="https://github.com/user-attachments/assets/284083d2-5607-4076-8209-b86f894d8865" />

---

## Step 3 — Force LDAP Sync (If Needed)

If users do not update immediately:

- Trigger LDAP sync from the LDAP integration page (if available)
- Or restart the Zammad stack (only if necessary)

<img width="1281" height="802" alt="Screenshot 2026-02-28 at 10 19 40 PM" src="https://github.com/user-attachments/assets/75ddd5be-399e-4a55-92f5-1525860bcddf" />

On the Ubuntu host (example):

```bash
cd ~/zammad
sudo docker compose restart
```

## Step 4 — Access Validation Testing (Expected Behavior)

Validation Steps

Admin/Agent Validation (chris.graves)

	1.	Log in as chris.graves
	2.	Confirm ability to access:
	  •	Settings
	  •	Integrations
	  •	User management
	3.	Confirm “Admin” role displayed under user profile/roles
      • Ticket views/queues
      • Assignments
      • Internal notes (if enabled)

<img width="1281" height="802" alt="Screenshot 2026-02-28 at 10 38 48 PM" src="https://github.com/user-attachments/assets/683afc5b-1483-40f9-86d0-6d754d678520" />

Customer Validation (jdoe)

	1.	Log in as jdoe
	2.	Confirm ability to:
	  •	Create ticket
	  •	View own ticket history
	3.	Confirm cannot view internal queues or settings

<img width="1278" height="868" alt="Screenshot 2026-02-28 at 10 31 47 PM" src="https://github.com/user-attachments/assets/d2676058-8102-42a7-97a8-7d995c579411" />

Documentation Notes (What This Demonstrates)

This phase demonstrates real-world IAM practices:
	•	RBAC using centralized identity (Active Directory)
	•	Separation of duties (Admins vs Agents vs Customers)
	•	Least privilege validation (removing group membership removes access)
	•	Repeatable testing and governance-style documentation

⸻

Outcome

✅ Zammad roles are governed by AD security group membership

✅ Privilege changes are enforced through synchronization

✅ The lab now reflects enterprise IAM practices rather than manual user management

⸻

Troubleshooting (Quick Hits)

	•	User role not updating after AD change
	•	Force LDAP sync or restart containers
	•	Can’t log in as a synced user
	•	Confirm the correct login field (username vs email) shown in Zammad user list
	•	Duplicate user entries
	•	Ensure only one authoritative identity source is used (LDAP) and remove conflicting local accounts
