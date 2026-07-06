# Sessions — WhatsApp Session Management

A **session** represents a single WhatsApp account connection. Before sending or receiving messages, you must create and authenticate a session.

---

## Session Lifecycle for CRM Integration

```
Create → Start → Authenticate (QR/Pairing) → Connected → [Send/Receive] → Stop → Delete
```

A CRM typically:
1. Creates a session per phone number
2. Authenticates via QR code (shown in CRM UI) or pairing code
3. Stores the session ID with the CRM contact/user
4. Uses the session ID for all subsequent messaging

---

## Create a Session

```http
POST /api/sessions
```

Creates a new WhatsApp session. The session is created in a `stopped` state and must be started and authenticated before use.

### Request Body

```json
{
  "name": "support-line-1",
  "config": {
    "phoneNumber": "1234567890"
  }
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | yes | Unique, alphanumeric name with hyphens |
| `config` | object | no | Session configuration object |
| `config.phoneNumber` | string | no | Phone number associated with this session |
| `proxyUrl` | string | no | Optional proxy URL for this session |
| `proxyType` | string | no | Proxy type (`socks5`, `http`, `https`) |

### CRM Use Case
Register a new sales rep's or support agent's WhatsApp number in your CRM. Store the returned session ID in your CRM user profile.

### Response `201 Created`

```json
{
  "id": "uuid-session-id",
  "name": "support-line-1",
  "status": "stopped",
  "createdAt": "2025-01-01T00:00:00.000Z",
  "updatedAt": "2025-01-01T00:00:00.000Z"
}
```

---

## List All Sessions

```http
GET /api/sessions
```

Returns all sessions visible to the API key.

### Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| `limit` | number | Max results to return |
| `offset` | number | Offset for pagination |

### CRM Use Case
Display a dashboard of all connected phone numbers, their status, and last activity. Filter by status to show only connected sessions.

### Response `200 OK`

```json
[
  {
    "id": "uuid-session-id",
    "name": "support-line-1",
    "status": "connected",
    "phone": "1234567890",
    "pushName": "Support Team",
    "connectedAt": "2025-01-01T00:00:00.000Z",
    "lastActive": "2025-01-01T12:00:00.000Z",
    "createdAt": "2025-01-01T00:00:00.000Z",
    "updatedAt": "2025-01-01T12:00:00.000Z"
  }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |
| `name` | string | Session name |
| `status` | string | `stopped`, `starting`, `connected`, `disconnected`, `error` |
| `phone` | string | Connected phone number |
| `pushName` | string | WhatsApp profile name |
| `connectedAt` | string | ISO timestamp of last connection |
| `lastActive` | string | ISO timestamp of last activity |
| `lastError` | string | Last error message (if in error state) |
| `createdAt` | string | ISO creation timestamp |
| `updatedAt` | string | ISO last update timestamp |

---

## Get Session by ID

```http
GET /api/sessions/{id}
```

Returns full details for a single session.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
Check a specific agent's WhatsApp connection status before attempting to send a message. If disconnected, prompt re-authentication.

### Response `200 OK`

Same shape as individual session object above.

---

## Delete a Session

```http
DELETE /api/sessions/{id}
```

Permanently deletes a session and all its data.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
When an agent leaves the company or changes phone numbers, remove their session from the system.

### Response `204 No Content`

---

## Start a Session

```http
POST /api/sessions/{id}/start
```

Starts a stopped session and attempts to connect to WhatsApp.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
When an agent logs into the CRM for the first time each day, auto-start their WhatsApp session.

### Response `200 OK`

```json
{
  "id": "uuid-session-id",
  "status": "starting",
  "message": "Session is starting"
}
```

---

## Stop a Session

```http
POST /api/sessions/{id}/stop
```

Gracefully stops a session and disconnects from WhatsApp.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
When an agent goes offline or ends their shift, stop their session to free resources.

### Response `200 OK`

```json
{
  "id": "uuid-session-id",
  "status": "stopped",
  "message": "Session stopped"
}
```

---

## Force-Kill a Session

```http
POST /api/sessions/{id}/force-kill
```

Forcefully terminates a stuck session (sends SIGKILL to the engine process, then cleans up). Use only when a normal stop fails.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
If a session is stuck in `starting` or `error` state and won't respond to stop, force-kill it programmatically via a "Reset Connection" button.

### Response `200 OK`

```json
{
  "message": "Session force-killed",
  "id": "uuid-session-id"
}
```

---

## Get QR Code

```http
GET /api/sessions/{id}/qr
```

Returns a QR code for authenticating the session by scanning with WhatsApp mobile app.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
Display the QR code in the CRM UI for the agent to scan with WhatsApp. Poll this endpoint until a code is available.

### Response `200 OK`

```json
{
  "qrCode": "data:image/png;base64,iVBOR...",
  "status": "qr_available"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `qrCode` | string | Base64-encoded PNG image data URI |
| `status` | string | `qr_available`, `connecting`, `connected`, `expired` |

---

## Request Pairing Code

```http
POST /api/sessions/{id}/pairing-code
```

Requests an 8-character pairing code as an alternative to QR code authentication. The user enters this code in WhatsApp's "Link a Device" screen.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### Request Body

```json
{
  "phoneNumber": "1234567890"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `phoneNumber` | string | yes | Phone number to pair with |

### CRM Use Case
For remote agents who cannot scan a QR code, display the pairing code and instruct them to enter it in WhatsApp Settings → Linked Devices → Link a Device.

### Response `200 OK`

```json
{
  "pairingCode": "ABCD1234",
  "status": "pairing_code_generated"
}
```

---

## Get Session Groups

```http
GET /api/sessions/{id}/groups
```

Returns all WhatsApp groups the session is a member of.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### CRM Use Case
Display all groups a phone number belongs to. Useful for routing group messages to the right team member.

### Response `200 OK`

```json
[
  {
    "id": "group-id@g.us",
    "name": "Project Alpha",
    "participantCount": 15,
    "subject": "Project Alpha Discussion"
  }
]
```

---

## Get Active Chats

```http
GET /api/sessions/{id}/chats
```

Returns active chats/conversations for the session.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |
| `limit` | number | Max results |
| `offset` | number | Pagination offset |

### CRM Use Case
Populate a "Recent Conversations" panel in the CRM. Show active chats grouped by contact or group.

### Response `200 OK`

```json
[
  {
    "id": "contact@c.us",
    "name": "John Doe",
    "lastMessage": {
      "text": "Thanks for your help!",
      "timestamp": "2025-01-01T12:00:00.000Z"
    },
    "unreadCount": 2
  }
]
```

---

## Mark Chat as Read

```http
POST /api/sessions/{id}/chats/read
```

Marks a chat as read/seen on WhatsApp.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### Request Body

```json
{
  "chatId": "contact@c.us"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Chat ID to mark as read |

### CRM Use Case
When an agent opens a conversation in the CRM, automatically mark it as read on WhatsApp so the blue double-check appears.

### Response `200 OK`

```json
{
  "message": "Chat marked as read"
}
```

---

## Mark Chat as Unread

```http
POST /api/sessions/{id}/chats/unread
```

Marks a chat as unread.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### Request Body

```json
{
  "chatId": "contact@c.us"
}
```

### CRM Use Case
Allow agents to mark conversations for follow-up by setting them as unread.

### Response `200 OK`

```json
{
  "message": "Chat marked as unread"
}
```

---

## Delete a Chat

```http
POST /api/sessions/{id}/chats/delete
```

Deletes a chat from the chat list. Useful for cleaning up left groups or unwanted conversations.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### Request Body

```json
{
  "chatId": "group-id@g.us"
}
```

### CRM Use Case
When an agent leaves a group via the CRM, remove the chat from the chat list to keep the view clean.

### Response `200 OK`

```json
{
  "message": "Chat deleted"
}
```

---

## Send Chat Presence

```http
POST /api/sessions/{id}/chats/typing
```

Sends a typing, recording, or paused presence indicator to a chat.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Session UUID |

### Request Body

```json
{
  "chatId": "contact@c.us",
  "state": "typing"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Chat to send presence to |
| `state` | string | yes | `typing`, `recording`, or `paused` |

### CRM Use Case
Show the contact a "typing..." indicator when the agent is composing a message in the CRM. Set to `paused` when the agent stops typing.

### Response `200 OK`

```json
{
  "message": "Presence indicator sent"
}
```

---

## Session Statistics Overview

```http
GET /api/sessions/stats/overview
```

Returns multi-session monitoring statistics.

### CRM Use Case
Display a dashboard widget showing total sessions, connected count, disconnected sessions, and messages processed today across all phone numbers.

### Response `200 OK`

```json
{
  "total": 5,
  "connected": 3,
  "disconnected": 1,
  "error": 1,
  "totalMessagesToday": 247
}
```
