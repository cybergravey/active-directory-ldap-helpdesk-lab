# Active Directory + LDAP Helpdesk Integration Lab

## Overview

This project demonstrates the deployment and integration of a Windows Active Directory domain with a self-hosted helpdesk system (Zammad) using LDAP for centralized authentication and role-based access control (RBAC).

The lab simulates a real-world enterprise environment where internal IT staff authenticate against Active Directory to access helpdesk systems.

---

## Lab Architecture

**Infrastructure Components**

- Windows Server (Domain Controller)
- Windows Workstation VM
- Ubuntu Server (Zammad host)
- Active Directory Domain: `sarifim.local`
- Helpdesk Platform: Zammad (self-hosted)

---

## Key Implementations

- Active Directory domain deployment
- Organizational structure and security groups
- Dedicated LDAP service account
- Secure LDAP bind configuration
- Attribute mapping (givenName, sn, mail, sAMAccountName)
- Role-based access control (IT-Helpdesk → Agent)
- Live domain authentication testing
- Troubleshooting LDAP bind behavior
- UPN vs sAMAccountName authentication validation

---

## What This Project Demonstrates

- Identity and Access Management (IAM)
- LDAP integration in enterprise applications
- Windows Server administration
- Service account configuration
- Role-based access control (RBAC)
- Troubleshooting authentication flows
- Hybrid Windows/Linux infrastructure management

---

## Documentation

Detailed implementation steps are located in the `/docs` directory.

---

## Future Enhancements

- Ticket workflow simulation
- Automated password reset ticket flow
- Access revocation testing
- Bind account permission hardening
- OU-based LDAP filtering

---

## Author

Chris Graves  
Cybergravey | Sarifim Labs
