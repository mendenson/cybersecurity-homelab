# Lab 01 - VirtualBox Network, SSH and Nmap Scan

## 1. Objective

The objective of this lab was to build the first version of my cybersecurity homelab using VirtualBox with two Linux virtual machines:

- Ubuntu VM as the target machine
- Kali Linux / Parrot OS VM as the testing machine

The main goal was to configure network communication between both virtual machines, enable SSH on Ubuntu, and validate connectivity using ping, SSH, and Nmap.

---

## 2. Lab Environment

| Component | Description |
|---|---|
| Host Machine | MacBook Intel |
| Virtualization Platform | Oracle VirtualBox |
| Target VM | Ubuntu |
| Testing VM | Kali Linux / Parrot OS |
| Network Type 1 | NAT |
| Network Type 2 | Host-only Adapter |
| Host-only Network | vboxnet0 |
| Internal Lab Network | 192.168.56.0/24 |

---

## 3. Network Design

The lab uses two network adapters for each virtual machine:

| Adapter | Type | Purpose |
|---|---|---|
| Adapter 1 | NAT | Provides internet access to the VM |
| Adapter 2 | Host-only Adapter | Allows communication between the host and VMs inside the lab |

Basic network diagram:

```text
Internet
   |
Home Router
   |
PC Windows - Host Machine
   |
VirtualBox
   |
Host-only Network: 192.168.56.0/24
   |
   |---- Ubuntu VM
   |
   |---- Kali/Parrot VM
   ```

## 4. VirtualBox Network Configuration

Both VMs were configured with the following network settings:

### Adapter 1
```
Attached to: NAT
Purpose: Internet access
```
### Adapter 2
```
Attached to: Host-only Adapter
Name: vboxnet0
Purpose: Internal lab communication
```
The Host-only network was configured as:
```
Network: 192.168.56.0/24
DHCP Range: 192.168.56.100 - 192.168.56.199
```
## 5. IP Address Verification

The IP addresses were checked using:
```
ip a
```
Screenshots:
```
screenshots/ubuntu-ip-a.png
screenshots/kali-ip-a.png
```
## 6. Connectivity Test with Ping
From Kali/Parrot to Ubuntu:
```
ping <UBUNTU_HOST_ONLY_IP>
```
From Ubuntu to Kali/Parrot:
```
ping <KALI_HOST_ONLY_IP>
```
Result:
```
Both virtual machines were able to communicate with each other through the Host-only network.
```
Screenshot:
```
screenshots/ping-test.png
screenshots/ping-test2.png
```
## 7. SSH Setup on Ubuntu
SSH was installed and enabled on the Ubuntu VM:
```
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```
## 8. SSH Connection Test
From Kali/Parrot, I connected to the Ubuntu VM using SSH:
```
ssh <ubuntu_user>@<UBUNTU_HOST_ONLY_IP>
```
Result:
```
SSH connection from Kali/Parrot to Ubuntu was successful.
```
Screenshot:
```
screenshots/ssh-test.png
```
## 9. Nmap Scan
From Kali/Parrot, I scanned the Ubuntu VM:
```
nmap -sV <UBUNTU_HOST_ONLY_IP>
```
Expected result:
```
Port 22/tcp open - SSH service detected
```
Screenshot:
```
screenshots/nmap-scan.png
```
## 10. Troubleshooting
### Problem
During the first test, the ping returned:
```
Destination Host Unreachable
```
### Cause
Only one virtual machine was running during the connectivity test.
### Solution
Both virtual machines need to be powered on at the same time when testing communication between them.

After starting both VMs, the ping test worked successfully.

## 11. What I Learned
In this lab, I learned how to:

- Configure NAT and Host-only networking in VirtualBox
- Identify Linux network interfaces using ip a
- Test connectivity between two virtual machines
- Install and enable SSH on Ubuntu
- Connect from Kali/Parrot to Ubuntu using SSH
- Run a basic Nmap service scan
- Troubleshoot a common networking issue in a virtual lab environment

## 12. Next Steps
The next labs will include:

- Creating a vulnerable target machine
- Running deeper Nmap scans
- Installing Docker on Ubuntu
- Deploying a vulnerable web application
- Capturing and analyzing network traffic with Wireshark
- Documenting findings in a professional cybersecurity report format