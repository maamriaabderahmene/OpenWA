# Webhooks — Real-Time Event Notifications

Webhooks deliver real-time WhatsApp events to your CRM. When a message arrives, status changes, or any event occurs, OpenWA sends an HTTP POST to the configured webhook URL.

---

## Webhook Flow in a CRM

```
WhatsApp → OpenWA → Webhook POST → Your CRM Webhook Receiver
```

The CRM:
1. Creates a webhook URL endpoint that receives POST requests from OpenWA
2. Configures a webhook per session (one per phone number)
3. Processes incoming events to update CRM records in real time

---

## Create a Webhook

```http
POST /api/sessions/{sessionId}/webhooks
```

Creates a new webhook for a session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "url": "https://your-crm.com/api/whatsapp-webhook",
  "events": ["message", "message.ack", "presence.update"],
  "secret": "your-webhook-secret",
  "headers": {
    "Authorization": "Bearer your-crm-token"
  },
  "retryCount": 3
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `url` | string | yes | HTTPS webhook endpoint |
| `events` | string[] | no | Event types to subscribe to (all events if omitted) |
| `secret` | string | no | Secret for HMAC signature verification |
| `headers` | object | no | Custom headers sent with each webhook |
| `retryCount` | number | no | Number of retries on failure (default 3) |

### Available Event Types

| Event | Description |
|-------|-------------|
| `message` | New message received |
| `message.ack` | Message delivery/read acknowledgement |
| `message.reaction` | Reaction added/removed |
| `presence.update` | Contact presence changed (online/offline/typing) |
| `group.join` | Someone joined a group |
| `group.leave` | Someone left a group |
| `group.update` | Group settings changed |
| `contact.update` | Contact details updated |

### CRM Use Case
When setting up a new WhatsApp number in the CRM, automatically create a webhook pointing to the CRM's webhook receiver. This ensures all inbound messages are captured.

### Response `201 Created`

```json
{
  "id": "webhook-uuid",
  "sessionId": "session-uuid",
  "url": "https://your-crm.com/api/whatsapp-webhook",
  "events": ["message", "message.ack"],
  "active": true,
  "retryCount": 3,
  "createdAt": "2025-01-01T00:00:00.000Z",
  "updatedAt": "2025-01-01T00:00:00.000Z"
}
```

---

## List Webhooks

```http
GET /api/sessions/{sessionId}/webhooks
```

Lists all webhooks configured for a session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Display configured webhooks in the CRM settings page. Allow admin to verify webhook is active.

### Response `200 OK`

```json
[
  {
    "id": "webhook-uuid",
    "url": "https://your-crm.com/api/whatsapp-webhook",
    "events": ["message"],
    "active": true,
    "retryCount": 3,
    "createdAt": "2025-01-01T00:00:00.000Z"
  }
]
```

---

## Get Webhook by ID

```http
GET /api/sessions/{sessionId}/webhooks/{id}
```

Gets a specific webhook's details.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Webhook UUID |

### Response `200 OK`

Full webhook object as above.

---

## Update a Webhook

```http
PUT /api/sessions/{sessionId}/webhooks/{id}
```

Updates an existing webhook's configuration.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Webhook UUID |

### Request Body

```json
{
  "url": "https://your-crm.com/api/whatsapp-webhook-v2",
  "events": ["message", "message.ack", "group.join"],
  "active": true
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `url` | string | no | New webhook URL |
| `events` | string[] | no | Updated event filter |
| `secret` | string | no | New secret |
| `headers` | object | no | Updated custom headers |
| `active` | boolean | no | Enable/disable webhook |
| `retryCount` | number | no | Updated retry count |

### CRM Use Case
Update webhook URL when the CRM endpoint changes without needing to delete and recreate.

### Response `200 OK`

Updated webhook object.

---

## Delete a Webhook

```http
DELETE /api/sessions/{sessionId}/webhooks/{id}
```

Deletes a webhook.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Webhook UUID |

### CRM Use Case
Remove webhook configuration when deactivating a phone number in the CRM.

### Response `204 No Content`

---

## Test a Webhook

```http
POST /api/sessions/{sessionId}/webhooks/{id}/test
```

Sends a test payload to the webhook URL to verify connectivity.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Webhook UUID |

### CRM Use Case
"Test Webhook" button in CRM settings to verify the endpoint is reachable and processing events.

### Response `200 OK`

```json
{
  "message": "Test payload sent",
  "statusCode": 200
}
```

---

## List Delivery Failures

```http
GET /api/webhooks/delivery-failures
```

Lists webhook deliveries that failed after all retries were exhausted.

### Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| `limit` | number | Max results |
| `offset` | number | Pagination offset |

### CRM Use Case
Monitor webhook health. If delivery failures are detected, alert the CRM admin to check the webhook endpoint.

### Response `200 OK`

```json
[
  {
    "id": "failure-uuid",
    "webhookId": "webhook-uuid",
    "event": "message",
    "payload": { ... },
    "error": "Connection timeout",
    "attempts": 3,
    "lastAttemptAt": "2025-01-01T00:00:00.000Z"
  }
]
```

---

## List Webhooks Visible to Key

```http
GET /api/webhooks
```

Lists webhooks visible to the calling API key (scoped to its allowed sessions).

### CRM Use Case
A multi-tenant CRM can scope API keys to specific sessions. This endpoint returns only the webhooks the key has access to.

### Response `200 OK`

```json
[
  {
    "id": "webhook-uuid",
    "sessionId": "session-uuid",
    "url": "https://example.com/webhook",
    "events": ["message"],
    "active": true
  }
]
```

---

## Webhook Payload Format

When an event occurs, OpenWA sends a POST to your webhook URL with this payload shape:

```json
{
  "event": "message",
  "sessionId": "session-uuid",
  "data": {
    "messageId": "true_whatsapp_message_id",
    "chatId": "1234567890@c.us",
    "fromMe": false,
    "type": "text",
    "text": "Hello, I need help with my order",
    "timestamp": "2025-01-01T00:00:00.000Z",
    "sender": "1234567890@c.us"
  }
}
```

### Signature Verification

If a `secret` was configured, the webhook includes an `X-Webhook-Signature` header. Compute the HMAC-SHA256 of the raw request body using the secret and compare it to this header to verify authenticity.

---

## CRM Integration Architecture

```
┌──────────────┐     Webhook POST     ┌──────────────────────┐
│   OpenWA     │ ──────────────────▶  │  CRM Webhook Handler │
│              │                      │                      │
│  Session 1   │                      │  1. Verify signature │
│  Session 2   │                      │  2. Parse event      │
│  Session 3   │                      │  3. Update contact   │
└──────────────┘                      │  4. Store message    │
                                      │  5. Trigger workflow │
                                      └──────────────────────┘
```
