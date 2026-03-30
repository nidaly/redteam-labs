# HTB - Meow

## 📌 Overview
- **Platform:** Hack The Box
- **Difficulty:** Easy
- **Machine Name:** Meow
- **Date Completed:** 30th March 2026

---

## 🎯 Objective
Gain initial access to the target machine and identify any privilege escalation opportunities.

---

## 🌐 Target Information
- **Target IP:** 10.129.64.116  

---

## 🔍 Reconnaissance

### Nmap Scan
```bash
nmap -p- -sV -T4 10.129.64.116 -vv
```
## 📊 Results
- **Port 23 (Telnet)** → Open  
- **Service:** Telnet  

---

## 📡 Enumeration

- Identified **Telnet service running on port 23**
- Telnet is an **unencrypted remote login protocol**
- Commonly misconfigured in vulnerable systems  

### 🔎 Key Observation
- Presence of Telnet suggests possible weak or no authentication  

---

## 🚪 Exploitation

### Step 1: Connect to Telnet
```bash
telnet 10.129.64.116
```
### Step 2: Attempt Login
- Tried logging in as:
  - `root`

### ✅ Result
- Successfully logged in **without a password**

---

## 🔐 Privilege Escalation

- Not required  
- Direct **root access** was obtained upon login  

---

## 🚨 Vulnerability Identified

### Misconfigured Telnet Service

- Telnet exposed to network  
- Root login allowed without authentication  
- No encryption used (credentials can be intercepted)  

---

## 🛡️ Detection & Mitigation

### 🔍 Detection
- Monitor:
  - Traffic on **port 23 (Telnet)**
  - Unauthorized root login attempts  

- SIEM Alerts:
  - Telnet usage in modern infrastructure  
  - Unusual remote login patterns  

### 🛠️ Mitigation
- Disable Telnet service  
- Use **SSH (Secure Shell)** instead  
- Enforce strong authentication mechanisms  
- Restrict root login access  
- Apply network access controls (firewall rules)  

---

## 🧠 Key Learnings

- Enumeration is critical in identifying attack vectors  
- Legacy protocols like Telnet are insecure  
- Misconfigurations can lead to full system compromise  
- Always test for weak authentication mechanisms  
