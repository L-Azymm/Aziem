
# Network Brute Force and Sniffing Walkthrough

## 🎯 Objectives

- Understand how brute-force attacks work on common network services (FTP, Telnet, SSH).
- Practice using tools like Hydra for password attacks.
- Learn how to use Wireshark to analyze network traffic.
- Identify insecure protocols that transmit data in plaintext.
- Propose secure alternatives and mitigation strategies.

---

## 🧰 Setup

- **Hydra** – for performing brute force attacks  
- **Wireshark** – for capturing and analyzing network traffic  
- **Kali Linux** – used as the attacker machine  
- **Metasploitable2** – vulnerable virtual machine  
- **FTP, Telnet, SSH** – services used for login and analysis  

---

## 🔍 Service Discovery (Optional Enumeration Step)

Use Nmap to identify running services on the target:

```bash
nmap -sV -p 21,22,23 192.168.154.133
```

### Explanation:
- `-sV`: Version detection (shows service version like vsftpd, OpenSSH)
- `-p`: Specifies ports to scan (21 = FTP, 22 = SSH, 23 = Telnet)
  
Result:  
- FTP running on port 21  
- Telnet on port 23  
- SSH on port 22

---

## 📍 Target IP Enumeration

Run the following command on **Kali Linux** to find the Metasploitable2 IP:

```bash
netdiscover
```

Or:

```bash
nmap -sn 192.168.154.0/24
```

Alternatively, on **Metasploitable2**:

```bash
ifconfig
```

---

## 1. Enumeration of Usernames🥐

Prepare a list of potential usernames and passwords to use for brute-force attacks.

### 1.1 Create a text file for usernames:

```bash
vim username.txt
```
Add the following potential usernames:
```
msfadmin
user
admin
root
test
guest
```
### 1.2 Create a text file for passwords:

```bash
vim password.txt
```

Add these potential passwords:
```
msfadmin
user
admin
root
test
guest
```
![Screenshot](https://github.com/L-Azymm/Labwork-1/blob/Image/Screenshot%202025-04-08%20122646.png?raw=true)

---
### Built-in List
Alternatively, you can use rockyou.txt, which is a popular wordlist for brute-force attacks, typically found in /usr/share/wordlists/rockyou.txt in Kali Linux. To use it with Hydra, you can run the following:
```bash
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ftp://192.168.154.133
```
## 2. Brute Force Attacks🥖

### 2.1 FTP
- **Tool**: Hydra
- **Command**:

```bash
hydra -L usernames.txt -P passwords.txt ftp://192.168.154.133
```

### Explanation:
- `-L`: Load a list of usernames from a file.
- `-P`: Load a list of passwords from a file.
- `ftp://`: Specifies the FTP service and target IP.

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-08%20122731.png?raw=true)

**Result**: Successful login found – `msfadmin:msfadmin`


---

### 2.2 Telnet
- **Tool**: Hydra
- **Command**:

```bash
hydra -l msfadmin -P passwords.txt telnet://192.168.154.133
```

### Explanation:
- `-l`: Specify a single username.
- `-P`: Load a list of passwords from a file.
- `telnet://`: Specifies the Telnet service and target IP.

![Screenshot](https://github.com/L-Azymm/Labwork-1/blob/Image/Screenshot%202025-04-08%20124126.png?raw=true)


**Result**: Successful login – `msfadmin:msfadmin`


---

### 2.3 SSH
- **Tool**: Hydra
- **Command**:

```bash
hydra -L usernames.txt -P passwords.txt ssh://192.168.154.133
```

### Explanation:
- `-L`: Load a list of usernames from a file.
- `-P`: Load a list of passwords from a file.
- `ssh://`: Specifies the SSH service and target IP.

**Problem**: Hydra (or the SSH client it's using) doesn't support the older key exchange algorithms used by the Meta2 machine, which is very outdated.

![Screenshot](https://github.com/L-Azymm/Labwork-1/blob/Image/Screenshot%202025-04-08%20125923.png?raw=true)

---

## 3. Sniffing Network Traffic🍖

### 3.1 Start Wireshark on Kali

- Open **Wireshark**.
- Select the active network interface (e.g., eth0 or ens33).
- Start capturing.

### 3.2 FTP Login Traffic Analysis

- Connect using:

```bash
ftp 192.168.154.133
```

- Login with: `msfadmin:msfadmin`

- In **Wireshark**, use the filter:

```bash
ftp
```

---

### 3.3 Telnet Login Traffic Analysis

- Connect using:

```bash
telnet 192.168.154.133
```

- Login with: `msfadmin:msfadmin`

- In **Wireshark**, use the filter:

```bash
telnet
```

---

## 4. Problems Encountered🍕

| Protocol | Problem | Solution |
|----------|---------|----------|
| FTP      | None    | N/A      |
| Telnet   | Service was off initially | Enabled Telnet on Metasploitable2 |
| SSH      | Hydra connection failed due to key mismatch | Use updated SSH client or fix key exchange settings on the target machine |

---

## 5. Mitigation Strategies🍥

| Protocol | Vulnerability | Secure Alternative | Why it’s better |
|----------|---------------|--------------------|-----------------|
| FTP      | Sends credentials in plaintext | SFTP / FTPS | Encrypts file transfer data and credentials |
| Telnet   | Transmits all in plaintext | SSH | SSH encrypts communication |
| SSH      | Still brute-forceable | Use key-based login, strong passwords | Prevents brute force access |

---

## ✅ Summary

- FTP and Telnet are insecure protocols that transmit credentials in plaintext.
- Hydra can easily brute-force weak credentials.
- SSH is secure but can still be targeted using brute-force unless hardened.
- Use tools like Wireshark to confirm data security over the network.
- Always replace insecure services with encrypted alternatives like SSH or FTPS.
- Apply proper access control, firewalls, and monitoring to reduce attack surfaces.

---
