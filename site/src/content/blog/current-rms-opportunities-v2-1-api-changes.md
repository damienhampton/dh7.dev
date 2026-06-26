---
title: "Current RMS Opportunities API v2.1 — changes and restrictions"
slug: current-rms-opportunities-v2-1-api-changes
publishedAt: 2026-06-26
brief: "What changed in the Current RMS Opportunities v2.1 API, what's been restricted, and what that means if you're building integrations against it."
tags: ["current-rms", "api", "integrations", "development"]
draft: false
---

Current RMS released v2.1 of the Opportunities API on 15 June 2026. The URL and query parameters are unchanged from V1, but the response shape is different and some associations are no longer available on the list endpoint.

## What changed

### Single opportunity: `GET /opportunities/{id}`

V1 returned the full record including all associations by default. V2.1 returns only opportunity fields, charge totals, and meta unless you explicitly request more.

To include associations, pass `include[]` as a query parameter — one value per association:

```
GET /opportunities/12345?include[]=opportunity_items&include[]=participants
```

Omitted associations are not returned. **Requests that previously relied on associations being present by default 
will need updating.**

### Opportunity list: `GET /opportunities`

V2.1 list responses include opportunity fields and charge totals for each record, plus pagination in meta. The `include[]` parameter works the same way for other associations.

The difference from the single endpoint: `opportunity_items`, `item_assets`, `return_item_assets`, and `participants` are **not available on the list endpoint**. Passing these as `include[]` values on the list will not work.

**If you need line items, assets, or participants, you have to fetch the individual opportunity:** `GET /opportunities/
{id}`.

## What this means for integrations

If your integration fetches a list of opportunities and then reads line items or assets from the embedded response, that will break on V2.1. The pattern changes to:

1. Fetch the list to get opportunity IDs and top-level fields
2. For each opportunity where you need line items or assets, make a separate request to `/opportunities/{id}` with the relevant `include[]` values

That's more requests. If you're processing large volumes of opportunities, it's worth checking whether you actually need line items on every record, or only on a subset — recently changed ones, for example.

If you're only reading top-level fields and charge totals, the list endpoint in V2.1 is fine without changes beyond removing any `include[]` values that are no longer supported there.
