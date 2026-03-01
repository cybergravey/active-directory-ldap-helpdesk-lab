# 04 - Security Groups and LDAP Service Account Configuration

## Objective

Prepare Active Directory for secure LDAP integration by:

1. Creating role-based security groups
2. Creating a dedicated service account for LDAP binding
3. Applying least privilege principles

---

## Role-Based Security Group Strategy

Created security group:

- IT-Helpdesk (Security Group)

<img width="1279" height="800" alt="image" src="https://github.com/user-attachments/assets/99eaacda-8da1-4bf2-b308-db62c42479b8" />

Purpose:

Members of this group will be mapped to the "Agent" role inside Zammad via LDAP.

This allows centralized role management within Active Directory rather than inside the application.

---

## Why Use AD Groups for Role Control?

Using AD groups provides:

- Centralized identity control
- Separation of identity and application roles
- Immediate access revocation when user removed from group
- Enterprise-aligned RBAC design

---

## LDAP Service Account Creation

Created dedicated service account:

- Name: Zammad LDAP
- Logon Name: svc_zammad_ldap@sarifim.local

<img width="1280" height="799" alt="image" src="https://github.com/user-attachments/assets/3e5d84b7-3d81-495f-835d-80a5c1c4c74d" />

Purpose:

This account is used exclusively for LDAP binding between Zammad and Active Directory.

---

## Why Use a Service Account?

Best practice in enterprise environments:

- Never bind LDAP with a personal admin account
- Isolate authentication mechanisms
- Limit potential blast radius if credentials are compromised

---

## Account Configuration

Configured the service account with:

- Strong password
- Password never expires (controlled lab environment)
- Removed unnecessary group memberships
- Not a member of Domain Admins

---

## Permissions Required

The service account requires:

- Read access to user objects
- Read access to group memberships

No write permissions were granted.

---

## Group Membership Assignment

Added the following user to IT-Helpdesk:

- Chris Graves

<img width="1287" height="804" alt="Screenshot 2026-02-28 at 6 11 43 PM" src="https://github.com/user-attachments/assets/898908d9-0dbf-4c0c-8e1d-7b4a36e0fef8" />

Purpose:

To validate role mapping within Zammad during LDAP integration.

---

## Validation

Verified:

- Service account visible in Active Directory
- IT-Helpdesk group correctly populated
- Users properly assigned to group

---

## Comment

> This demonstrates the implementation of a least-privilege LDAP service account and designed role-based access control using Active Directory security groups, ensuring centralized identity governance and application-level role enforcement.

