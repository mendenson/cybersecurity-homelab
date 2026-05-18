# Lab 02 - Linux Hardening Basics with UFW and Nmap Validation

## 1. Overview

This lab focuses on basic Linux hardening using an Ubuntu virtual machine as the target system and a Kali Linux virtual machine as the testing machine.

The main goal was to reduce the network exposure of the Ubuntu VM by enabling the UFW firewall, allowing only SSH traffic, and validating the results with Nmap before and after the hardening process.

This lab is part of my cybersecurity homelab journey focused on building practical skills in Linux administration, network security, Blue Team fundamentals, and security validation.

---
## 2. Lab Objective

The objectives of this lab were to:

- Verify the initial network exposure of the Ubuntu VM
- Identify open ports and running services using Nmap
- Update the Ubuntu system
- Install and enable UFW firewall
- Allow only SSH traffic through the firewall
- Validate SSH access after firewall activation
- Run a second Nmap scan after hardening
- Compare before-and-after scan results
- Document troubleshooting and lessons learned

---

## 3. Lab Environment

| Component | Description |
|---|---|
| Host Machine | Windows PC |
| Virtualization Platform | Oracle VirtualBox |
| Target Machine | Ubuntu VM |
| Testing Machine | Kali Linux |
| Network Type | Host-only Adapter |
| Lab Network | 192.168.56.0/24 |
| Ubuntu IP | 192.168.56.102 |
| Main Security Tool | UFW |
| Validation Tool | Nmap |
| Remote Access Service | SSH |

---

## 4. Network Design

The lab uses a Host-only network in VirtualBox to allow communication between the Ubuntu VM and the Kali/Parrot VM.

```text
Windows PC - Host Machine
        |
        | VirtualBox
        |
        |--- Ubuntu VM
        |       IP: 192.168.56.102
        |       Role: Target / Server
        |
        |--- Kali Linux
                Role: Testing / Scanner
                
```
The Ubuntu VM acts as the system being hardened, while the Kali/Parrot VM is used to test connectivity, SSH access, and port exposure.

---

## 5. Tools Used

| Tool | Purpose|
|---|---|
| Ubuntu Linux | Target System to be hardened |
| Kali Linux | Testing and scanning machine |
| UFW | Linux firewall configuration |
| Nmap | Port scanning and service detection |
| SSH | Remote access service |
| VirtualBox | Virtualization platform |
| Terminal | Command-line administration |

---

## 6. Initial Connectivity Test
Before starting the hardening process, I confirmed that both virtual machines were running at the same time and could communicate through the Host-only network.

From the Kali/Parrot VM, I tested connectivity to the Ubuntu VM:
```
ping 192.168.56.102
```
The ping test confirmed that the Ubuntu VM was reachable from the testing machine.

---

## 7. Initial Nmap Scan - Before Hardening
Before enabling the firewall, I ran an Nmap scan from the Kali/Parrot VM against the Ubuntu VM.

Command used:
```
nmap -sV -oN lab02-before-hardening.txt 192.168.56.102
```
### Result Before Hardening
```
Host is up.
Not shown: 999 closed tcp ports (reset)

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.0p2 Ubuntu
```
### Analysis Before Hardening
The initial scan showed that the Ubuntu VM had only one open TCP port:
| Port | State | Service | Description |
|---|---|---|---|
| 22/tcp | Open | SSH | Remote administration service |

The remaining 999 scanned TCP ports were reported as closed.

This means the Ubuntu VM already had a small attack surface, but the firewall was not yet actively filtering unwanted inbound traffic.

---

## 8. System Update
Before applying firewall rules, I updated the Ubuntu system.

Commands used:
```
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
```
Updating the system is an important hardening step because it ensures that installed packages receive the latest security patches and bug fixes.

---

## 9. Checking Active Services
To check listening services and open ports directly from the Ubuntu VM, I used:
```
sudo ss -tulnp
```
This helped confirm which services were listening for network connections.

I also checked running services with:
```
systemctl --type=service --state=running
```
The main network service exposed in this lab was SSH.

---

## 10. UFW Firewall Configuration
First, I checked the UFW status:
```
sudo ufw status verbose
```
Then I allowed SSH traffic before enabling the firewall:
```
sudo ufw allow ssh
```
or alternatively:
```
sudo ufw allow 22/tcp
```
After allowing SSH, I enabled UFW:
```
sudo ufw enable
```
Finally, I confirmed the firewall status:
```
sudo ufw status verbose
```
### Expected UFW Result
```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
```
This configuration allows SSH while filtering other unsolicited inbound connections.

---

## 11. SSH Validation After Firewall Configuration
After enabling UFW, I tested SSH access from the Kali/Parrot VM to the Ubuntu VM.

Command used:
```
ssh <ubuntu_user>@192.168.56.102
```
Example:
```
ssh ubuntu@192.168.56.102
```
The SSH connection worked successfully after enabling the firewall, confirming that the firewall rule for SSH was correctly configured.

