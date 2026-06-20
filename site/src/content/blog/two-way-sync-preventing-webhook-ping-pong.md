---
title: "2-way synchronisation: preventing webhook ping-pong between Current RMS and Pipedrive"
slug: two-way-sync-preventing-webhook-ping-pong
publishedAt: 2026-06-20
brief: "When you sync data both ways between two systems, each update can trigger a webhook that triggers another update. Here's how to break the loop."
tags: ["current-rms", "pipedrive", "webhooks", "integrations", "development", "synchronisation"]
draft: true
---

## Problem

You build a 2-way sync between two systems. System A sends a webhook when something changes. Your integration updates System B. System B sends a webhook. Your integration updates System A. System A sends a webhook.

Infinite loop.

This is the webhook ping-pong problem. It's easy to trigger accidentally and not immediately obvious what's causing it — the logs show the integration doing the right thing, but doing it indefinitely.

## Approaches to breaking the loop

### 1. Compare before writing

Before updating System B, check whether the value you're about to write differs from what's already there. If they match, skip the write. No write, no webhook, no loop.

This is the simplest approach and works well when reads are cheap relative to writes. It doesn't require any additional state.

The limitation: it only works if the systems represent the same values in a directly comparable way. If Current RMS uses a numeric contact ID and Pipedrive uses a string, or if field formats differ, you'll need to normalise before comparing.

### 2. Track recently synced records

Maintain a short-lived record of records you've just written. When a webhook arrives, check whether the record is in that set. If it is, skip processing — it's the echo of your own previous update.

This is the most generally applicable approach and works regardless of how the two systems represent data.

### 3. Source-of-truth architecture

Designate one system as authoritative for each field. Current RMS owns equipment data; Pipedrive owns contact data. When a webhook arrives for a field owned by the other system, ignore it entirely.

Writes only flow one direction per field, so there's no loop to create. This requires a clear field-ownership map and discipline in maintaining it, but it's the most architecturally sound approach for stable integrations.

### 4. Idempotency timestamps

Compare the `updated_at` timestamp on the incoming webhook against the time your integration last wrote to that record. If the incoming change is older than or equal to your last write, skip it — it's the echo.

This requires storing the write timestamp per record and per field, but avoids false positives when legitimate rapid updates come in from both sides.

## Code example: tracking recently synced records

The in-memory approach using a `Map` as a keyed store with manual TTL:

```javascript
// Simple in-memory sync tracker
// Key format: `${system}:${recordType}:${recordId}`
// Value: timestamp of when the write occurred

const recentlySynced = new Map();
const SYNC_TTL_MS = 10_000; // 10 seconds

function markAsSynced(system, recordType, recordId) {
  const key = `${system}:${recordType}:${recordId}`;
  recentlySynced.set(key, Date.now());

  // Clean up after TTL
  setTimeout(() => {
    recentlySynced.delete(key);
  }, SYNC_TTL_MS);
}

function wasRecentlySynced(system, recordType, recordId) {
  const key = `${system}:${recordType}:${recordId}`;
  const timestamp = recentlySynced.get(key);
  if (!timestamp) return false;
  return Date.now() - timestamp < SYNC_TTL_MS;
}
```

Use it around writes and webhook handlers:

```javascript
// When writing to Pipedrive as a result of a Current RMS webhook
async function syncToPipedrive(currentRmsRecord) {
  // Mark before writing so the echo webhook is caught
  markAsSynced('pipedrive', 'contact', currentRmsRecord.id);

  await pipedriveClient.updateContact(/* ... */);
}

// Pipedrive webhook handler
function handlePipedriveWebhook(event) {
  const { id } = event.current;

  if (wasRecentlySynced('pipedrive', 'contact', id)) {
    // This is the echo of our own write — skip it
    return;
  }

  // Legitimate change from Pipedrive — process it
  syncToCurrentRms(event.current);
}
```

The TTL needs to be long enough to cover the round-trip time between your write and the echo webhook arriving. 5–10 seconds is usually sufficient. Increase it if your webhook delivery is slow.

## Production note

In production, use Redis or a database for the tracking store rather than an in-memory `Map`. A process restart will clear in-memory state and re-open the loop until the next write sets the key again. Redis with a TTL (via `SET key value EX seconds`) is a direct replacement and survives restarts and multi-instance deployments.

```javascript
// Redis equivalent
await redis.set(key, Date.now(), 'EX', 10); // expires after 10 seconds

async function wasRecentlySyncedRedis(system, recordType, recordId) {
  const key = `sync:${system}:${recordType}:${recordId}`;
  return (await redis.exists(key)) === 1;
}
```
