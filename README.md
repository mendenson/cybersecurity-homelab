# Cybersecurity Homelab

This repository documents my cybersecurity homelab journey, focused on building practical skills for SOC, Blue Team, networking, Linux administration, and security analysis.

## Lab Environment

- Host: Windows PC
- Virtualization: Oracle VirtualBox
- VMs:
  - Ubuntu
  - Kali Linux
- Tools:
  - Git
  - GitHub
  - VS Code
  - SSH
  - Nmap

## Labs

| Lab | Description | Status |
|---|---|---|
| Lab 01 | [VirtualBox Network, SSH and Nmap Scan](https://github.com/mendenson/cybersecurity-homelab/blob/main/labs/lab01-virtualbox-network-ssh-nmap.md) | Completed |
| Lab 02 | [Linux Hardening Basics with UFW and Nmap Validation]() | Completed |
| Lab 03 | [SSH Hardening with Key-Based Authentication](https://github.com/mendenson/cybersecurity-homelab/blob/main/labs/lab03-ssh-hardening.md) | Completed |
|Lab 04 | Linux User and Permission Management | |
|Lab 05 | Linux Log Monitoring with Auth Logs | |
|Lab 06 | Installing and Testing Fail2Ban | |
|Lab 07 | Docker Setup and Vulnerable Web App Deployment | |
|Lab 08 | Web Application Scanning with Nmap and Nikto | |
|Lab 09 | Basic Incident Investigation Using Linux Logs | |

## Lab 01 Summary

In Lab 01, I configured a basic virtual network using VirtualBox, connected Ubuntu and Kali/Parrot through a Host-only network, enabled SSH on Ubuntu, and validated communication using ping, SSH, and Nmap.

## Lab 02 Summary

In this lab, I hardened an Ubuntu VM by enabling UFW firewall, allowing only SSH traffic, and validating the result with Nmap before and after the configuration. The final scan showed that only SSH remained open while all other scanned TCP ports were filtered.

## Lab 03 Summary

In this lab, I hardened SSH access on the Ubuntu VM by enabling key-based authentication, disabling password authentication, blocking root login, and validating the final configuration from the Kali VM. The final Nmap scan confirmed that SSH remained open on port 22, while authentication was improved by allowing only SSH key-based access.