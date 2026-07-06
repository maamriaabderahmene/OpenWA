# Labels — Label Management (WhatsApp Business)

WhatsApp Business labels allow you to organize and categorize chats. The Labels API lets a CRM read and manage these labels programmatically.

---

## Get All Labels

```http
GET /api/sessions/{sessionId}/labels
```

Returns all labels for a session (WhatsApp Business only).

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Sync WhatsApp Business labels to CRM tags/categories. Allow mapping WhatsApp labels to CRM pipelines (e.g., "New Lead", "Follow Up", "Closed Won").

### Response `200 OK`

```json
[
  {
    "id": "label-id",
    "name": "New Lead",
    "color": 1,
    "count": 15
  },
  {
    "id": "label-id-2",
    "name": "Follow Up",
    "color": 2,
    "count": 8
  }
]
```

---

## Get Label by ID

```http
GET /api/sessions/{sessionId}/labels/{labelId}
```

Returns a specific label.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `labelId` | string | Label ID |

### Response `200 OK`

```json
{
  "id": "label-id",
  "name": "New Lead",
  "color": 1,
  "count": 15
}
```

---

## Get Labels for a Chat

```http
GET /api/sessions/{sessionId}/labels/chat/{chatId}
```

Returns all labels applied to a specific chat.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `chatId` | string | Chat ID |

### CRM Use Case
When opening a conversation in the CRM, show which WhatsApp Business labels are applied. Use this to sync label state.

### Response `200 OK`

```json
{
  "chatId": "1234567890@c.us",
  "labels": [
    {
      "id": "label-id",
      "name": "VIP Customer",
      "color": 3
    }
  ]
}
```

---

## Add Label to Chat

```http
POST /api/sessions/{sessionId}/labels/chat/{chatId}
```

Adds a label to a chat.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `chatId` | string | Chat ID |

### Request Body

```json
{
  "labelId": "label-id"
}
```

### CRM Use Case
When a CRM user moves a deal to a new stage, automatically apply the corresponding WhatsApp label. Or provide a "Tag Chat" button that lets agents assign labels from the CRM.

### Response `200 OK`

```json
{
  "message": "Label added to chat"
}
```

---

## Remove Label from Chat

```http
DELETE /api/sessions/{sessionId}/labels/chat/{chatId}/{labelId}
```

Removes a label from a chat.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `chatId` | string | Chat ID |
| `labelId` | string | Label ID to remove |

### CRM Use Case
When a deal stage changes, remove old labels and add new ones. Keep WhatsApp labels in sync with CRM pipelines.

### Response `200 OK`

```json
{
  "message": "Label removed from chat"
}
```

---

## CRM Integration Suggestion

Map CRM pipeline stages to WhatsApp Business labels:

| CRM Stage | WhatsApp Label |
|-----------|---------------|
| New Lead | 🟢 New Lead |
| Contacted | 🔵 Contacted |
| Follow Up | 🟡 Follow Up |
| Negotiation | 🟠 Negotiation |
| Closed Won | 🟢 Closed Won |
| Closed Lost | 🔴 Closed Lost |

When a deal moves between stages in your CRM, automatically add/remove labels via the Labels API to keep WhatsApp organized.
