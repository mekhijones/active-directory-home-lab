# Active Directory Home Lab

**Author:** Mekhi Jones
**Date:** June 2026
**Environment: ** Oracle VirtualBox | Windows Server 2022 (Domain Controller) | Domain: mekhilab.local


## Overview
This lab simulates a small enterprise Windows environment to build and demonstrate hands-on skills in a Active Directory environment. This lab was built using a Windows Server 2022 Evaluation ISO, virtualized in Oracle VirtualBox.

The goal was not only to install Active Directory. But to get an understanding on what it is like to use Active Directory. The use of Active Directory was used to get an realistic view of the structure of Active Directory and the security controls that was applied with Active Directory. By proving these controls work, I triggered and investigated a real security event. 


## Environment Setup

| Hypervisor | Oracle VirtualBox|
| Domain Controller OS | WIndows Server 2022 Standard Evaluation (Desktop Experience) |
|Domain Name | mekhilab.local|
|Server Hostname | WIN-8CSG8P56NI5 |
|Networking | Bridged Adapter, static IP(192.168.0.200) |

## Build Process

1. **Configured the VM with : 8GB RAM, 4 CPUs, 50GB dynamically allocated disk**
2. **Installed Windows Server 2022 using a custom(clean) install with the Desktop Experience edition, to keep full GUI access to administrative tools.**
3. **Configured a static IP address and pointed the server's DNS to its own server (127.0.0.1), since the Domain Controller would also server as the domian's DNS server.**
4. **Installed the Active Directory Domain Services role on Server Manager.**
5. **Promoted the server to a Domain Controller, creating a new forest and domain: 'mekhilab.local'.**
6. **Verified the installation by confirming Active Directory Users and Computers ('dsa.msc') and that loaded the new domain structure successfully.**

## Organizational Structure

Built out a simulated three-department company structure using Organizational Units (OUs), with one user account per department. 

| OU | User | Logon Name |

| IT | Mekhi Jones | mjones|
| HR | Peter Banks | pbanks|
|Finance | Alyssa Williams | awilliams

Created matching security groups ( IT-Group, HR-Group, Finance-Group) and assigned each user to their respective group. Which created the foundation for future role-based access control and Group Policy targeting.


## Security Configuration: Account Lockout Policy

Configured an account lockout policy via the **Default Domain Policy** GPO:
- **Account lockout threshold:** 5 invalid logon attempts
- **Account lockout duration:** 30 minutes
- **Reset account lockout counter after:** 30 minutes

This account policy was applied at the domain level using Group Policy Managment('gpmc.msc') and forced the group policy update using 'gpupdate /force'. Verified the active group policy using net accounts /domain. 

## Detection & Investigation

To validate the policy was functioning and not just configured. I triggered a lockout and investigated the event using Event Viewer. 

**Test Procedure:**
1. Attempted to log on as 'awilliams' with an incorrect password 5 times in a row.
2. Confirmed the account become locked, which produced the message: *"The referenced account is currently locked out and may not be logged on to."*



**Evidence captured in the Security event log using Event Viewer:**

-**Event ID 4625** (Audit Failure-*An account failed to log on*)
-logged 5 times, timestamp seconds apart, responding to each failed attempt.
**Event ID 4740** (Audit Failure - *A user account was locked out*) - the lockout event. 
Detail: 
- 'TargetUserName: awilliams'
- 'TargetDomainName: WIN-8CSG8P56NI5'
- 'Computer: WIN-8CSG8P56NI5.mekhilab.local'
- 'SubjectDomainName: MEKHILAB'
- 'TimeCreated: 2026-06-20T19:43:38Z'

## Skills Demonstrated

- Windows Server Installation and configuration
- Active Directory Domain Services deployment and the new creation of a forest/domain
- Usage of Organizational Units
- Group Policy Management - enforcement of security policies
- Windows Security event log analysis (Event IDs 4625, 4740)
- Basic incident investigation using Window tools.

## Conclusion

- I learned that the built-in Administrator account exempt from account lockout policy. 
- A VM can crash during a Domain Controller reboot. This can happen on lower resourced host.
- The creation of this lab takes time and I had a taste of what it is like to do an Active Directory in a real work envrionment. 
