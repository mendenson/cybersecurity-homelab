# Lab 03 - SSH Hardening with Key-Based Authentication

## 1. Overview

This lab focuses on hardening SSH access on an Ubuntu virtual machine in a cybersecurity homelab environment.

The Ubuntu VM was used as the target/server machine, while the Kali Linux VM was used as the testing/client machine. The main goal was to improve SSH security by replacing password-based authentication with SSH key-based authentication.

This lab demonstrates an important Linux security practice commonly used in real environments: reducing the risk of brute-force attacks by disabling password authentication and allowing access only through SSH keys.

---

## 2. Lab Objective

The objectives of this lab were to:

- Verify SSH access before hardening
- Create a backup of the SSH server configuration file
- Generate an SSH key pair on the Kali VM
- Copy the public SSH key to the Ubuntu VM
- Confirm key-based SSH login
- Disable SSH password authentication
- Disable root login over SSH
- Disable empty passwords
- Reduce the number of allowed authentication attempts
- Validate the SSH configuration before restarting the service
- Confirm that SSH still works after hardening
- Confirm that password-based login is blocked
- Validate that port 22 remains open using Nmap
- Document the process and results for a cybersecurity portfolio

---

## 3. Lab Environment

| Component | Description |
|---|---|
| Host Machine | Windows PC |
| Virtualization Platform | Oracle VirtualBox |
| Target Machine | Ubuntu VM |
| Testing Machine | Kali Linux VM |
| Network Type | Host-only Adapter |
| Lab Network | 192.168.56.0/24 |
| Ubuntu IP Address | 192.168.56.102 |
| SSH Port | 22/tcp |
| Main Service Hardened | OpenSSH Server |
| Validation Tool | Nmap |

---

## 4. Network Design

The lab uses a Host-only network in VirtualBox. This allows the Kali VM and Ubuntu VM to communicate inside an isolated virtual lab network.

```text
Windows PC - Host Machine
        |
        | Oracle VirtualBox
        |
        |--- Ubuntu VM
        |       IP: 192.168.56.102
        |       Role: SSH Server / Target
        |
        |--- Kali Linux VM
                Role: SSH Client / Testing Machine
```
The Kali VM was used to connect to the Ubuntu VM through SSH and validate the final SSH configuration.

---
## 5. Tools Used

| Tool | Purpose |
|---|---|
| Ubuntu Linux | Target machine / SSH server |
| Kali Linux | Testing machine / SSH client |
| OpenSSH Server | Remote administration service |
| ssh-keygen | SSH key pair generation |
| ssh-copy-id | Copying the public key to the target machine |
| sshd_config | SSH server configuration file |
| systemctl	| Managing the SSH service |
| Nmap | Validating the SSH port and service |
| VirtualBox | Virtualization platform |

---

## 6. Initial SSH Connectivity Test

Before applying any hardening changes, I confirmed that SSH access from Kali to Ubuntu was working.

From the Kali VM:
```
ssh ubuntu@192.168.56.102
```
The SSH connection worked successfully using password authentication before the hardening process.

This confirmed that:

- The Ubuntu VM was reachable
- SSH was installed and running
- Port 22 was accessible
- The Kali VM could authenticate to the Ubuntu VM

---

## 7. SSH Configuration Backup

Before modifying the SSH configuration, I created a backup of the original sshd_config file.

On the Ubuntu VM:
```
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```
To confirm the backup:
```
ls -l /etc/ssh/sshd_config*
```
### Why this step matters

Creating a backup is important because SSH misconfiguration can break remote access. If something goes wrong, the original configuration can be restored with:
```
sudo cp /etc/ssh/sshd_config.bak /etc/ssh/sshd_config
sudo systemctl restart ssh
```

---

## 8. SSH Key Pair Generation

On the Kali VM, I generated a new SSH key pair using the Ed25519 algorithm.
```
ssh-keygen -t ed25519 -C "kali-to-ubuntu-lab03"
```
The default location was used:
```
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```
### Key files created
| File | Description |
|---|---|
|id_ed25519|Private key|
|id_ed25519.pub|Public key|
### Important security note

The private key must stay on the client machine and should never be shared.

The public key can be copied to the server.

---

