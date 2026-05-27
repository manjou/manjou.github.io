---
title: Aurora AI Security
description: Capstone project — enterprise network design and SOC infrastructure for a simulated AI security company, built on a fully virtualized environment.
status: In Progress
status_class: active
github: https://github.com/manjou/aurora-ai-security
date: 2026-02-01
tags:
  - Wazuh SIEM
  - pfSense
  - Ubuntu Server
  - Kali Linux
  - Windows 11 ARM
  - VirtualBox / UTM
  - NIST CSF 2.0
  - GDPR / NIS2
  - Atomic Red Team
---

## Overview

Aurora AI Security is my Masterschool capstone project — a four-phase lab that simulates the security infrastructure of a fictional AI company called Aurora AI Security GmbH. The goal is to design, deploy, and validate an enterprise-grade security environment from scratch.

## Project Phases

### Phase 1 — Network Planning (Completed)

Designed a VLAN-segmented network (`192.168.64.0/24`) mapped to the NIST Cybersecurity Framework 2.0. Deliverables included:

- Network topology diagram with DMZ, internal subnets, and management VLAN
- IP addressing scheme and DHCP scoping
- Risk assessment aligned to GDPR and NIS2 requirements
- Incident response workflow design

### Phase 2 — Virtual Infrastructure (In Progress)

Standing up eight virtual machines on Apple M3 via UTM (QEMU):

- **pfSense CE** — perimeter firewall with VLAN routing
- **Alpine Linux** — virtual switch fabric
- **Wazuh 4.x** (Manager + Indexer + Dashboard) — SIEM stack
- **Ubuntu Server** — Wazuh host
- **Windows 11 ARM** — endpoint agent
- **Kali Linux** — attacker simulation

### Phase 3 — Security & SOC Planning (Upcoming)

Threat modeling using MITRE ATT&CK, detection rule development, and playbook authoring for common incident scenarios.

### Phase 4 — Implementation & Validation (Final)

Penetration testing of the lab environment, vulnerability scanning with OpenVAS/Nessus, and a full after-action report.

## Key Technical Decisions

- Chose **Wazuh** over a hosted SIEM for cost and hands-on experience with agent deployment and rule tuning
- Used **pfSense** because it closely mirrors enterprise firewall products and has strong documentation
- Attack simulation via **Atomic Red Team** rather than manual exploitation for reproducibility

## Skills Demonstrated

- SIEM deployment and configuration
- Firewall rule management
- Network segmentation and VLAN design
- Compliance framework mapping (NIST, GDPR, NIS2)
- Threat detection and log analysis
