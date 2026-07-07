---
title: gcp-cloudscope
description: A live GCP asset-inventory and GRC compliance pipeline, plus a GDPR RAG chatbot — a personal sandbox for the same pattern used at panos.ai.
status: Completed
status_class: completed
github: https://github.com/manjou/gcp-cloudscope
date: 2026-07-04
tags:
  - Google Cloud Platform
  - Cloud Asset Inventory
  - Python
  - Vertex AI / Gemini
  - Cloud SQL / pgvector
  - Flask
  - GDPR / EU AI Act
  - NIST CSF 2.0
---

## Overview

gcp-cloudscope is a real GCP project run against my own personal cloud account — not a mockup — built around two pipelines: an asset-inventory-to-compliance-report pipeline, and a GDPR retrieval-augmented-generation (RAG) chatbot. The goal was to mirror, in miniature, the same asset-inventory and compliance-classification work I do at my panos.ai internship, on infrastructure I fully control and could afford to break.

## Pipeline 1 — Asset Inventory & GRC Compliance Report

1. **Live scan** (`scanner.py`) — pulls every resource in the project via the Cloud Asset Inventory API (`SearchAllResources`), read-only, using a service account scoped to exactly `roles/cloudasset.viewer`.
2. **Canonical transform** (`gcp_extractor.py`) — maps GCP's raw resource types onto a canonical asset schema (`vm`, `database`, `storage`, `ai-endpoint`, …), the same schema shape used across the multi-cloud asset-inventory work at panos.ai.
3. **Rule-driven classification** (`report_generator.py` + `grc_rules.yaml`) — every asset gets a sensitivity level and a regulatory scope (GDPR, EU AI Act) purely from a YAML rules file, no logic changes needed to update policy. Live firewall rules are evaluated separately (fetched via `gcloud compute firewall-rules list`, since the asset-search API doesn't expose `sourceRanges`) against a small network-exposure ruleset — flagging any management port (22/3389) left open to `0.0.0.0/0` as a HIGH finding.
4. **Static HTML report** — a self-contained compliance report, no server required, showing exactly what's in scope, why, and what's still exposed.

**Two real bugs, not staged ones:** the first version of the report flagged an already-remediated ICMP rule as a false HIGH finding (a portless protocol was slipping past a port-specific rule); the second version showed every asset as "unclassified" because the rules file was written against GCP's raw type strings while the canonical schema had already normalized them. Both were config-drift bugs between two layers of the same pipeline that were supposed to agree on vocabulary — neither surfaced as a crash, only as a wrong-looking number, which is the harder kind of bug to catch.

## Security finding: the exposure nobody created

Scanning the project surfaced roughly 40 unused subnets spread across every GCP region — an artifact of GCP's default VPC auto-provisioning a subnet per region at project creation — and two firewall rules open to the entire internet: `default-allow-rdp` (TCP 3389, for a project running zero Windows machines) and `default-allow-ssh` (TCP 22, unrestricted). Remediated via the `gcloud` CLI rather than the console, so the fix is reproducible: deleted the RDP rule outright, and restricted SSH to a single source IP. Verified the fix two ways — `firewall-rules describe` (not the ambiguous `list` output) and `get-effective-firewalls` at the instance level, to prove nothing was being inherited from an organization policy above the project.

## Pipeline 2 — GDPR Compliance Chatbot (RAG)

A small retrieval-augmented-generation service: GDPR article text is embedded once via Vertex AI (`text-embedding-004`) into a Cloud SQL/pgvector table; a question comes in through a minimal HTML chat page, gets embedded the same way, semantically matched against the stored articles, and answered by Gemini using only the matched regulation text as context — with every question and answer logged to an audit table. The point of the RAG approach over asking Gemini directly: the answer is grounded in the actual regulation text, and every response cites which article it came from.

## Security practices carried through the whole project

- **No key files** — every GCP call authenticates via Application Default Credentials; nothing to leak because nothing is written to disk.
- **Secrets fail loud** — Cloud SQL config lives in `.env` only, with no hardcoded fallback, so missing configuration crashes at startup rather than misbehaving silently.
- **A leaked secret found early got rotated, not just deleted** — a Cloud SQL password briefly present in git history was rotated via `gcloud sql users set-password` before the history was scrubbed with `git filter-repo`; scrubbing alone would only have been cosmetic.
- **Same treatment applied again before this repo went public** — a real GCP project ID, project number, and a personal IP address were found in tracked files and git history during the pre-publish review, and were redacted with the same `filter-repo` approach, done while the repo still had zero remote (the safest possible moment to rewrite history).

## Skills demonstrated

- Cloud Asset Inventory API usage with least-privilege service accounts
- Rule-driven GRC classification decoupled from code (YAML policy, not hardcoded logic)
- Firewall/network exposure auditing and CLI-based remediation, independently verified
- Retrieval-augmented generation (Vertex AI embeddings + pgvector + Gemini)
- Git history remediation (credential rotation, `git filter-repo`) — used twice, for two separate real findings
