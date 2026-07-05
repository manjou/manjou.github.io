---
layout: post
title: "What I Learned Trying to Automate GRC"
description: "Agents are great at collecting evidence. They cannot carry responsibility. I built a cloud compliance scanner and a GDPR chatbot to find out where exactly that line runs."
category: GRC
---

<!-- DRAFT — do not publish before mid-July 2026. Add gcp-cloudscope repo link before publishing. -->

There's a seductive idea going around: point an AI agent at your cloud, let it scan everything, and compliance documents itself. No more spreadsheets, no more chasing people for answers, no more stale risk registers. I wanted to know how much of that is real. So I built it — a small lab called **gcp-cloudscope**: a GCP project with real infrastructure (Cloud SQL, Cloud Storage, a VM, a Vertex AI endpoint), a Python scanner that pulls the asset inventory, and a RAG chatbot that answers GDPR questions from the regulation's actual text.

Here's where automation delivered, where it hit a wall, and why that wall is structural — not a missing feature.

## What automated brilliantly

**Asset inventory.** One call to GCP's Cloud Asset Inventory API returned 133 resources — every bucket, database, VM, and network, with regions and labels. Doing this by hand would take days and be outdated before I finished. The *Identify* function of NIST CSF 2.0 starts with "know what you have," and machines are simply better at this than people. This is the strongest case for automation in all of GRC, and it's not close.

**Regulation retrieval.** I split the GDPR into articles, embedded them as vectors, and wired a chatbot to answer questions with the relevant article as context. Ask it what Article 30 requires and you get a grounded answer with the source next to it. As a drafting and lookup assistant, it's genuinely useful.

## Where it hit the wall

**The scanner only sees what's deployed.** Cloud Asset Inventory captures native infrastructure with active deployments. A Vertex AI *endpoint* shows up. Code that calls the Gemini API does not — there is no resource to find. So the most compliance-relevant question of 2026, *"where exactly does AI touch our data?"*, cannot be answered by scanning alone. Someone has to ask the engineers. The inventory is evidence, not understanding.

**The chatbot can answer about Article 30 — it cannot be your Article 30.** A Record of Processing Activities needs to state why you process data, on what legal basis, for how long. Those aren't facts lying around in the infrastructure waiting to be collected. They're decisions someone in the business has made — or hasn't made yet, which is exactly what a GRC process is supposed to surface.

**Issues are not a framework.** I tracked my work in a regular issue tracker, and I felt the gap immediately: there's no native way to link an issue to the requirement it satisfies, the control that implements it, the evidence that proves it, and the person who owns it. That chain — requirement → control → evidence → owner — is the entire point of a GRC program, and it's why dedicated GRC tooling exists. A hundred auto-generated issues don't approximate it; they bury it.

**Nobody can delegate risk acceptance to a script.** GDPR Article 5(2) names it directly: *accountability*. NIST CSF 2.0 added a whole Govern function, and its first questions are about roles and ownership. Somewhere, a human looks at a finding and decides: we fix this, we accept this, we escalate this — and signs. An agent that "decides" this has decided nothing; it has just hidden who's responsible. The EU AI Act's human-oversight requirements run on the same logic: the more consequential the automated output, the more deliberately a human must stay in the loop.

## The automation created its own finding

One more lesson, at my own expense. I built this project fast, with heavy AI assistance — and at that speed, the Cloud SQL password ended up hardcoded in three files and baked into the git history. The thing that caught it was an AI-assisted pre-publish review. The things that *fixed* it were human decisions: rotate the credential, rewrite the history, change how secrets are handled, write the incident down.

That's the whole thesis in one incident. Automation created the risk, automation helped detect it — and a person had to judge it, own it, and close it.

## How I actually work with AI (a disclosure)

This project was not typed character by character by me, and I won't pretend otherwise. I work with AI coding agents the way I'd work with a very fast junior colleague: they propose, I verify. Every line that touches credentials, IAM, or data gets read and questioned before it runs. The current generation of models is impressively good at spotting security issues — mine flagged the credential leak within minutes of being asked to review the repo for publication. But "the model said so" is never the end of the chain. I am. That's not a limitation of the workflow; it *is* the workflow. Human oversight isn't what you add when AI fails — it's what makes AI usable in the first place.

## Where the line runs

| | Automate it | A human owns it |
|---|---|---|
| Asset inventory | ✅ continuously | sets the scope |
| Evidence collection | ✅ pipelines, exports | judges sufficiency |
| Regulation lookup & drafting | ✅ RAG assistants | reviews and signs |
| Business context (purpose, legal basis) | ❌ | states it |
| Risk acceptance | ❌ | decides and signs |
| Requirement ↔ control ↔ evidence linkage | tooling helps | designs and maintains it |

So: should you automate GRC? Automate the **data plane** — inventory, evidence, retrieval — as aggressively as you can; it's where the toil lives and machines are honestly better. Govern the **decision plane** — scope, context, acceptance, sign-off — with named humans, because that's not a workflow inefficiency, it's the legal and ethical substance of the discipline.

An agent can fetch every fact about your cloud. It cannot answer for it. That difference is what GRC is.
