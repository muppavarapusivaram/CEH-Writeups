# CEH Lab Writeups 🔐

> Comprehensive documentation of Certified Ethical Hacker (CEH) practical labs and exercises

[![GitHub](https://img.shields.io/badge/GitHub-muppavarapusivaram-blue?style=flat&logo=github)](https://github.com/muppavarapusivaram)
[![CEH](https://img.shields.io/badge/Certification-CEH-red?style=flat&logo=security)]()
[![Labs](https://img.shields.io/badge/Labs-In%20Progress-yellow?style=flat)]()

---

## 📋 Table of Contents
- [About](#about)
- [Lab Structure](#lab-structure)
- [Completed Labs](#completed-labs)
- [Technologies & Tools](#technologies--tools)
- [How to Use](#how-to-use)
- [Disclaimer](#disclaimer)
- [Contact](#contact)

---

## 🎯 About

This repository contains detailed writeups and documentation of hands-on labs performed as part of the **Certified Ethical Hacker (CEH)** certification training. Each lab includes:

- **Objective & Overview**
- **Step-by-step procedures**
- **Tools and commands used**
- **Results and findings**
- **Security recommendations**
- **Key takeaways**

The goal is to document the learning journey and create a reference for cybersecurity concepts, techniques, and best practices.

---

## 📁 Lab Structure

Each lab follows a standardized documentation format:

```
Lab-Name/
│
└── README.md
    ├── Lab Overview
    ├── Objective
    ├── Lab Environment
    ├── Theoretical Background
    ├── Lab Steps
    ├── Results & Findings
    ├── Key Takeaways
    └── References
```

---

## ✅ Completed Labs

### Module: System Hacking

| # | Lab Name | Category | Difficulty | Status |
|---|----------|----------|------------|--------|
| 01 | [Active Online Attack using Responder](./01-Active-Online-Attack-Responder) | Password Attacks | ⭐⭐⭐ | ✅ |
| 02 | [Reverse Shell Generator](./02-Reverse-Shell-Generator) | Remote Access | ⭐⭐⭐ | ✅ |
| 03 | [Privilege Escalation - UAC Bypass & Sticky Keys](./03-Privilege-Escalation) | Post-Exploitation | ⭐⭐⭐⭐ | ✅ |
| 04 | [Coming Soon] | - | - | 🔄 |

### Module: Network Scanning
| # | Lab Name | Category | Difficulty | Status |
|---|----------|----------|------------|--------|
| - | [Coming Soon] | - | - | 🔄 |

### Module: Enumeration
| # | Lab Name | Category | Difficulty | Status |
|---|----------|----------|------------|--------|
| - | [Coming Soon] | - | - | 🔄 |

### Module: Vulnerability Analysis
| # | Lab Name | Category | Difficulty | Status |
|---|----------|----------|------------|--------|
| - | [Coming Soon] | - | - | 🔄 |

### Module: Web Application Hacking
| # | Lab Name | Category | Difficulty | Status |
|---|----------|----------|------------|--------|
| - | [Coming Soon] | - | - | 🔄 |

**Legend:**
- ⭐ Easy
- ⭐⭐ Medium
- ⭐⭐⭐ Hard
- ⭐⭐⭐⭐ Advanced
- ✅ Completed
- 🔄 In Progress
- ⏳ Planned

---

## 🛠️ Technologies & Tools

### Operating Systems
- **Kali Linux** / **Parrot Security OS**
- **Windows 10/11**
- **Windows Server**

### Tools Used
- **Responder** - LLMNR/NBT-NS Poisoning
- **John the Ripper** - Password Cracking
- **Nmap** - Network Scanning
- **Metasploit Framework** - Exploitation & Post-Exploitation
- **MSFVenom** - Payload Generation
- **HoaxShell** - PowerShell Reverse Shell
- **Burp Suite** - Web Application Testing
- **Wireshark** - Network Analysis
- **Hashcat** - Advanced Password Recovery
- **Docker** - Container Management
- **Apache2** - Web Server
- **Reverse Shell Generator** - Automated Payload Creation
- And more...

---

## 📖 How to Use

### For Learning
1. Browse the lab folders
2. Read the README.md in each lab
3. Follow the step-by-step procedures
4. Set up your own lab environment to practice

### For Reference
- Use the search function to find specific topics
- Check the "Key Takeaways" section for quick insights
- Review defense recommendations for security best practices

### Prerequisites
- Basic understanding of networking concepts
- Familiarity with Linux command line
- Virtual machine software (VMware/VirtualBox)
- **Important:** Always practice in isolated lab environments

---

## 🎯 Lab Highlights

### Recent Addition: Privilege Escalation Lab
**What You'll Learn:**
- Bypassing Windows User Account Control (UAC)
- Exploiting FodHelper Registry Key
- Using Meterpreter's getsystem technique
- Exploiting Sticky Keys for persistence
- Gaining NT AUTHORITY\SYSTEM privileges
- Creating persistent backdoors

**Attack Chain:**
```
Initial Access → UAC Bypass → Privilege Escalation → Persistence
```

**Key Techniques:**
- `bypassuac_fodhelper` module
- `getsystem -t 1` command
- Sticky Keys accessibility feature exploitation
- Lock screen privilege escalation

---

## ⚠️ Disclaimer

**EDUCATIONAL PURPOSE ONLY**

This repository is for **educational and learning purposes only**. The techniques and tools documented here are part of authorized CEH lab exercises conducted in controlled environments.

### Important Notes:
- ❌ **DO NOT** use these techniques on systems you don't own or have explicit permission to test
- ❌ Unauthorized access to computer systems is **illegal**
- ✅ Always obtain proper authorization before conducting security testing
- ✅ Practice only in isolated lab environments or authorized platforms
- ✅ Follow responsible disclosure practices

**The author assumes no liability for misuse of the information provided.**

---

## 🎓 Certification Info

**Certified Ethical Hacker (CEH)**
- **Issuing Organization:** EC-Council
- **Certification Level:** Intermediate to Advanced
- **Focus Areas:** Ethical Hacking, Penetration Testing, Security Assessment

---

## 📊 Progress Tracker

```
Total Labs Planned:    50+
Labs Completed:        3
Labs In Progress:      0
Completion:            [██████░░░░░░░░░░░░░░] 6%
```

Last Updated: November 30, 2025

---

## 🔥 Latest Updates

### November 30, 2025
- ✅ Added **Privilege Escalation Lab** (UAC Bypass & Sticky Keys)
- 📝 Updated documentation with MITRE ATT&CK mappings
- 🛡️ Added comprehensive defense recommendations
- 📊 Enhanced security analysis section

### November 02, 2025
- ✅ Completed **Reverse Shell Generator Lab**
- ✅ Completed **Active Online Attack using Responder Lab**
- 🚀 Repository initialized

---

## 🧪 Lab Environment Specifications

### Typical Lab Setup
- **Attacker Machine:** Parrot Security OS (10.10.1.13)
- **Target Machine:** Windows 11 (10.10.1.11)
- **Network:** Isolated lab environment
- **Virtualization:** VMware / VirtualBox

### Network Configuration
```
┌─────────────────┐         ┌─────────────────┐
│  Parrot Security│         │   Windows 11    │
│   10.10.1.13    │◄───────►│   10.10.1.11    │
│   (Attacker)    │         │    (Target)     │
└─────────────────┘         └─────────────────┘
```

---

## 🎯 Skills Developed

Through these labs, you will develop expertise in:

### Technical Skills
- ✅ Network reconnaissance and scanning
- ✅ Password cracking and hash analysis
- ✅ Exploitation techniques
- ✅ Post-exploitation and privilege escalation
- ✅ Reverse shell creation and management
- ✅ Windows security mechanism bypass
- ✅ Persistence establishment
- ✅ Command and control (C2) operations

### Security Analysis
- ✅ Vulnerability identification
- ✅ Attack chain analysis
- ✅ Security control assessment
- ✅ Defensive countermeasure design
- ✅ Incident detection strategies

### Tools Mastery
- ✅ Metasploit Framework
- ✅ MSFVenom payload generation
- ✅ Responder and LLMNR poisoning
- ✅ John the Ripper
- ✅ Various enumeration tools

---

## 🔗 MITRE ATT&CK Techniques Covered

| Technique ID | Technique Name | Labs |
|--------------|----------------|------|
| T1059.001 | PowerShell | Lab 02, 03 |
| T1071.001 | Web Protocols | Lab 02 |
| T1548.002 | Bypass UAC | Lab 03 |
| T1546.008 | Accessibility Features | Lab 03 |
| T1134 | Access Token Manipulation | Lab 03 |
| T1557.001 | LLMNR/NBT-NS Poisoning | Lab 01 |
| T1110 | Brute Force | Lab 01 |
| More... | Coming Soon | - |

---

## 🤝 Contributing

While this is a personal learning repository, suggestions and feedback are welcome!

- Open an issue for questions or suggestions
- Feel free to fork and create your own version
- Share your learning journey!

---

## 📬 Contact

- **GitHub:** [@muppavarapusivaram](https://github.com/muppavarapusivaram)
- **Repository:** [CEH-Writeups](https://github.com/muppavarapusivaram/CEH-Writeups)

---

## 📝 License

This repository is shared for educational purposes. Please respect intellectual property and use responsibly.

---

## 🌟 Acknowledgments

- EC-Council for CEH curriculum
- Open-source security tool developers
- Cybersecurity community for knowledge sharing
- Metasploit Framework team
- Offensive Security for education resources

---

<div align="center">

**⭐ Star this repo if you find it helpful!**

Made with ❤️ for the cybersecurity community

</div>

---

## 📚 Additional Resources

### Official Documentation
- [EC-Council CEH Official Site](https://www.eccouncil.org/programs/certified-ethical-hacker-ceh/)
- [Metasploit Documentation](https://docs.metasploit.com/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

### Security Organizations
- [OWASP](https://owasp.org/)
- [Cybersecurity & Infrastructure Security Agency (CISA)](https://www.cisa.gov/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [SANS Institute](https://www.sans.org/)

### Learning Platforms
- [TryHackMe](https://tryhackme.com/)
- [HackTheBox](https://www.hackthebox.com/)
- [PentesterLab](https://pentesterlab.com/)
- [VulnHub](https://www.vulnhub.com/)

---

## 🏆 Achievements

- ✅ **3 Labs Completed** - System Hacking Module
- 🎯 **Multiple Attack Vectors** - Password Attacks, Remote Access, Privilege Escalation
- 🛡️ **Defense Strategies** - Documented countermeasures for each attack
- 📖 **Comprehensive Documentation** - Professional write-ups with detailed analysis

---

## 🔮 Upcoming Labs

Stay tuned for upcoming labs covering:
- 🔄 Network Scanning with Nmap
- 🔄 Web Application Attacks (SQL Injection, XSS)
- 🔄 Wireless Network Hacking
- 🔄 Social Engineering Techniques
- 🔄 Malware Analysis
- 🔄 Cryptography Attacks

---

*Happy Learning! Stay Ethical! 🔐*

**Remember: With great power comes great responsibility. Use your skills wisely!**
