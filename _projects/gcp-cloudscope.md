---
title: Cloud Asset Inventory → Compliance Chatbot
description: From raw GCP resource data to a plain-language compliance answer — live asset scanning, NIST CSF ID.AM mapping, GRC rule evaluation, and a GDPR-aware RAG chatbot.
status: Completed
status_class: completed
github: https://github.com/manjou/gcp-cloudscope
date: 2026-07-04
tags:
  - NIST CSF
  - GDPR
  - GCP
  - Python
  - Vertex AI
  - Compliance Automation
---

## What it does

Most cloud environments drift out of sync with the compliance rules meant to govern them. This project answers a specific question — **which cloud assets touch personal data, and are they configured the way our GRC rules require?** — by turning raw GCP resource state into something a compliance reviewer can actually query.

## How it works

1. **Extraction** — `scanner.py` / `gcp_extractor.py` pull live resource data (133 resources scanned) directly from a GCP project via the Cloud Asset Inventory API, read-only, using a service account scoped to exactly `roles/cloudasset.viewer`.
2. **Normalization** — resources get mapped onto a canonical schema aligned to NIST CSF's ID.AM (Asset Management) category, so the same rules can apply regardless of how GCP labels things internally.
3. **Rule evaluation** — `grc_rules.yaml` + `report_generator.py` check each asset against defined compliance rules and flag violations. Policy lives in YAML, not code: updating a rule never means touching the pipeline.
4. **Semantic layer** — the 16 relevant GDPR articles are embedded with Vertex AI and served through a Flask app backed by pgvector/Cloud SQL. Instead of reading a raw report, you ask a question in plain language and get an answer grounded in the actual regulation text, with the source article cited.

## What I actually found and fixed

This wasn't just wiring pieces together — running it against live data surfaced two real bugs:

- A rule matching **ICMP traffic as if it were a management-port violation** (false positive: a portless protocol slipping past a port-specific rule).
- A rules file written against **GCP's raw internal type strings instead of the normalized schema**, which silently broke every rule relying on it — every asset showed as "unclassified."

Both were config-drift bugs between two layers of the same pipeline that were supposed to agree on vocabulary. Neither surfaced as a crash, only as a wrong-looking number — the harder kind of bug to catch. Both required tracing the mismatch back through the pipeline to fix.

## Security finding: the exposure nobody created

Scanning the project also surfaced roughly 40 unused default subnets and two firewall rules open to the entire internet: `default-allow-rdp` (TCP 3389, in a project running zero Windows machines) and `default-allow-ssh` (TCP 22, unrestricted). Remediated via the `gcloud` CLI so the fix is reproducible — RDP rule deleted, SSH restricted to a single source IP — and verified two ways: `firewall-rules describe` and `get-effective-firewalls` at the instance level, to prove nothing was inherited from a policy above the project.

## Built with AI-agent collaboration

This was built working with an AI coding agent throughout. My role was defining the compliance problem, designing the schema and rule logic, and validating the output against real data — including catching the two bugs above. Directing and reviewing an agent through a working technical pipeline, and knowing when its output is wrong, is itself part of the skill this project demonstrates.

## Security practices carried through

- **No key files** — every GCP call authenticates via Application Default Credentials.
- **Secrets fail loud** — Cloud SQL config lives in `.env` only, no hardcoded fallback; missing configuration crashes at startup instead of misbehaving silently.
- **A leaked secret got rotated, not just deleted** — a Cloud SQL password briefly in git history was rotated via `gcloud sql users set-password` before the history was scrubbed with `git filter-repo`.
- **Pre-publish review** — a real project ID, project number, and personal IP found in tracked files were redacted the same way before the repo went public.

## Where it stands

Public repo, deployed and screenshotted. One known unresolved issue: the Cloud SQL connector occasionally hangs on init (not blocking — the chat UI works; initialization sometimes needs a retry).
