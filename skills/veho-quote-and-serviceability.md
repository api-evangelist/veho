---
name: Quote a Veho shipment and check serviceability
description: >-
  Check whether a destination ZIP is inside Veho's delivery footprint and get
  a simple rate quote before creating an order.
api: openapi/veho-api-openapi-original.yml
operations: [getServiceableZips, createSimpleQuote]
generated: '2026-07-21'
method: generated
---

# Quote a Veho shipment and check serviceability

Base URL: `https://api.shipveho.com/v2` (sandbox:
`https://api.sandbox.shipveho.com/v2`).

## Steps

1. Check the footprint with `getServiceableZips` (`GET /serviceable-zips`) —
   on the sandbox host this endpoint requires no authentication; `serviceType`
   defaults to `delivery` (may also be `pickup`). ZIPs accept `XXXXX`,
   `XXXXX-XXXX`, or 9 digits without the hyphen.
2. Quote with `createSimpleQuote` (`POST /quote/rate`) using the package
   `length`/`width`/`height`/`weight`, `serviceClass`, and `shipDate`; the
   response returns the rate plus `quoteId`, `zone`, `assumedInjectionZip`,
   and `createdAt`.
3. Carry the `quoteId` onto the package when creating the order
   (`skills/veho-create-order-and-label.md`).

## Rules

- Destination ZIPs outside the serviceable list fail order validation with
  the `unserviceable_zip_code` error code (`errors/veho-problem-types.yml`).
- The sandbox serves a subset of production ZIPs — always source sandbox test
  destinations from the sandbox list (`sandbox/veho-sandbox.yml`).
