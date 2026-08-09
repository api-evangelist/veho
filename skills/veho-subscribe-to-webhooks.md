---
name: Subscribe to Veho package-milestone webhooks
description: >-
  Configure, verify, monitor, and replay Veho webhook deliveries for the 22
  package.* event types.
api: openapi/veho-tech-api-openapi-original.yml
operations: [addWebhookConfiguration, getWebhookConfigurations, updateWebhookConfigurationById, getWebhookEvents, getWebhookEventsById, replayWebhookEvents, deleteWebhookConfigurationById]
generated: '2026-07-21'
method: generated
---

# Subscribe to Veho package-milestone webhooks

Authenticate with the `apikey` header. One configuration binds **one**
`eventType` + `serviceType` pair to an endpoint — create one configuration per
event you care about.

1. **Create a configuration** — `addWebhookConfiguration`
   (`POST /webhooks/webhookConfigurations`) with `endpoint` (HTTPS URL),
   `eventType` (e.g. `package.delivered`, `package.outForPickup` — 22
   `package.*` values in the EventType schema), `serviceType`
   (`delivery` or `pickup`), and optional `customHeaders` — Veho sends these
   headers on every delivery, so set a secret header here to authenticate
   deliveries (there is no signature scheme).
2. **List / inspect** — `getWebhookConfigurations`
   (`GET /webhooks/webhookConfigurations`) and adjust with
   `updateWebhookConfigurationById` (`PATCH`) or remove with
   `deleteWebhookConfigurationById` (`DELETE`).
3. **Monitor deliveries** — `getWebhookEvents`
   (`GET /webhooks/webhookEvents`) filtered by `occurredAtMin`/`occurredAtMax`,
   `orderId`, `packageId`, `endpoint`, `trackingId`, `barcode`, or `status`;
   drill in with `getWebhookEventsById`.
4. **Recover from outages** — `replayWebhookEvents`
   (`POST /webhooks/replay`) to re-send missed events after your endpoint was
   down.

Payloads follow the WebhookEvent schema (packageId, orderId, eventType,
occurredAt, trackingId, payload, result). Test end-to-end in the sandbox
(`api.sandbox.shipveho.com`) before configuring production endpoints.