---

## 12. Final Nmap Scan - After Hardening
After enabling UFW and allowing SSH, I ran another Nmap scan from the Kali/Parrot VM against the Ubuntu VM.

Command used:
```
nmap -sV -oN lab02-after-hardening.txt 192.168.56.102
```
### Result After Hardening
```
Host is up.
Not shown: 999 filtered tcp ports (no-response)

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.0p2 Ubuntu
```

---

## 13. Before vs After Comparison

| Item | Before Hardening | After Hardening |
|---|---|---|
| Host Reachable | Yes | Yes |
|SSH Port 22 | Open |Open |
|Other TCP Ports | Closed | Filtered |
| Firewall Bahavior | Ports responded as closed | Port did not respond |
| UFW Status | Not active / not configured | Active |
| SSH Access | Working | Working |
| Attack Surface | SSH exposed | Only SSH allowed |

---

## 14. Security Analysis
Before hardening, Nmap reported 999 TCP ports as closed. This means the Ubuntu VM responded to connection attempts by indicating that those ports were closed.

After enabling UFW, Nmap reported 999 TCP ports as filtered. This means the firewall did not respond to connection attempts on those ports.

This is an important security improvement because filtered ports reveal less information to a scanning machine.

The only open port after hardening was:
```
22/tcp open ssh
```
This was expected because SSH was intentionally allowed for remote administration.

The final result confirms that UFW was active and filtering unwanted inbound traffic while still allowing legitimate SSH access.

---

## 15. Troubleshooting
### Issue 1 - Destination Host Unreachable
During the initial lab setup, I received the following error:
```
Destination Host Unreachable
```
#### Cause
Only one virtual machine was running during the connectivity test.

For Host-only communication between two virtual machines, both VMs must be powered on at the same time.
#### Solution
I started both the Ubuntu VM and the Kali at the same time and repeated the ping test using the Host-only IP address.

After that, communication between the VMs worked correctly.

### Issue 2 - SSH Permission Denied
During the SSH test, authentication failed with:
```
Permission denied (publickey,password).
```
#### Cause
The SSH service was reachable, but the login attempt failed because of incorrect credentials or using the wrong username/password combination.
#### Solution
I confirmed the correct Ubuntu username and used the Ubuntu user's password when connecting from Kali.

After correcting the credentials, SSH access worked successfully.

---

## 16. Key Commands Used
### Ubuntu VM
```
ip a
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
sudo ss -tulnp
systemctl --type=service --state=running
sudo ufw status verbose
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose
```
### Kali
```
ping 192.168.56.102
nmap -sV -oN lab02-before-hardening.txt 192.168.56.102
ssh <ubuntu_user>@192.168.56.102
nmap -sV -oN lab02-after-hardening.txt 192.168.56.102
```

---

## 17. Evidence Collected

The following evidence was collected during this lab:

| Evidence | Description |
|---|---|
| [lab02-before-hardening.txt](https://github.com/mendenson/cybersecurity-homelab/blob/main/scams/lab02-before-hardening.txt) |Nmap scan before enabling UFW |
| [lab02-after-hardening.txt](https://github.com/mendenson/cybersecurity-homelab/blob/main/scams/lab02-after-hardening.txt) | Nmap scan after enabling UFW |
| [UFW status screenshot](https://github.com/mendenson/cybersecurity-homelab/blob/main/screenshots/lab02-ufw-status.png) | Firewall enabled and SSH allowed |
| [SSH test screenshot](https://github.com/mendenson/cybersecurity-homelab/blob/main/screenshots/lab02-ssh-test.png) | Successful SSH connection |
| [Nmap after-hardening screenshot](https://github.com/mendenson/cybersecurity-homelab/blob/main/screenshots/lab02-nmap-after-hardening.png) | Final validation scan |



---

## 18. What I Learned

In this lab, I learned how to:

- Use Nmap to identify open ports and services
- Interpret Nmap port states such as open, closed, and filtered
- Update an Ubuntu Linux system
- Check listening services with ss
- Configure UFW firewall rules
- Allow SSH while blocking other inbound traffic
- Validate firewall behavior using before-and-after scans
- Troubleshoot SSH authentication issues
- Document technical findings in a professional format

---

## 19. Conclusion

This lab successfully demonstrated basic Linux hardening using UFW.

The Ubuntu VM was scanned before and after firewall configuration. Before hardening, Nmap reported 999 closed TCP ports and one open SSH port. After enabling UFW and allowing only SSH, Nmap reported 999 filtered TCP ports and only SSH remained open.

This confirms that the firewall was working as expected by filtering unwanted inbound traffic while still allowing secure remote administration through SSH.

This lab strengthened my understanding of Linux security basics, firewall configuration, service exposure, and validation using Nmap.