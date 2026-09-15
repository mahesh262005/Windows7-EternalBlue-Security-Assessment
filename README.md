# Windows 7 Security Assessment & EternalBlue Exploitation Report

## ⚠️ Disclaimer
The information contained in this report is strictly for educational, testing, and professional development purposes. This assessment was performed in a fully isolated, authorized, and legal lab environment against an intentionally vulnerable target. The author is not responsible for any misuse, damage, or illegal activity caused by utilizing the methodologies or techniques outlined in this document.

---

## Executive Summary
This report documents a targeted, end-to-end security evaluation and penetration testing engagement conducted against a Windows 7 target host within an authorized laboratory environment. The assessment followed a structured five-phase methodology—reconnaissance, scanning/enumeration, vulnerability assessment, exploitation, and post-exploitation analysis—to demonstrate the critical risks associated with legacy unpatched operating systems and vulnerable SMB protocols.

* **Target Host:** Windows 7 Home Basic SP1 (Isolated Lab VM)
* **Target IP Range:** `10.0.2.X` / `192.168.1.X` (Masked for OpSec)
* **Attacker Platform:** Kali Linux (`mahesh@kali`)
* **Assessment Date:** September 2026
* **Tools Used:** Nmap v7.99, Enum4linux, Nessus Vulnerability Scanner, Metasploit Framework, Meterpreter

---

## Key Findings & Exploitation Phase Summary

### 1. Phase 1: Reconnaissance and Fingerprinting
* **Port Scanning:** A TCP scan via Nmap identified 9 open ports out of 1,000 tested, highlighting key open protocols: Microsoft RPC (`135/tcp`), NetBIOS session service (`139/tcp`), and Microsoft-DS/SMB (`445/tcp`).
* **OS Detection:** Fingerprinted the target host operating system precisely as Microsoft Windows 7 Home Basic 7601 Service Pack 1.
* **Enumeration:** Executed `enum4linux` and Nmap default scripts (`-sC`) to confirm workgroup membership (`WORKGROUP`) and guest authentication context.

### 2. Phase 2: Vulnerability Assessment (Nessus Scan)
An unauthenticated advanced vulnerability scan identified **18 total findings**. The key critical/high findings include:
* **MS17-010 (EternalBlue):** Security update for Microsoft Windows SMB Server (Plugin #97833) with a critical Vulnerability Priority Rating (VPR) of `9.9`.
* **MS11-030:** DNS Resolution Remote Code Execution (Plugin #53514) allowing unauthenticated code execution.
* **Unsupported Windows OS:** Marked with a maximum CVSS score of `10.0`, reflecting severe exposure due to lack of vendor patches.

### 3. Phase 3: Exploitation (MS17-010 EternalBlue)
* **Module Selection:** Utilized Metasploit's `exploit/windows/smb/ms17_010_eternalblue` with a `windows/x64/meterpreter/reverse_tcp` payload.
* **Execution:** Successfully performed pool grooming and buffer overwrites, resulting in an active Meterpreter session with the highest possible privileges: `NT AUTHORITY\SYSTEM`.

### 4. Phase 4: Post-Exploitation & Data Harvesting
* **System Profiling:** Verified x64 architecture and system details using `sysinfo`, `getuid`, and `getpid`.
* **Credential Harvesting:** Extracted local SAM database password hashes via `hashdump` for Administrator, Guest, and vboxuser.
* **Surveillance & Keylogging:** Captured a live desktop screenshot and initialized a keystroke sniffer (`keyscan_start`) which successfully logged typed user credentials (`admin / pass!123`).
* **Process Migration:** Migrated the Meterpreter agent to a stable process PID to ensure persistence and stealth.

### 5. Phase 5: Anti-Forensics & Log Clearance
* **Log Inspection:** Observed 339 Application events and 724 Security audit trails using `eventvwr.msc`.
* **Clearance:** Executed the Meterpreter `clearev` command, successfully wiping 361 Application records, 1,333 System records, and 761 Security records.
* **Forensic Artifact:** Post-clearance inspection verified that the logs were blanked, leaving only a single forensic artifact: **Event ID 1102 ("The audit log was cleared")** attributed to the SYSTEM security context.

---

## Remediation & Recommendations

1. **Implement Patch Management:** Immediately apply official Microsoft security patches (specifically `KB4013389` for MS17-010) or upgrade to a modern, supported operating system.
2. **Disable Legacy Protocols:** Completely disable outdated and insecure protocols such as **SMBv1** across the entire network environment.
3. **Enforce Network Segmentation:** Isolate legacy or sensitive assets into dedicated VLANs and block external/internal access to high-risk ports like TCP `445`.
4. **Deploy Endpoint Detection & Response (EDR):** Install advanced endpoint security capable of blocking unauthorized process migrations, memory tampering, and keystroke logging.
5. **Strengthen SIEM Log Monitoring:** Configure immediate, automated real-time alerts for critical forensic indicators such as **Event ID 1102**.

---

## Scope Note
This assessment was performed in a fully isolated lab environment against a virtual machine distributed for security training and research purposes. No production systems were involved.

## 📄 Security Assessment Report

### Windows 7 EternalBlue Security Assessment

[🔗 View & Download Full Report](https://mahesh262005.github.io/Windows7-EternalBlue-Security-Assessment/)

***
**Prepared by:** Mahesh Ade — Cybersecurity Student
