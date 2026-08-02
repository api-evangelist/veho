---
name: Create a Veho order and download its shipping label
description: >-
  Create a delivery order with Veho, confirm its packages, and download the
  shipping label in pdf, png, or zpl format.
api: openapi/veho-api-openapi-original.yml
operations: [createSimpleQuote, createOrder, getOrderById, getPackageLabel]
generated: '2026-07-21'
method: generated
---

# Create a Veho order and download its shipping label

Base URL: `https://api.shipveho.com/v2` (sandbox: `https://api.sandbox.shipveho.com/v2`).
Authenticate every call with the `apikey: <your key>` header (see
`authentication/veho-authentication.yml`). Use the sandbox key against the
sandbox host first; sandbox destination ZIPs must come from the sandbox
serviceable ZIP list (`sandbox/veho-sandbox.yml`).

## Steps

1. (Optional) Quote the shipment with `createSimpleQuote` (`POST /quote/rate`)
   to get a rate and `quoteId` for a provisional order.
2. Create the order with `createOrder` (`POST /orders`). Minimum payload:
   `recipient`, `destination` (street, city, state, zipCode), and
   `packageCount: 1` — Veho infers package details from your preconfigured
   defaults. Declare per-package `length`/`width`/`height`/`weight` when known.
   Constraints: dimensions <= 48" on any axis, weight <= 50 lbs, volume
   <= 9849.6 cubic inches; barcodes (optional) must be unique within 30 days.
3. Read the created order with `getOrderById` (`GET /orders/{_id}`); its
   `packages[]` carry `_id`, `trackingId`, and label links.
4. Download each label with `getPackageLabel`
   (`GET /labels/{packageId}.{format}`), format one of `pdf`, `png`, `zpl`.

## Rules

- 400/422 responses return a `message` plus `errors[]` array — branch on each
  error object's `code` only (messages may change); see
  `errors/veho-problem-types.yml`.
- There is no idempotency-key contract (`conventions/veho-conventions.yml`):
  do not blindly retry a timed-out `createOrder`; reconcile first via
  `findOrders` with your `externalId`.
- Cancel with `cancelOrder` (`PUT /orders/{_id}/events/cancelled`) or
  `cancelOrderById` for alternative IDs.
