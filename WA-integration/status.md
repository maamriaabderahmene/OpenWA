# Status — WhatsApp Status Updates

The Status API allows a CRM to post and read WhatsApp Status updates (stories). Posting status is available for Baileys-based sessions only.

---

## Get All Contact Status Updates

```http
GET /api/sessions/{sessionId}/status
```

Returns all status updates from the session's contacts.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Monitor contacts' status updates for business-relevant announcements (e.g., "Back in office Monday", "New product launched"). Surface these in a CRM activity feed.

### Response `200 OK`

```json
[
  {
    "id": "status-id",
    "contactId": "1234567890@c.us",
    "contactName": "John Doe",
    "type": "text",
    "text": "Back in the office on Monday!",
    "timestamp": "2025-01-01T08:00:00.000Z",
    "expiresAt": "2025-01-02T08:00:00.000Z"
  }
]
```

---

## Get Contact Status Updates

```http
GET /api/sessions/{sessionId}/status/{contactId}
```

Returns status updates from a specific contact.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `contactId` | string | Contact WhatsApp ID |

### Response `200 OK`

```json
[
  {
    "id": "status-id",
    "contactId": "1234567890@c.us",
    "type": "image",
    "url": "...",
    "caption": "New product launch!",
    "timestamp": "2025-01-01T08:00:00.000Z"
  }
]
```

---

## Post Text Status

```http
POST /api/sessions/{sessionId}/status/send-text
```

Posts a text status update. **Baileys only.**

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "text": "We're hiring! Check out our careers page."
}
```

### CRM Use Case
Post company announcements, recruitment drives, or promotional offers as WhatsApp status updates directly from the CRM marketing module.

### Response `201 Created`

```json
{
  "statusId": "status-id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Post Image Status

```http
POST /api/sessions/{sessionId}/status/send-image
```

Posts an image status update. **Baileys only.**

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "url": "https://example.com/promo-image.jpg",
  "caption": "New collection dropping soon!"
}
```

### CRM Use Case
Post product images, event flyers, or brand visuals to engage contacts.

### Response `201 Created`

```json
{
  "statusId": "status-id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Post Video Status

```http
POST /api/sessions/{sessionId}/status/send-video
```

Posts a video status update. **Baileys only.**

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "url": "https://example.com/promo-video.mp4",
  "caption": "See our product in action!"
}
```

### CRM Use Case
Post product demos, behind-the-scenes videos, or event recaps as status updates.

### Response `201 Created`

```json
{
  "statusId": "status-id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Delete Status

```http
DELETE /api/sessions/{sessionId}/status/{statusId}
```

Deletes own status update.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `statusId` | string | Status ID to delete |

### CRM Use Case
Remove outdated or incorrect status posts from the CRM.

### Response `200 OK`

```json
{
  "message": "Status deleted"
}
```
