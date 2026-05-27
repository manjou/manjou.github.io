---
layout: post
title: "Setting Up Wazuh SIEM in a Home Lab"
description: "A step-by-step walkthrough of deploying Wazuh 4.x on Ubuntu Server in VirtualBox, including agent enrollment and first alert."
category: Lab Notes
date: 2026-04-10
---

Running your own SIEM is one of the most practical things you can do as an aspiring SOC analyst. This post walks through how I set up Wazuh in my home lab — the decisions I made, the problems I hit, and what I learned.

## Why Wazuh

Wazuh is open-source, actively maintained, and mirrors the architecture of enterprise SIEM products closely enough to be genuinely educational. It handles log collection, file integrity monitoring, vulnerability detection, and active response — all in one stack.

The alternative I considered was Splunk's free tier, but the 500MB/day ingest limit felt too restrictive for meaningful attack simulation. Wazuh has no such limit.

## Lab Architecture

```
Host: macOS M3
VMs via VirtualBox:
  - Ubuntu Server 22.04 (Wazuh Manager + Indexer + Dashboard)
  - Windows 11 ARM (Wazuh Agent)
  - Kali Linux 2024 (attacker)
Network: Host-only adapter (192.168.56.0/24)
```

## Installation Steps

### 1. Provision Ubuntu Server

Allocate at least 4GB RAM and 50GB disk — Wazuh's OpenSearch indexer is memory-hungry. I used the minimal server install and updated packages first:

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Wazuh All-in-One

Wazuh provides an assisted installer:

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

The script installs the Manager, Indexer, and Dashboard. When it completes, it outputs the dashboard admin credentials — **save these immediately**.

### 3. Enroll a Windows Agent

On the Windows 11 VM, download the Wazuh agent MSI and run the enrollment command with your manager's IP:

```powershell
.\wazuh-agent-4.7.0-1.msi /q WAZUH_MANAGER="192.168.56.10"
```

Start the service and within a few minutes you'll see the agent appear in the dashboard.

## First Alerts

Once the Windows agent was enrolled, I ran a basic Nmap scan from Kali against the Windows VM:

```bash
nmap -sV -p 1-1000 192.168.56.20
```

The Wazuh dashboard immediately flagged the scan as a network anomaly under rule group `network_scan`. The alert included the source IP, timestamp, and rule description.

## What I Learned

- Wazuh's default ruleset is broad but not tuned. You'll get noise from legitimate activity — the real skill is in adjusting `local_rules.xml` to reduce false positives.
- The OpenSearch indexer requires careful memory tuning in `/etc/wazuh-indexer/jvm.options`. I set the heap to 2GB.
- Agent enrollment over NAT requires configuring the manager's `ossec.conf` to accept the correct network range.

## Next Steps

I'm working on writing custom detection rules for specific attack scenarios and integrating Shuffle (an open-source SOAR) to automate basic triage responses. I'll write about that next.
