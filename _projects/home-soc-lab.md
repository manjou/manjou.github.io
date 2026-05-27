---
title: Home SOC Lab
description: Personal detection lab running Wazuh SIEM on Ubuntu Server with multiple endpoints, used for continuous threat detection practice.
status: In Progress
status_class: active
date: 2026-02-01
tags:
  - Wazuh
  - Ubuntu Server
  - Kali Linux
  - VirtualBox
---

## Overview

An ongoing personal lab environment used to practice real-world SOC analyst workflows — log collection, alert triage, and detection tuning — outside of the structured capstone project.

## Architecture

- **Wazuh Manager + Dashboard** running on Ubuntu Server 22.04 in VirtualBox
- Multiple endpoint agents on Windows and Linux VMs
- Kali Linux attacker machine for generating realistic telemetry

## What I Practice

- Writing custom Wazuh detection rules
- Investigating Wazuh alerts and correlating events
- Running attack scenarios (port scans, brute force, privilege escalation) and tracing them through logs
- Network packet analysis with Wireshark

## Ongoing Goals

- Add a simulated phishing campaign and track the email/web telemetry through the SIEM
- Integrate a ticketing workflow to simulate real SOC case management
- Document detection logic in a runbook format