## 9. Copying the Public Key to Ubuntu

From the Kali VM, I copied the public key to the Ubuntu VM using:
```
ssh-copy-id ubuntu@192.168.56.102
```
This command added the Kali public key to the Ubuntu user's authorized keys file:
```
~/.ssh/authorized_keys
```
After this step, the Kali VM was able to authenticate to the Ubuntu VM using the SSH key.

---

## 10. Testing Key-Based SSH Login

From the Kali VM, I tested SSH access again:
```
ssh ubuntu@192.168.56.102
```
The connection worked successfully using key-based authentication.

This confirmed that:

- The SSH key pair was generated correctly
- The public key was copied to the Ubuntu VM
- The Ubuntu SSH server accepted key-based authentication
- It was safe to proceed with disabling password authentication

---

## 11. SSH Server Hardening Configuration

On the Ubuntu VM, I edited the SSH server configuration file:
```
sudo nano /etc/ssh/sshd_config
```
The following settings were configured:
```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
PermitEmptyPasswords no
X11Forwarding no
MaxAuthTries 3
```
---
## 12. Explanation of SSH Hardening Settings
|Setting|Value|Purpose|
|---|---|---|
|PermitRootLogin|no|Prevents direct root login over SSH|
|PasswordAuthentication|no|Disables password-based SSH login|
|PubkeyAuthentication|yes|Allows SSH key-based authentication|
|PermitEmptyPasswords|no|Blocks accounts with empty passwords|
|X11Forwarding|no|Disables remote graphical forwarding|
|MaxAuthTries|3|Limits failed login attempts per connection|
### Security improvement

These settings reduce the risk of:

- Brute-force password attacks
- Unauthorized root access
- Weak password-based authentication
- Unnecessary SSH features being exposed
- Repeated authentication attempts

---
## 13. Validating the SSH Configuration

Before restarting the SSH service, I tested the configuration syntax:
```
sudo sshd -t
```
No errors were returned, which means the SSH configuration was valid.

This is an important step because restarting SSH with an invalid configuration could break remote access.

---
## 14. Restarting the SSH Service

After validating the configuration, I restarted the SSH service:
```
sudo systemctl restart ssh
```
A systemd warning appeared saying that the service file or configuration had changed on disk and recommended running:
```
sudo systemctl daemon-reload
```
After that, the SSH service was restarted again:
```
sudo systemctl restart ssh
```
The service status was checked with:
```
sudo systemctl status ssh
```
The SSH service was active and running.

---
## 15. Testing SSH Access After Hardening

After the SSH hardening changes, I tested normal SSH login again from the Kali VM:
```
ssh ubuntu@192.168.56.102
```
The login was successful.

This confirmed that SSH key-based authentication was working after password authentication was disabled.

---
## 16. Testing Password Authentication Blocking

To confirm that password authentication was disabled, I forced SSH to try password-based authentication only.

From the Kali VM:
```
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no ubuntu@192.168.56.102
```
### Expected result
```
Permission denied
```
This confirmed that password-based login was blocked and only key-based authentication was allowed.

---
## 17. Nmap Validation

After hardening SSH, I ran an Nmap scan from the Kali VM against the Ubuntu VM.

Command used:
```
nmap -sV -p 22 192.168.56.102 -oN lab03-ssh-hardening-nmap.txt
```
### Nmap Result
```
Starting Nmap 7.95
Nmap scan report for 192.168.56.102
Host is up.

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.0p2 Ubuntu
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap done: 1 IP address scanned
```
---
## 18. Important Analysis

The Nmap scan still shows port 22 as open:
```
22/tcp open ssh OpenSSH 10.0p2 Ubuntu
```
This is expected.

The goal of this lab was not to close the SSH port. The goal was to harden the authentication method used by SSH.

Before hardening:
```
SSH allowed password authentication.
```
After hardening:
```
SSH allows key-based authentication.
Password authentication is disabled.
Root login is disabled.
```
This means SSH is still available for administration, but access is more secure.

