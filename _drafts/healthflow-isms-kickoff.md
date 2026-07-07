---
layout: post
title: "Starting an ISO 27001 ISMS from Zero: The HealthFlow Simulation"
description: "I'm building a full ISO 27001:2022 ISMS for a fictional 40-person Berlin health-tech SaaS — scope, risk assessment, statement of applicability, policies. Here's the setup and what I want to learn."
category: GRC
---

<!-- DRAFT — publish once scope definition and the first risk assessment pass are done, then promote to a full project page. -->

Most ISO 27001 material explains what an ISMS *is*. Far less shows what it feels like to build one from an empty folder. So I'm running a simulation: **HealthFlow**, a fictional 40-person Berlin SaaS company handling health appointment data — special-category data under GDPR Art. 9, which makes every decision sharper.

## The setup

HealthFlow doesn't exist, but its constraints are realistic: a small engineering team, one product, cloud infrastructure, customers who are medical practices, and data that regulators care deeply about. No dedicated security team — which is exactly the situation where an ISMS either earns its keep or collapses into shelfware.

## What I'm building

Working through the ISO 27001:2022 lifecycle in order:

1. **Scope definition** — what's inside the ISMS boundary and, more importantly, what's out and why.
2. **Risk assessment** — asset-based, with health-data processing as the driving scenario.
3. **Statement of Applicability** — every Annex A control: applicable or not, justified either way.
4. **Policy framework** — the minimum set of policies a 40-person company would actually read.

## Why simulate it

At Panos.ai I built an EU AI Act and GDPR compliance baseline for a real AI startup; the recurring lesson was that frameworks only get useful when they meet a specific company's constraints. HealthFlow is my way of applying that lesson to ISO 27001 end to end — including the boring parts nobody blogs about, like justifying excluded controls.

Progress notes will land here as each phase is done.
