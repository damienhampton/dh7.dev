---
title: "From Pipedrive deal won to Current RMS opportunity: automating the sales-to-ops handoff"
slug: pipedrive-to-current-rms-automating-the-handoff
publishedAt: 2026-06-20
brief: "A specific, common problem for rental businesses that use both Pipedrive and Current RMS."
tags: ["pipedrive", "current-rms", "integrations", "business"]
category: business
draft: true
---

Sales manages the pipeline in Pipedrive. Operations manages the rental workflow in Current RMS. The two systems do different jobs and neither should replace the other.

But there's a gap between them.

## The gap

When a deal is won in Pipedrive, someone has to create the corresponding opportunity in Current RMS. That person is usually not the sales rep — it falls to an ops coordinator, an admin, or whoever is next in line to pick it up.

In practice, this is what happens: the deal is marked Won, someone sees it and makes a note, the opportunity gets created later that day or the following morning. The customer's details are typed in again. Occasionally something is wrong — wrong date, wrong contact name, field left blank. Occasionally it's missed entirely.

The delay might be a few hours. Often it's overnight. In a busy period, it can be longer.

## What the automation does

When a deal moves to Won in Pipedrive, a webhook fires to the integration. The integration reads the deal data — customer details, hire dates, equipment requirements, any custom fields that map to Current RMS fields — and creates the opportunity in Current RMS immediately.

The opportunity is in Current RMS before the sales rep has finished the call. No re-entry. No delay. No missed handoffs.

If the deal is updated — dates change, equipment list is revised — the integration can propagate those changes automatically.

## What stays separate

Pipedrive continues to manage the pipeline, the sales activity, the deal history. Current RMS continues to manage the rental workflow, availability, logistics, invoicing.

The integration doesn't merge the two systems. It removes the manual step between them.

The two systems still do separate jobs. They just agree on when a deal has become a booking, and that agreement happens without anyone having to tell them.
