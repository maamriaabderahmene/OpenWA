# Audit — Audit Logs

The Audit API provides a searchable log of all actions performed through OpenWA. Every API call, session event, and configuration change is recorded for compliance and troubleshooting.

---

## List Audit Logs

```http
GET /api/audit
```

Returns audit log entries with optional filters.

### Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| `action` | string | Filter by action type (`session.create`, `message.send`, `webhook.create`, etc.) |
| `severity` | string | Filter by severity (`info`, `warn`, `error`) |
| `sessionId` | string | Filter by session |
| `apiKeyId` | string | Filter by API key that performed the action |
| `limit` | number | Max results |
| `offset` | number | Pagination offset |

### CRM Use Case
Display an audit trail in the CRM security settings. Track which agent performed which action on which phone number. Monitor for unusual activity.

### Response `200 OK`

```json
[
  {
    "id": "audit-uuid",
    "action": "message.send",
    "severity": "info",
    "sessionId": "session-uuid",
    "apiKeyId": "key-uuid",
    "apiKeyName": "CRM Integration Key",
    "details": {
      "chatId": "1234567890@c.us",
      "messageType": "text"
    },
    "ip": "203.0.113.1",
    "timestamp": "2025-01-01T12:00:00.000Z"
  }
]
```

---

## Audit Event Types

| Action | Description |
|--------|-------------|
| `session.create` | Session created |
| `session.start` | Session started |
| `session.stop` | Session stopped |
| `session.delete` | Session deleted |
| `message.send` | Message sent |
| `message.delete` | Message deleted |
| `webhook.create` | Webhook created |
| `webhook.update` | Webhook updated |
| `webhook.delete` | Webhook deleted |
| `apikey.create` | API key created |
| `apikey.update` | API key updated |
| `apikey.delete` | API key deleted |
| `apikey.revoke` | API key revoked |
| `config.update` | Configuration changed |
| `auth.failure` | Authentication failure |
| `auth.success` | Successful authentication |
