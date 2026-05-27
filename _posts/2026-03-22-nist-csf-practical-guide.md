---
layout: post
title: "NIST CSF 2.0 — A Practical Read for Beginners"
description: "Breaking down the updated NIST Cybersecurity Framework for people who learn by doing, not by reading 50-page PDFs."
category: Frameworks
date: 2026-03-22
---

NIST CSF gets thrown around a lot in job postings and training programs. When I first encountered it, it felt abstract — a lot of governance language that didn't obviously connect to the hands-on technical work I was doing in the lab. This post is my attempt to make it concrete.

## What It Actually Is

The NIST Cybersecurity Framework is a voluntary framework for managing cybersecurity risk. Version 2.0, released in 2024, added a sixth function — **Govern** — to the original five. It's not a technical checklist; it's a structure for thinking about where your security program is and where it needs to go.

The six functions are:

| Function | What it means in practice |
|----------|---------------------------|
| **Govern** | Who owns security decisions? What's the risk tolerance? |
| **Identify** | What assets do you have? What are the risks? |
| **Protect** | What controls are in place to reduce risk? |
| **Detect** | How do you know when something bad is happening? |
| **Respond** | What's the plan when an incident occurs? |
| **Recover** | How do you get back to normal? |

## How I Used It in the Aurora Lab

When designing the Aurora AI Security lab, I used the CSF as a design checklist. For each function, I asked: does my lab architecture have *something* here?

**Identify** → Asset inventory of all VMs, their roles, and data classification.

**Protect** → pfSense firewall rules, VLAN segmentation, endpoint hardening.

**Detect** → Wazuh SIEM with custom rules, file integrity monitoring enabled.

**Respond** → Documented incident response playbooks (even if it's just me running the lab).

**Recover** → VM snapshots as a basic backup/recovery mechanism.

This exercise made the framework click. It stopped being a governance document and became a practical gap analysis tool.

## CSF vs. ISO 27001

A question I had early on: what's the difference between NIST CSF and ISO 27001?

- **ISO 27001** is a certification standard. Organizations get audited against it. It's more prescriptive and compliance-focused.
- **NIST CSF** is a risk management framework. It's more flexible and focuses on outcomes rather than specific controls.

In practice, many organizations use both — CSF for strategic risk management, ISO 27001 for certification and third-party assurance.

## Further Reading

- The official NIST CSF 2.0 documentation is surprisingly readable — start with the core functions document, not the full framework profile guide.
- CISA publishes CSF implementation guides for specific sectors (healthcare, energy, etc.) which are good for seeing real-world application.
