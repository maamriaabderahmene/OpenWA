# Auth — Authentication & API Key Management

The Auth API manages API keys used to authenticate all requests to OpenWA. Admin-level keys can create, list, update, delete, and revoke other API keys.

---

## API Key Concepts

| Concept | Description |
|---------|-------------|
| **Key Prefix** | First few characters of the key for identification (`owa_k1_...`) |
| **Full Key** | Shown only once at creation — store it securely |
| **Role** | `admin` (full access) or `readonly` (read-only endpoints) |
| **Scoping** | API keys can be scoped to specific sessions and IP addresses |
| **Revocation** | Keys can be revoked without deletion to disable access temporarily |

---

## Create API Key

```http
POST /api/auth/api-keys
```

Creates a new API key. **Admin only.**

### Request Body

```json
{
  "name": "CRM Integration Key",
  "role": "admin",
  "allowedIps": ["203.0.113.0/24"],
  "allowedSessions": ["session-uuid-1", "session-uuid-2"],
  "expiresAt": "2026-01-01T00:00:00.000Z"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | yes | Human-readable name for the key |
| `role` | string | no | `admin` (default) or `readonly` |
| `allowedIps` | string[] | no | CIDR ranges that can use this key |
| `allowedSessions` | string[] | no | Session IDs this key can access |
| `expiresAt` | string | no | ISO timestamp for key expiration |

### CRM Use Case
When integrating a CRM with OpenWA, create a dedicated API key. Use session scoping if the CRM should only access specific phone numbers. Use IP restrictions for security.

### Response `201 Created`

```json
{
  "id": "key-uuid",
  "name": "CRM Integration Key",
  "keyPrefix": "owa_k1_a1b2",
  "apiKey": "owa_k1_a1b2c3d4e5f6...",
  "role": "admin",
  "allowedIps": ["203.0.113.0/24"],
  "allowedSessions": ["session-uuid-1"],
  "isActive": true,
  "expiresAt": "2026-01-01T00:00:00.000Z",
  "createdAt": "2025-01-01T00:00:00.000Z"
}
```

**Warning:** The `apiKey` field is only returned at creation. Store it immediately.

---

## List All API Keys

```http
GET /api/auth/api-keys
```

Lists all API keys. **Admin only.**

### CRM Use Case
Display all active API keys in the OpenWA settings page of the CRM for auditing purposes.

### Response `200 OK`

```json
[
  {
    "id": "key-uuid",
    "name": "CRM Integration Key",
    "keyPrefix": "owa_k1_a1b2",
    "role": "admin",
    "allowedIps": ["203.0.113.0/24"],
    "allowedSessions": ["session-uuid-1"],
    "isActive": true,
    "lastUsedAt": "2025-01-15T12:00:00.000Z",
    "usageCount": 1523,
    "expiresAt": "2026-01-01T00:00:00.000Z",
    "createdAt": "2025-01-01T00:00:00.000Z"
  }
]
```

---

## Get API Key Details

```http
GET /api/auth/api-keys/{id}
```

Gets details for a specific API key. **Admin only.**

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | API key UUID |

### Response `200 OK`

Full key object as above (without the full `apiKey` value).

---

## Update API Key

```http
PUT /api/auth/api-keys/{id}
```

Updates an API key. **Admin only.**

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | API key UUID |

### Request Body

```json
{
  "name": "CRM Integration Key v2",
  "role": "readonly",
  "allowedIps": ["203.0.113.0/24"],
  "allowedSessions": ["session-uuid-1", "session-uuid-3"],
  "expiresAt": "2027-01-01T00:00:00.000Z"
}
```

### CRM Use Case
Update key scope when a new phone number is added to the CRM integration, or change the role if the integration no longer needs admin access.

### Response `200 OK`

Updated key object.

---

## Delete API Key

```http
DELETE /api/auth/api-keys/{id}
```

Permanently deletes an API key. **Admin only.**

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | API key UUID |

### CRM Use Case
When decommissioning a CRM integration or rotating keys, delete the old key.

### Response `204 No Content`

---

## Revoke API Key

```http
POST /api/auth/api-keys/{id}/revoke
```

Revokes an API key (marks it inactive). **Admin only.**

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | API key UUID |

### CRM Use Case
Temporarily disable a compromised key without deleting it. Reactivate by updating the key.

### Response `200 OK`

```json
{
  "id": "key-uuid",
  "isActive": false
}
```

---

## Validate an API Key

```http
POST /api/auth/validate
```

Validates an API key and returns its details.

### Request Body

```json
{
  "apiKey": "owa_k1_a1b2c3d4e5f6..."
}
```

### CRM Use Case
If the CRM stores multiple OpenWA API keys (e.g., for different tenants), validate a key at login to ensure it's still active and scoped correctly.

### Response `200 OK`

```json
{
  "valid": true,
  "key": {
    "id": "key-uuid",
    "name": "CRM Integration Key",
    "role": "admin",
    "allowedSessions": ["session-uuid-1"],
    "isActive": true
  }
}
```
