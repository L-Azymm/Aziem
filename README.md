
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
  
Note: Target IP may vary between screenshots based on network setup. In this lab, both 192.168.154.133 and 192.168.204.147 refer to the Metasploitable2 VM

---

## 🔍 Service Discovery (Optional Enumeration Step)

Use Nmap to identify running services on the target:

```bash
nmap -sV -p 21,22,23 192.168.154.133
```
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20124316.png?raw=true)

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
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20125108.png?raw=true)

Or:

```bash
nmap -sn 192.168.154.0/24
```
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20125217.png?raw=true)

Alternatively, on **Metasploitable2**:

```bash
ifconfig
```
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20125237.png?raw=true)

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

Example of FTP hydra using rockyou.txt wordlist:

```bash
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt ftp://192.168.154.133
```

---

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
- **Tool**: Medusa
- **Command**:

```bash
medusa -h 192.168.204.147 -U usernames.txt -P passwords.txt -M ssh
```
### Explaination
- `-h`: Target host (your Metasploitable IP)
- `-U`: File containing usernames
- `-P`: 	File containing passwords
- `-M`: Module to use – in this case, ssh for brute-forcing SSH login
  
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20210422.png?raw=true)

Results: ACCOUNT FOUND: [ssh] Host: 192.168.204.147 User: msfadmin Password: msfadmin [SUCCESS]


### 2.4 HTTP

#### Setup

- Open Burp Suite
  - Start Burp Suite on Kali Linux.
    
    ```bash
    burpsuite
    ```

---

  - Use a Temporary Project
  - Burp Default
  - Start Burp
    
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20191451.png?raw=true)  

---

- Intercept
  
  - In the **Proxy** tab, turn on the intercept
    
    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20204722.png?raw=true)

    
---

- Open the Target Login Page
  
  - Example:
    
    ```bash
    http://testphp.vulnweb.com/login.php
    ```
    
    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20202556.png?raw=true)

  - In **Proxy** tab, foward the login request
    
    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20205121.png?raw=true)

  - For this lab, enter a wrong username and password first
    
    - Example: admin:password
      
      ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20202556.png?raw=true)

---

- In the **proxy** tab, you will see the request from the login page stating the username and the password
  
  - Send the request to the Intruder
    
    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20205307.png?raw=true)

---
- Open the **Intruder** tab
  
  - make sure tha attack type is set to **cluster**
    
  - select the username and password and click "Add" to mark them


    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20203025.png?raw=true)

---
- Go to **Payload** tab
  
  - Add the wordlist that you want to use
    
  - You will have 2 sets of payload each for username (Set 1) and password (Set 2)

    Username
    
    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20202644.png?raw=true)

    Password
    
    ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20202654.png?raw=true)

---

#### The Output of The attack

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20202919.png?raw=true)

#### Explaination

- A status code 200 with an unusually large response length likely indicates a successful login because it loads the full authenticated page, unlike failed attempts which return shorter responses

---

## 3. Sniffing Network Traffic🍖

### 3.1 Start Wireshark on Kali

- Open **Wireshark**.
  
- Select the active network interface (e.g., eth0 or ens33).
  
- Start capturing.
  
  ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20133655.png?raw=true)

### 3.2 FTP Login Traffic Analysis

Example: Target ip is 192.168.204.147

- Connect using:

```bash
ftp 192.168.204.147
```

  ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20134224.png?raw=true)

- Login with: `msfadmin:msfadmin`

- In **Wireshark**, use the filter:

```bash
ftp
```
  ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20134309.png?raw=true)

From the info, you can see:

- FTP server is ready. The server responds with its banner/version.
  
- USER (username): msfadmin
  
- The server acknowledges and asks for the password
  
- PASS (password): msfadmin
  
- A successful login

---

### 3.3 Telnet Login Traffic Analysis

- Connect using:

```bash
telnet 192.168.204.147
```
  ![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20135432.png?raw=true)

- Login with: `msfadmin:msfadmin`

- In **Wireshark**, use the filter:

```bash
telnet
```
or (for clearer capture)
```bash
telnet && ip.src == 192.168.204.147
```
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20143826.png?raw=true)

### Reminder
Telnet doesn't show the imput for username directly but instead shows in frames by frames for each letter
for example:
- m
- s
- f
- a
- d
- m
- i
- n

Example of the input of the letter:

- "m"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20171939.png?raw=true)

- "s"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172005.png?raw=true)

- "f"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172026.png?raw=true)

- "a"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172108.png?raw=true)

- "d"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172130.png?raw=true)

- "m"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172145.png?raw=true)

- "i"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172201.png?raw=true)

- "n"

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20172212.png?raw=true)

---

Password not being visible

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20143910.png?raw=true)

### 3.4 SSH Login Traffic Analysis
What you need to know:
- SSH is a secure, encrypted protocol designed to replace older, insecure protocols like Telnet.
- Unlike Telnet, SSH encrypts all data sent over the network, including the username, password, and any commands or output.

This resulting in:
- The username and password are encrypted when transmitted over the network.
- Wireshark will not be able to capture the plaintext of the username or password.

Run
```
ssh msfadmin@192.168.204.147
```
As we know the username of is "msfadmin"
![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20173834.png?raw=true)

In **wireshark** filter using:
```bash
tcp.port == 22
```

![Screenshot](https://github.com/L-Azymm/Aziem/blob/Image/Screenshot%202025-04-15%20173914.png?raw=true)

Easy to say, **You can see but wont recognize**

*"Things are not always what they seem; the first appearance deceives many."*
— Phaedrus

---

## 4. Problems Encountered🍕

| Protocol | Problem | Solution |
|----------|---------|----------|
| FTP      | None    | N/A      |
| Telnet   | Service was off initially | Enabled Telnet on Metasploitable2 |
| SSH      | Hydra connection failed due to key mismatch or outdated program | Use Hydra |

---

## 5. Mitigation Strategies🍥

| Protocol | Vulnerability | Secure Alternative | Why it’s better |
|----------|---------------|--------------------|-----------------|
| FTP      | Sends credentials in plaintext | SFTP / FTPS | Encrypts file transfer data and credentials |
| Telnet   | Transmits all in plaintext | SSH | SSH encrypts communication |
| SSH      | Still brute-forceable | Use key-based login, strong passwords | Prevents brute force access |
| HTTP     | Data sent in Plaintext | HTTPS | Data is encrypted |

---

## ✅ Summary

- FTP and Telnet are insecure protocols that transmit credentials in plaintext.
- Hydra can easily brute-force weak credentials.
- SSH is secure but can still be targeted using brute-force unless hardened.
- Use tools like Wireshark to confirm data security over the network.
- Always replace insecure services with encrypted alternatives like SSH or FTPS.
- Apply proper access control, firewalls, and monitoring to reduce attack surfaces.

---
