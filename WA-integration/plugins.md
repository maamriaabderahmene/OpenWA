# Plugins — Extending OpenWA

The Plugins API allows a CRM to install, configure, and manage OpenWA plugins. Plugins extend OpenWA's functionality with custom features like AI assistants, custom message processors, CRM-specific integrations, and more.

---

## Plugin Lifecycle

```
Install → Configure → Enable → [Active] → Disable → Uninstall
```

---

## List All Plugins

```http
GET /api/plugins
```

Returns all installed plugins.

### CRM Use Case
Display installed plugins and their status in the CRM admin panel.

### Response `200 OK`

```json
[
  {
    "id": "plugin-id",
    "name": "AI Reply Generator",
    "version": "1.0.0",
    "type": "service",
    "status": "enabled",
    "builtIn": false,
    "provides": ["ai.reply.generate"],
    "sessionScoped": false,
    "activeSessions": [],
    "description": "Generates AI-powered reply suggestions for agents",
    "author": "Your Company",
    "enabledAt": "2025-01-01T00:00:00.000Z"
  }
]
```

---

## Install Plugin from Upload

```http
POST /api/plugins/install
```

Installs a plugin from an uploaded `.zip` package. Uses `multipart/form-data`.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | file | yes | `.zip` plugin package |

### CRM Use Case
Install a custom-built plugin for your CRM integration by uploading the zip package from the CRM admin UI.

### Response `201 Created`

```json
{
  "id": "plugin-id",
  "name": "CRM Sync Plugin",
  "version": "1.0.0",
  "status": "disabled"
}
```

---

## Install Plugin from URL

```post
POST /api/plugins/install-url
```

Installs a plugin by downloading its `.zip` from a URL (SSRF-guarded).

### Request Body

```json
{
  "url": "https://plugins.example.com/crm-sync-plugin.zip"
}
```

### Response `201 Created`

```json
{
  "id": "plugin-id",
  "name": "CRM Sync Plugin",
  "status": "disabled"
}
```

---

## List Plugin Catalog

```http
GET /api/plugins/catalog
```

Lists the remote plugin catalog, annotated with install state.

### Response `200 OK`

```json
[
  {
    "id": "plugin-id",
    "name": "AI Reply Generator",
    "description": "AI-powered reply suggestions",
    "version": "1.0.0",
    "author": "OpenWA",
    "installed": true,
    "installedVersion": "1.0.0"
  }
]
```

---

## Get Plugin by ID

```http
GET /api/plugins/{id}
```

Returns plugin details.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Response `200 OK`

Full plugin object.

---

## Uninstall Plugin

```http
DELETE /api/plugins/{id}
```

Uninstalls a plugin (removes its files; built-ins are protected).

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Response `200 OK`

```json
{
  "message": "Plugin uninstalled"
}
```

---

## Enable Plugin

```http
POST /api/plugins/{id}/enable
```

Enables a plugin.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Response `200 OK`

```json
{
  "message": "Plugin enabled"
}
```

---

## Disable Plugin

```http
POST /api/plugins/{id}/disable
```

Disables a plugin.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Response `200 OK`

```json
{
  "message": "Plugin disabled"
}
```

---

## Update Plugin Configuration

```http
PUT /api/plugins/{id}/config
```

Updates the plugin's global configuration.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Request Body

```json
{
  "config": {
    "apiKey": "your-api-key",
    "model": "gpt-4",
    "maxTokens": 500
  }
}
```

### CRM Use Case
Configure a plugin with CRM-specific settings like API keys, endpoint URLs, or feature flags.

### Response `200 OK`

```json
{
  "message": "Plugin configuration updated"
}
```

---

## Serve Plugin Config UI

```http
GET /api/plugins/{id}/config-ui
```

Serves a plugin's sandboxed config-UI entry HTML (for use in an iframe `srcdoc`).

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### CRM Use Case
Embed the plugin's configuration UI inside the CRM settings page using an iframe, providing a seamless configuration experience.

### Response `200 OK`

HTML content.

---

## Set Session-Specific Plugin Config

```http
PUT /api/plugins/{id}/config/{sessionId}
```

Sets a plugin config override for a specific session. Send empty body to clear the override.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "greetingMessage": "Welcome to our support! How can I help?"
}
```

### CRM Use Case
Configure a plugin differently per phone number (session). For example, a greeting plugin may have different messages for sales vs. support lines.

### Response `200 OK`

```json
{
  "message": "Session config updated"
}
```

---

## Set Plugin Sessions

```http
PUT /api/plugins/{id}/sessions
```

Sets which sessions a session-scoped plugin is activated for (`['*']` = all sessions).

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Request Body

```json
{
  "sessions": ["session-uuid-1", "session-uuid-3"]
}
```

### CRM Use Case
Enable a plugin only for specific phone numbers. For example, enable an auto-reply plugin only on the support line, not the sales line.

### Response `200 OK`

```json
{
  "message": "Plugin sessions updated"
}
```

---

## Update Plugin

```http
POST /api/plugins/{id}/update
```

Updates an installed plugin in place from a URL (preserves config + enabled state).

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Request Body

```json
{
  "url": "https://plugins.example.com/crm-sync-plugin-v2.zip"
}
```

### CRM Use Case
Push plugin updates from the CRM admin panel without losing configuration.

### Response `200 OK`

```json
{
  "message": "Plugin updated",
  "oldVersion": "1.0.0",
  "newVersion": "1.1.0"
}
```

---

## Check Plugin Health

```http
GET /api/plugins/{id}/health
```

Checks a plugin's health status.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Plugin ID |

### Response `200 OK`

```json
{
  "status": "healthy",
  "uptime": 86400
}
```
