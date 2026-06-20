---
title: "Why your Current RMS data is never quite right in Xero"
slug: why-your-current-rms-data-is-never-quite-right-in-xero
publishedAt: 2026-06-20
brief: "Current RMS and Xero both work fine on their own. The gap between them is where the problem lives."
tags: ["current-rms", "xero", "integrations", "business"]
category: business
draft: true
---

Current RMS handles the rental workflow well. Xero handles accounting well. Neither of these is in dispute.

The problem is what happens between them.

## How the gap usually works

Invoices are raised in Current RMS. They get exported — as a CSV, a spreadsheet, or through a third-party connector — and brought into Xero. Or someone enters them manually.

It almost works.

Tax codes don't align cleanly between the two systems. Contact names come through in a different format, or under a slightly different company name. Timing is off — invoices raised in Current RMS on a Friday might not appear in Xero until the following week, depending on when the export runs.

The result is that Xero is never quite a clean mirror of what Current RMS shows. The data is close, but not exact.

## Someone spends time closing the gap

In most businesses running this setup, there's a weekly or monthly reconciliation task. Someone compares what Current RMS shows against what Xero shows and resolves the differences. Tax code mismatches, contact duplicates, missing lines.

This isn't because either system is broken. It's because the gap between them creates work that wouldn't exist if they were connected properly.

There isn't a single source of truth. Each system maintains its own version of the same transactions.

## What a direct integration looks like

When Current RMS and Xero are connected directly — via API, with a sync layer that maps fields correctly — invoices raised in Current RMS appear in Xero immediately, with the correct tax codes, the correct contact, and the correct amounts.

Contact records stay in sync. Payments recorded in Xero can be reflected back into Current RMS.

The reconciliation task becomes a check rather than a correction exercise. Most months, there's nothing to fix.
