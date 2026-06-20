---
title: "2-way synchronisation: preventing webhook ping-pong between Current RMS and Pipedrive"
slug: two-way-sync-preventing-webhook-ping-pong
publishedAt: 2026-06-20
brief: "When you sync data both ways between two systems, each update can trigger a webhook that triggers another update. Both Current RMS and Pipedrive give you a clean way out."
tags: ["current-rms", "pipedrive", "webhooks", "integrations", "development", "synchronisation"]
draft: true
---

## Problem

You build a 2-way sync between two systems. System A sends a webhook when something changes. Your integration updates System B. System B sends a webhook. Your integration updates System A. System A sends a webhook.

Infinite loop.

This is the webhook ping-pong problem. It's easy to trigger accidentally and not immediately obvious what's causing it — the logs show the integration doing the right thing, but doing it indefinitely.

## The straightforward solution

Both Current RMS and Pipedrive include metadata on their webhook payloads that identifies where the change originated. This is the cleanest way to break the loop: the platform tells you whether the change came from a user or from an API call. If it came from the API, it's the echo of your own write — ignore it.

No state to track. No TTL to tune. No race conditions.

## Current RMS: member_id

Every Current RMS webhook payload includes `action.member_id` — the ID of the user whose action triggered it. When your integration writes to Current RMS via the API, it authenticates as a dedicated API user. That user has a known member ID.

The filter is simple: if the `member_id` matches your API user, skip it.

```typescript
const RMS_API_USER_ID = process.env.RMS_API_USER_ID;

function handleCurrentWebhook(body: CurrentWebhookBody) {
  const { member_id } = body.action;

  if (member_id === parseInt(RMS_API_USER_ID)) {
    // Change was made by the API user — our own echo. Ignore.
    return;
  }

  // Legitimate change from a human user — process it.
  syncToPipedrive(body);
}
```

This only works if the integration authenticates as a dedicated API user — one that no human logs in as. If the integration shares credentials with a real user, changes made by that user will be filtered out alongside the echoes.

## Pipedrive: change_source

Pipedrive includes `meta.change_source` on every webhook event. When a change is made via the API, this is `"api"`. When a change is made through the Pipedrive UI, it's something else (`"app"`, or the name of a third-party integration).

```typescript
function handlePipedriveWebhook(event: PipedriveEvent) {
  const { change_source: changeSource, is_bulk_update: isBulkUpdate } = event.body.meta;

  if (changeSource === "api" && isBulkUpdate === false) {
    // Change came through the API — our own echo. Ignore.
    return;
  }

  // Legitimate change — process it.
  syncToCurrentRms(event.body);
}
```

The `isBulkUpdate` carve-out is worth noting. Bulk API operations — imports, mass updates — also arrive with `change_source: "api"`, but they may represent legitimate data changes you want to act on rather than echoes of your own individual writes. Whether you apply the same filter to bulk updates depends on your integration's scope.

## Generic fallback: TTL-based tracking

Not all platforms provide origin metadata on webhooks. Where they don't, a common approach is to maintain a short-lived record of writes your integration has made, and skip incoming webhooks for records in that set.

```typescript
const recentlySynced = new Map<string, number>();
const TTL_MS = 10_000;

function markSynced(system: string, id: string) {
  const key = `${system}:${id}`;
  recentlySynced.set(key, Date.now());
  setTimeout(() => recentlySynced.delete(key), TTL_MS);
}

function wasSynced(system: string, id: string): boolean {
  const ts = recentlySynced.get(`${system}:${id}`);
  return ts !== undefined && Date.now() - ts < TTL_MS;
}
```

Mark before writing; check on incoming webhook. The TTL needs to exceed the round-trip between your write and the echo webhook arriving — 5–10 seconds is usually enough.

In production, use Redis with a native TTL rather than an in-memory `Map`. A process restart clears in-memory state and re-opens the loop.

If a platform exposes origin metadata, use that instead. It's simpler and not vulnerable to timing edge cases.
