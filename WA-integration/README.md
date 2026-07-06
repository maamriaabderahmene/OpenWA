# OpenWA — CRM Integration Guide

This guide documents every OpenWA REST API endpoint framed for CRM integration. Use these APIs to embed WhatsApp messaging, session management, contact sync, group operations, and automation directly into your CRM.

## Base URL

```
http://<your-host>:2785/api
```

All requests require an `X-API-Key` header with a valid API key.

---

## Modules

| Module | File | Description |
|--------|------|-------------|
| [Sessions](./sessions.md) | `sessions.md` | Create, manage, and monitor WhatsApp sessions |
| [Messages](./messages.md) | `messages.md` | Send and manage text, media, templates, bulk messages |
| [Webhooks](./webhooks.md) | `webhooks.md` | Receive real-time event notifications from WhatsApp |
| [Contacts](./contacts.md) | `contacts.md` | Sync and manage WhatsApp contacts |
| [Groups](./groups.md) | `groups.md` | Create and manage WhatsApp groups |
| [Labels](./labels.md) | `labels.md` | Label management for WhatsApp Business |
| [Channels](./channels.md) | `channels.md` | Subscribe and read channel/newsletter content |
| [Templates](./templates.md) | `templates.md` | Reusable message templates |
| [Status](./status.md) | `status.md` | Post and read WhatsApp Status updates |
| [Catalog](./catalog.md) | `catalog.md` | Business catalog and product messaging |
| [Auth & API Keys](./auth.md) | `auth.md` | API key management and validation |
| [Audit](./audit.md) | `audit.md` | Audit log querying |
| [Health](./health.md) | `health.md` | Health check endpoints |
| [Settings](./settings.md) | `settings.md` | Application settings (read-only at runtime) |
| [Infrastructure](./infrastructure.md) | `infrastructure.md` | Server config, data export/import, storage |
| [Statistics](./statistics.md) | `statistics.md` | Usage and message statistics |
| [Plugins](./plugins.md) | `plugins.md` | Plugin system for extending OpenWA |

---

## Common Patterns

### Session ID
Every session is identified by a unique `id` (UUID). This is the primary key for all per-session operations.

### Error Responses
```json
{
  "statusCode": 400,
  "message": "Invalid session ID",
  "error": "Bad Request"
}
```

### Pagination
List endpoints support `limit` and `offset` query parameters for pagination.

### Authentication
All endpoints except `/api/health` require `X-API-Key` header authentication.
