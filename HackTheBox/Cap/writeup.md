# HTB - Cap

## 📌 Overview
- **Platform:** Hack The Box
- **Difficulty:** Easy
- **Machine Name:** Cap
- **Date Completed:** 31st March 2026

---

## 🎯 Objective
Gain initial access to the target machine and escalate privileges to root.

---

## 🌐 Target Information
- **Target IP:** [Add IP]

---

## 🔍 Reconnaissance

### Nmap Scan
```bash
nmap -sC -sV <target-ip>
```

### 📊 Results
- **Port 21 (FTP)** → Open  
- **Port 22 (SSH)** → Open  
- **Port 80 (HTTP)** → Open  

---

## 📡 Enumeration

### 🌐 Web Enumeration (Port 80)
- Accessed web application via browser  
- Observed dashboard with network capture functionality  
- Application allows downloading `.pcap` files  

### 🔎 Key Observation
- Capture files are stored with incremental IDs:

```
/data/0
/data/1
/data/2
```

- Possible **IDOR (Insecure Direct Object Reference)** vulnerability  

---

## 🚪 Exploitation

### Step 1: Exploit IDOR
- Modified URL to access other users' capture files:

```
http://<target-ip>/data/0
```

### Step 2: Download PCAP File
- Retrieved `.pcap` file containing network traffic  

### Step 3: Analyze PCAP
```bash
wireshark capture.pcap
```

### Findings
- Discovered FTP credentials in plaintext:
  - **Username:** nathan  
  - **Password:** [Found in capture]  

---

## 🔑 Initial Access

### FTP Login
```bash
ftp <target-ip>
```

- Logged in using discovered credentials  
- Access successful  

---

## 🔐 Privilege Escalation

### Step 1: SSH Access
```bash
ssh nathan@<target-ip>
```

- Gained shell access  

---

### Step 2: Check Capabilities
```bash
getcap -r / 2>/dev/null
```

### Findings
- Python binary has special capability:
  - `cap_setuid`  

---

### Step 3: Exploit Capability
```bash
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

### Result
- Privilege escalated to **root**  

---

## 🚨 Vulnerabilities Identified

### 1. IDOR (Insecure Direct Object Reference)
- Unauthorized access to other users' data via predictable IDs  

### 2. Sensitive Data Exposure
- Credentials exposed in network capture file  

### 3. Misconfigured Linux Capabilities
- Python binary allowed privilege escalation  

---

## 🛡️ Detection & Mitigation (SOC Perspective ⭐)

### 🔍 Detection
- Monitor:
  - Unusual access to `/data/*` endpoints  
  - Download of multiple capture files  
  - Suspicious use of `getcap`  

- SIEM Alerts:
  - Access control violations (IDOR patterns)  
  - Credential exposure in logs/traffic  
  - Privilege escalation behavior  

---

### 🛠️ Mitigation
- Implement proper **access controls** for user data  
- Avoid storing sensitive data in plaintext  
- Restrict access to `.pcap` files  
- Remove unnecessary Linux capabilities  
- Use secure credential handling practices  

---

## 🧠 Key Learnings

- IDOR is a common and critical web vulnerability  
- Sensitive data exposure can lead to full compromise  
- Linux capabilities can be abused for privilege escalation  
- Always analyze downloadable files for hidden data  
