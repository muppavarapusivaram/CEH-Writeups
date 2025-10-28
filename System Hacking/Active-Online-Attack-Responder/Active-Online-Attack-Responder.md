# CEH Lab Documentation: Active Online Attack using Responder

## Lab Overview
**Lab Title:** Perform Active Online Attack to Crack System Password using Responder  
**Attack Type:** LLMNR/NBT-NS Poisoning  
**Difficulty:** Intermediate  
**Estimated Time:** 30 minutes

---

## Objective
Extract and crack password hashes from a Windows system by exploiting LLMNR (Link Local Multicast Name Resolution) and NBT-NS (NetBIOS Name Service) protocols using the Responder tool.

---

## Lab Environment

| Machine | OS | IP Address | Credentials |
|---------|-----|-----------|-------------|
| Attacker Machine | Parrot Security | 10.10.1.13 | attacker/toor |
| Target Machine | Windows 11 | 10.10.1.11 | Jason/qwerty |

---

## Theoretical Background

### LLMNR & NBT-NS Overview
- **LLMNR (Link Local Multicast Name Resolution)** and **NBT-NS (NetBIOS Name Service)** are Windows protocols used for name resolution on local networks
- Enabled by default in Windows operating systems
- Vulnerable to poisoning attacks due to lack of authentication

### Attack Mechanism
1. Attacker listens for LLMNR/NBT-NS broadcast requests
2. Attacker spoofs responses claiming to be the legitimate server
3. Victim system accepts the connection
4. Attacker captures authentication hashes (NTLM)

### Responder Tool
- LLMNR, NBT-NS, and MDNS poisoner
- Responds to specific NBT-NS queries based on name suffix
- Default response: File Server Service requests (SMB)
- Captures: OS version, client version, NTLM credentials

---

## Lab Steps

### Step 1: Initial Setup

**Target Machine (Windows 11):**
1. Switch to Windows 11 machine (10.10.1.11)
2. Press `Ctrl+Alt+Delete` to activate
3. Login as **Jason** with password **qwerty**
4. If privacy settings appear, click through: `Next` → `Next` → `Accept`

---

### Step 2: Launch Responder on Attacker Machine

**Attacker Machine (Parrot Security):**

1. Switch to Parrot Security machine
2. Open MATE Terminal from desktop

3. Run Responder:
```bash
sudo responder -I eth0
```

4. Enter password when prompted: `toor`

**Command Breakdown:**
- `-I eth0`: Specifies network interface (verify with `ifconfig` if needed)

**Expected Output:**
- Responder starts listening on the network interface
- Waiting for events to capture

---

### Step 3: Trigger Authentication Request

**Target Machine (Windows 11):**

1. Right-click on Start icon
2. Click **Run**
3. In the Open field, type:
```
\\CEH-Tools
```
4. Click **OK**
5. Leave the window open

---

### Step 4: Capture Password Hashes

**Attacker Machine (Parrot Security):**

1. Monitor Responder terminal output
2. Observe captured authentication attempt
3. Responder displays:
   - Target OS version
   - Client version
   - NTLM client IP address
   - Username
   - **NTLMv2 password hash**

**Log Location:**
```
/usr/share/responder/logs
```

4. Select and copy the captured hash value for user **Jason**

---

### Step 5: Save Hash for Cracking

1. Open new terminal window
2. Switch to root:
```bash
sudo su
```

3. Open text editor with new file:
```bash
pluma hash.txt
```

4. Enter password: `toor`
5. Paste the copied hash value
6. Save and close the file

---

### Step 6: Crack the Password Hash

1. In terminal, run John the Ripper:
```bash
john hash.txt
```

**Expected Output:**
- John the Ripper processes the hash
- Password displayed in plain text: **qwerty**

---

## Results & Findings

### Captured Information
- **Username:** Jason
- **Password Hash:** [NTLMv2 Hash captured via Responder]
- **Cracked Password:** qwerty
- **Target IP:** 10.10.1.11
- **Attack Success:** ✓ Complete

---

## Cleanup Steps

1. Close all terminal windows on Parrot Security
2. Switch to Windows 11 machine
3. Click Start → User Icon → **Sign out**
4. Close any Windows Security windows that appear

---

## Key Takeaways

### Attack Success Factors
- LLMNR/NBT-NS enabled by default in Windows
- Low awareness of this attack vector
- No authentication required for name resolution
- Automatic credential transmission

### Security Implications
- Internal network penetration testing effectiveness
- Password hash exposure through normal network activity
- Weak passwords easily cracked (e.g., "qwerty")

### Defense Recommendations
1. **Disable LLMNR/NBT-NS** if not required
2. Implement **SMB Signing**
3. Use **strong, complex passwords**
4. Enable **network access control (NAC)**
5. Monitor for suspicious authentication attempts
6. Implement **network segmentation**

---

## Tools Used

| Tool | Purpose | Command |
|------|---------|---------|
| Responder | LLMNR/NBT-NS poisoning | `sudo responder -I eth0` |
| John the Ripper | Password hash cracking | `john hash.txt` |
| Pluma | Text editor | `pluma hash.txt` |

---

## References & Additional Reading
- LLMNR/NBT-NS Poisoning Attack Vectors
- Responder Documentation
- John the Ripper Usage Guide
- Windows Name Resolution Security
- NTLM Authentication Protocols

---

## Lab Completion Status
- [x] Environment Setup
- [x] Responder Configuration
- [x] Hash Capture
- [x] Password Cracking
- [x] Documentation
- [x] Cleanup

**Lab Completed:** [Date]  
**Documented By:** [Your Name]

---