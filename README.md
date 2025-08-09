# Windows Server Active Directory & Group Policy Project

## Project Status

**Status:** Completed

---

## Table of Contents

- [1.0 Project Overview](#10-project-overview)
- [2.0 Technologies Used](#20-technologies-used)
- [3.0 Project Objectives](#30-project-objectives)
- [4.0 Key Implementations](#40-key-implementations)
- [5.0 Troubleshooting & Lessons Learned](#50-troubleshooting--lessons-learned)
- [6.0 Project Verification](#60-project-verification)
- [7.0 Conclusion](#70-conclusion)

---

## 1.0 Project Overview

This project documents the setup and administration of a secured Windows domain environment. The primary objective was to configure a small-scale network using **Windows Server** as a Domain Controller and enforce a comprehensive set of **Group Policy Objects (GPOs)** on a client machine. This project demonstrates proficiency in fundamental Active Directory administration, security policy implementation, and system-level configuration management.

## 2.0 Technologies Used

* **Virtualization:** Oracle VM VirtualBox
* **Operating Systems:** Windows Server (for AD DS), Windows 10/11 (client)
* **Services:** Active Directory Domain Services (AD DS), DNS, Group Policy Management
* **Tools:** PowerShell, `gpresult`, Command Prompt

## 3.0 Project Objectives

The following administrative tasks were successfully completed and verified:

1.  **Domain Infrastructure:** Install and configure AD DS, promote a server to a Domain Controller (DC), and join a client machine to the domain.
2.  **User & Group Management:** Create Organizational Units (OUs) and user accounts for effective management.
3.  **Security Policies:**
    * Enforce a strong password policy (complexity, length, history).
    * Implement an account lockout policy for failed logon attempts.
    * Configure advanced audit policies on domain controllers and client machines.
4.  **Endpoint Control:**
    * Disable access to sensitive tools like CMD, PowerShell, and Registry Editor.
    * Restrict the installation of software and printer drivers.
    * Disable access to removable media drives (USB, CD/DVD).
5.  **System & Network Policies:**
    * Apply a GPO-enforced firewall rule to block specific outbound traffic (e.g., pinging 8.8.8.8).
    * Configure Windows Defender Antivirus to scan network files.
    * Enforce BitLocker encryption on client OS drives.
    * Set RDP connection limits and idle session timeouts.
    * Configure a screensaver policy requiring a password.
    * Define a custom logon message for all users.

## 4.0 Key Implementations

### **Account & Logon Security**
- Configured a `UserPasswordPolicy` GPO to enforce password complexity, a minimum length of 8 characters, and account lockouts after 3 failed logon attempts.
- A Time of Day logon restriction was set for standard user accounts.

### **Network & Firewall Rules**
- An outbound firewall rule was created via GPO to block all ICMP traffic to specific remote IP addresses (`8.8.8.8`, `8.8.4.4`).
- RDP settings were configured to limit sessions to a single connection and enforce a session timeout.

### **Endpoint & System Control**
- Policies were set to disable key system tools like `cmd.exe` and `regedit.exe` for standard users.
- Removable media access was denied across the board to prevent data exfiltration.
- BitLocker Drive Encryption was enforced on the client OS drive, with a GPO configured to allow encryption without a compatible TPM (for virtual machine environments).

## 5.0 Troubleshooting & Lessons Learned

A key challenge encountered was a GPO conflict that prevented users from logging into the domain-joined client machine. The error was a generic "sign-in method isn't allowed" message.

- **Diagnosis:** The `gpresult /h` command was used to generate a detailed report, which identified the `Default Domain Controllers Policy` as the **Winning GPO** for the "Allow log on locally" right.
- **Resolution:** This GPO, which is meant only for servers, was incorrectly linked to the client's OU. The conflicting GPO link was removed, and the `gpupdate /force` command was run to apply the correct policy.
- **Lesson:** This experience highlighted the critical importance of GPO linking, precedence, and using diagnostic tools like `gpresult` to identify and resolve policy conflicts.

## 6.0 Project Verification

All policies were rigorously tested to confirm their functionality. The table below summarizes the verification of key policies.

| Policy | Verification Method | Status |
| :--- | :--- | :--- |
| **Password Policy** | Attempted to set a simple password | Pass |
| **Account Lockout** | Performed multiple failed logon attempts | Pass |
| **Firewall Rule** | Pinging `8.8.8.8` from Command Prompt | Pass |
| **Disable CMD/Regedit** | Attempted to open `cmd.exe` and `regedit.exe` | Pass |
| **Disallow Media Drives**| Inserted a virtual USB drive | Pass |
| **BitLocker** | Confirmed recovery key in Active Directory | Pass |
| **Audit Policies** | Checked Security logs in Event Viewer | Pass |

## 7.0 Conclusion

This project successfully demonstrates the ability to architect and manage a secure Active Directory domain. The comprehensive configuration of Group Policies ensures a hardened, compliant, and well-documented environment. The successful troubleshooting and resolution of a policy conflict further validate a strong grasp of Windows Server administration principles.
