# Cybersecurity Homelab

This repository documents my cybersecurity homelab journey, focused on building practical skills for SOC, Blue Team, networking, Linux administration, and security analysis.

## Lab Environment

- Host: Windows PC
- Virtualization: Oracle VirtualBox
- VMs:
  - Ubuntu
  - Kali Linux / Parrot OS
- Tools:
  - Git
  - GitHub
  - VS Code
  - SSH
  - Nmap

## Labs

| Lab | Description | Status |
|---|---|---|
| Lab 01 | VirtualBox Network, SSH and Nmap Scan | Completed |
| Lab 02 | Linux Hardening Basics with UFW and Nmap Validation | Completed |

## Lab 01 Summary

In Lab 01, I configured a basic virtual network using VirtualBox, connected Ubuntu and Kali/Parrot through a Host-only network, enabled SSH on Ubuntu, and validated communication using ping, SSH, and Nmap.

## Lab 02 Summary

In this lab, I hardened an Ubuntu VM by enabling UFW firewall, allowing only SSH traffic, and validating the result with Nmap before and after the configuration. The final scan showed that only SSH remained open while all other scanned TCP ports were filtered.