---
## 19. Before vs After Comparison
|Item|Before Hardening|After Hardening|
|---|---|---|
|SSH Port 22|Open|Open|
|Password Authentication|Allowed|Disabled|
|Public Key Authentication|Available|Enabled|
|Root Login|Default setting|Disabled|
|Empty Passwords|Default setting|Disabled|
|Max Authentication Attempts|Default setting|Limited to 3|
|SSH Access|Password-based login|Key-based login|
|Nmap Result|22/tcp open ssh|22/tcp open ssh|
---
## 20. Troubleshooting
### Issue 1 - Commented SSH Configuration Lines

Some SSH configuration lines had a # symbol at the beginning.

Example:
```
#PasswordAuthentication yes
#PubkeyAuthentication yes
```
### Cause

Lines starting with # are comments and are ignored by the SSH server.

Solution

The # symbol was removed and the values were changed explicitly:
```
PasswordAuthentication no
PubkeyAuthentication yes
```
### Issue 2 - SSH Service Restart Warning

After restarting SSH, the following warning appeared:
```
Warning: The unit file, source configuration file or drop-ins of ssh.service changed on disk.
Run 'systemctl daemon-reload' to reload units.
```
### Cause

Systemd detected that service-related files had changed on disk.

### Solution

I reloaded systemd and restarted SSH again:
```
sudo systemctl daemon-reload
sudo systemctl restart ssh
```
### Issue 3 - Incorrect SSH Option Syntax

While testing password authentication blocking, an incorrect SSH option was typed:
```
Bad configuration option: preferredauthentication
```
### Cause

The option was typed incorrectly. The correct option is:
```
PreferredAuthentications
```

with an s at the end.

### Solution

The corrected command was used:
```
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no ubuntu@192.168.56.102
```
---
## 21. Key Commands Used
### On Ubuntu VM
```
# Backup SSH configuration
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak

# Edit SSH configuration
sudo nano /etc/ssh/sshd_config

# Validate SSH configuration
sudo sshd -t

# Reload systemd
sudo systemctl daemon-reload

# Restart SSH
sudo systemctl restart ssh

# Check SSH service status
sudo systemctl status ssh
```
### On Kali VM
```
# Generate SSH key pair
ssh-keygen -t ed25519 -C "kali-to-ubuntu-lab03"

# Copy public key to Ubuntu
ssh-copy-id ubuntu@192.168.56.102

# Test normal SSH login
ssh ubuntu@192.168.56.102

# Test password authentication blocking
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no ubuntu@192.168.56.102

# Validate SSH service with Nmap
nmap -sV -p 22 192.168.56.102 -oN lab03-ssh-hardening-nmap.txt
```
---
## 22. Evidence Collected

The following evidence was collected during this lab:

|Evidence|Description|
|---|---|
|lab03-ssh-hardening-nmap.txt|Nmap scan showing SSH open on port 22|
|Screenshot of password login denied|Password authentication blocked|
|Screenshot of SSH service status|SSH service active and running|
---
## 23. What I Learned

In this lab, I learned how to:

- Generate SSH keys using ssh-keygen
- Copy public keys to a remote Linux server using ssh-copy-id
- Configure SSH key-based authentication
- Disable SSH password authentication
- Disable root login over SSH
- Validate SSH configuration with sshd -t
- Restart and verify the SSH service
- Test authentication behavior from a client machine
- Use Nmap to validate that SSH remains available
- Understand the difference between service exposure and authentication hardening
- Document a security configuration change professionally

---
## 24. Security Takeaways

Key-based SSH authentication is more secure than password-based authentication because it reduces the risk of brute-force login attempts.

Disabling root login also improves security because attackers cannot directly target the root account through SSH.

Limiting authentication attempts helps reduce repeated login attempts during a single SSH connection.

This lab shows that hardening does not always mean closing a service. In this case, SSH remained open, but the authentication method was made more secure.

---
## 25. Conclusion

This lab successfully hardened SSH access on the Ubuntu VM.

Before the hardening process, SSH password authentication was available. After generating an SSH key pair, copying the public key to the Ubuntu VM, and updating the SSH server configuration, password-based login was disabled and only key-based authentication remained available.

The final Nmap scan confirmed that port 22 remained open, which is expected because SSH is still required for remote administration. However, the authentication method was improved by disabling password login and root login.

This lab demonstrated an important Linux security practice: reducing the risk of brute-force attacks by enforcing SSH key-based authentication.