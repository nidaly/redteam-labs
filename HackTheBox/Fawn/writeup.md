# Hack The Box - Fawn Writeup

**Target IP:** 10.129.199.247  
**Machine:** Fawn  
**Category:** Enumeration / FTP Basics  

---

## 1. Reconnaissance

We begin by checking connectivity to the target:

```bash
ping 10.129.199.247
```

The target responds, confirming it is reachable.

---

## 2. Port Scanning

We run an initial Nmap scan to identify open ports:

```bash
nmap -sC -sV 10.129.199.247
```

### Observed Result

- Port 21/tcp → FTP (vsftpd)
- Anonymous login is allowed

---

## 3. Service Enumeration (FTP)

Since FTP is open, we attempt anonymous login:

```bash
ftp 10.129.199.247
```

When prompted:

```
Name: anonymous
Password: (press Enter)
```

Login is successful.

---

## 4. Exploring the FTP Server

After logging in, we list files:

```bash
ls
```

We find:

```
flag.txt
```

---

## 5. Retrieving the Flag

We download the file using:

```bash
get flag.txt
```

Then exit FTP:

```bash
bye
```

---

## 6. Reading the Flag

On our local machine:

```bash
cat flag.txt
```

### Flag Output

```
[REDACTED - HTB FLAG]
```

---

## 7. Key Takeaways

- Always start with full port enumeration using `nmap -sC -sV`.
- FTP misconfigurations (especially anonymous login enabled) are common in beginner machines.
- Simple file exposure can lead directly to full compromise in basic environments like Fawn.

---

## 8. Summary

The machine was solved purely through:

- Service discovery  
- Anonymous FTP login  
- File retrieval  

No exploitation was required — only misconfiguration discovery.

---

## 9. Lessons Learned

- Never allow anonymous FTP access in production environments.
- Always restrict directory listing and file access on exposed services.
- Even simple services can leak sensitive data if misconfigured.
