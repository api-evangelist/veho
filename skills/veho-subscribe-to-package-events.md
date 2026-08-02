---
name: Subscribe to Veho package events via webhooks
description: >-
  Register a webhook endpoint for Veho package milestone events, audit
  deliveries, and replay missed events.
api: openapi/veho-api-openapi-original.yml
operations: [addWebhookConfiguration, getWebhookConfigurations, getWebhookEvents, replayWebhookEvents]
generated: '2026-07-21'
method: generated
---

# Subscribe to Veho package events via webhooks

Base URL: `https://api.shipveho.com/v2`; `apikey` header on every call.
Full event surface: `asyncapi/veho-webhooks.yml`.

## Steps

1. Register one configuration per event type with `addWebhookConfiguration`
   (`POST /webhooks/webhookConfigurations`): `endpoint` (HTTPS URL),
   `eventType` (e.g. `package.delivered`), `serviceType` (`delivery` or
   `pickup`), and optional `customHeaders` (e.g. an auth key your receiver
   expects).
2. Verify what's registered with `getWebhookConfigurations`
   (`GET /webhooks/webhookConfigurations`); update or remove with
   `updateWebhookConfigurationById` / `deleteWebhookConfigurationById`.
3. Audit deliveries with `getWebhookEvents` (`GET /webhooks/webhookEvents`) —
   each event records `eventType`, `occurredAt`, `payload`, `result`, and the
   `packageId`/`orderId`/`trackingId` it concerns.
4. Recover missed events with `replayWebhookEvents` (`POST /webhooks/replay`).

## Rules

- There are 22 `package.*` event types (see `asyncapi/veho-webhooks.yml`);
  register only the ones you consume.
- Webhook payload bodies align with the GET package responses
  (changelog 2025.10.06) — reuse one parser for both.
- Endpoint receivers should be idempotent: replay plus at-least-once delivery
  means duplicates are possible.
