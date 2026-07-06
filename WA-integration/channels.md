# Channels — Channel/Newsletter Management

Channels (formerly "Newsletters") are broadcast channels on WhatsApp. The Channels API allows a CRM to subscribe to, read, and manage channel subscriptions.

---

## Get All Subscribed Channels

```http
GET /api/sessions/{sessionId}/channels
```

Returns all channels/newsletters the session is subscribed to.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Display subscribed channels in the CRM. Allow users to follow industry news or company announcements.

### Response `200 OK`

```json
[
  {
    "id": "channel-id@newsletter",
    "name": "Company Announcements",
    "description": "Official company news and updates",
    "subscriberCount": 1250
  }
]
```

---

## Get Channel Details

```http
GET /api/sessions/{sessionId}/channels/{channelId}
```

Returns details about a specific channel.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `channelId` | string | Channel ID |

### Response `200 OK`

```json
{
  "id": "channel-id@newsletter",
  "name": "Company Announcements",
  "description": "Official company news and updates",
  "subscriberCount": 1250,
  "createdAt": "2025-01-01T00:00:00.000Z"
}
```

---

## Unsubscribe from Channel

```http
DELETE /api/sessions/{sessionId}/channels/{channelId}
```

Unsubscribes from a channel.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `channelId` | string | Channel ID |

### Response `200 OK`

```json
{
  "message": "Unsubscribed from channel"
}
```

---

## Get Channel Messages

```http
GET /api/sessions/{sessionId}/channels/{channelId}/messages
```

Returns messages from a channel.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `channelId` | string | Channel ID |
| `limit` | number | Max messages |

### CRM Use Case
Read channel updates and surface them in a CRM news feed or dashboard widget.

### Response `200 OK`

```json
[
  {
    "id": "message-id",
    "channelId": "channel-id@newsletter",
    "text": "Important update about our Q2 strategy...",
    "timestamp": "2025-01-01T00:00:00.000Z"
  }
]
```

---

## Subscribe to Channel

```http
POST /api/sessions/{sessionId}/channels/subscribe
```

Subscribes to a channel using an invite code.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "inviteCode": "CHANNEL_INVITE_CODE"
}
```

### CRM Use Case
Allow CRM users to subscribe to channels by entering an invite code from the CRM interface.

### Response `200 OK`

```json
{
  "id": "channel-id@newsletter",
  "name": "Company Announcements",
  "subscriberCount": 1251,
  "message": "Subscribed successfully"
}
```
