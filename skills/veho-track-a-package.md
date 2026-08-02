---
name: Track a Veho package
description: >-
  Look up a Veho package by id or barcode, read its tracking event log, and
  hand the customer a hosted tracking URL.
api: openapi/veho-api-openapi-original.yml
operations: [getPackageById, getPackageTrackingInformation, getPackagesByBarcode]
generated: '2026-07-21'
method: generated
---

# Track a Veho package

Base URL: `https://api.shipveho.com/v2`; `apikey` header on every call.

## Steps

1. If you hold the Veho package `_id`, call `getPackageById`
   (`GET /packages/{_id}`). Pass `includeDeliveryImage=true` to get the
   proof-of-delivery photo once delivered.
2. If you only hold a barcode, resolve it with `getPackagesByBarcode`
   (`GET /packages?barCode=...`).
3. For tracking status, call `getPackageTrackingInformation`
   (`GET /packages/tracking`). Read `lastEvent` and the `eventLog[]`
   (message, city/state on certain events, `humanReadableOperation`).
4. Give the recipient the hosted tracker:
   `https://track.shipveho.com/#/trackingid/{trackingId}` — recipients can
   view status and leave delivery instructions for the driver
   (`components/veho-components.yml`).

## Rules

- Package milestones mirror the 22 webhook event types in
  `asyncapi/veho-webhooks.yml` (created, outForPickup, delivered,
  misdelivered, delayed, returnedToClient, ...). Prefer webhooks over polling
  for state changes.
- 404 means the package id/barcode doesn't exist; 403 means your apikey
  can't access that client's resources (`errors/veho-problem-types.yml`).
