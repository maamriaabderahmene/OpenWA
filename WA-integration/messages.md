# Messages — Send and Manage Messages

The Messages API allows a CRM to send all WhatsApp message types, retrieve message history, manage reactions, and process bulk messaging campaigns.

---

## Message Types at a Glance

| Type | Endpoint | CRM Use Case |
|------|----------|-------------|
| Text | `send-text` | Standard chat replies, notifications |
| Template | `send-template` | Saved response templates (SLAs, disclaimers) |
| Image | `send-image` | Product photos, screenshots |
| Video | `send-video` | Demos, tutorials |
| Audio | `send-audio` | Voice notes |
| Document | `send-document` | PDF invoices, contracts, reports |
| Location | `send-location` | Store locator, dispatch coordinates |
| Contact | `send-contact` | Share a contact card |
| Sticker | `send-sticker` | Brand stickers |
| Reply | `reply` | Quoted replies in conversations |
| Forward | `forward` | Forward conversations to another team |
| React | `react` | Emoji reactions |
| Bulk | `send-bulk` | Campaigns, announcements, broadcasts |

---

## Send Text Message

```http
POST /api/sessions/{sessionId}/messages/send-text
```

Sends a plain text message to a WhatsApp contact.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "text": "Hello! Thank you for contacting support."
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Recipient WhatsApp ID (`number@c.us` or `group@g.us`) |
| `text` | string | yes | Message text content |

### CRM Use Case
An agent types and sends a reply from the CRM chat interface. Every outbound message from the CRM goes through this endpoint.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Template Message

```http
POST /api/sessions/{sessionId}/messages/send-template
```

Renders a stored text template and sends it as a text message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "templateId": "template-uuid",
  "templateName": "order-confirmation",
  "vars": {
    "customer_name": "John",
    "order_number": "ORD-12345"
  }
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Recipient WhatsApp ID |
| `templateId` | string | no* | ID of a saved template |
| `templateName` | string | no* | Name of a saved template |
| `vars` | object | no | Variables to substitute into the template |

*Either `templateId` or `templateName` is required.

### CRM Use Case
Send standardized responses like order confirmations, appointment reminders, or SLA acknowledgements. The CRM can store templates and populate variables from CRM data (e.g., customer name, order ID).

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Image Message

```http
POST /api/sessions/{sessionId}/messages/send-image
```

Sends an image message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "url": "https://example.com/image.jpg",
  "caption": "Here's your invoice"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Recipient WhatsApp ID |
| `url` | string | no* | Public URL of the image |
| `base64` | string | no* | Base64-encoded image data |
| `mimetype` | string | no | MIME type (e.g., `image/jpeg`) |
| `filename` | string | no | Display filename |
| `caption` | string | no | Image caption |

*Either `url` or `base64` is required.

### CRM Use Case
Send product photos from your catalog, screenshot attachments, or scanned documents directly from CRM records.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Video Message

```http
POST /api/sessions/{sessionId}/messages/send-video
```

Sends a video message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "url": "https://example.com/video.mp4",
  "caption": "Product demo"
}
```

Parameters same as image (url, base64, mimetype, filename, caption).

### CRM Use Case
Share product demos, onboarding videos, or training content with customers.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Audio Message

```http
POST /api/sessions/{sessionId}/messages/send-audio
```

Sends an audio/voice message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "url": "https://example.com/audio.ogg"
}
```

Parameters same as image (url, base64, mimetype, filename).

### CRM Use Case
Send pre-recorded voice messages, voicemail greetings, or audio instructions.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Document Message

```http
POST /api/sessions/{sessionId}/messages/send-document
```

Sends a document/file.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "url": "https://example.com/invoice.pdf",
  "filename": "Invoice-12345.pdf",
  "caption": "Your invoice is attached"
}
```

Parameters same as image (url, base64, mimetype, filename, caption).

### CRM Use Case
Send PDF invoices, signed contracts, Excel reports, or any file stored in your CRM document management system.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Location Message

```http
POST /api/sessions/{sessionId}/messages/send-location
```

Sends a location pin on a map.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "latitude": -6.2088,
  "longitude": 106.8456,
  "title": "Our Office",
  "address": "123 Main Street, Jakarta"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Recipient WhatsApp ID |
| `latitude` | number | yes | Latitude coordinate |
| `longitude` | number | yes | Longitude coordinate |
| `title` | string | no | Location name |
| `address` | string | no | Address description |

### CRM Use Case
Share office locations, delivery addresses, meetup points, or field agent locations with customers.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Contact Message

```http
POST /api/sessions/{sessionId}/messages/send-contact
```

Sends a contact card (vCard).

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "contactName": "Jane Smith",
  "contactNumber": "0987654321"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Recipient WhatsApp ID |
| `contactName` | string | yes | Name on the contact card |
| `contactNumber` | string | yes | Phone number for the contact |

### CRM Use Case
Share a colleague's contact card with a customer, or share a customer's contact with another team member.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Sticker Message

```http
POST /api/sessions/{sessionId}/messages/send-sticker
```

Sends a sticker message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "url": "https://example.com/sticker.webp"
}
```

Parameters same as image (url, base64, mimetype).

### CRM Use Case
Send brand stickers, celebration stickers for milestones, or fun engagement stickers.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Reply to Message

```http
POST /api/sessions/{sessionId}/messages/reply
```

Sends a quoted reply to an existing message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "quotedMessageId": "true_whatsapp_message_id",
  "text": "I'll look into this right away."
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Chat ID |
| `quotedMessageId` | string | yes | The message ID to reply to |
| `text` | string | yes | Reply text |

### CRM Use Case
When an agent clicks "Reply" on a specific message in the CRM conversation view, thread the response as a quoted reply.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Forward Message

```http
POST /api/sessions/{sessionId}/messages/forward
```

Forwards a message from one chat to another.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "fromChatId": "1234567890@c.us",
  "toChatId": "0987654321@c.us",
  "messageId": "true_whatsapp_message_id"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `fromChatId` | string | yes | Source chat ID |
| `toChatId` | string | yes | Destination chat ID |
| `messageId` | string | yes | Message ID to forward |

### CRM Use Case
Forward a customer conversation to a senior agent or specialist for escalation. Forward group messages to individual contacts.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## React to Message

```http
POST /api/sessions/{sessionId}/messages/react
```

Adds or removes an emoji reaction to a message. Send an empty string to remove a reaction.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "messageId": "true_whatsapp_message_id",
  "emoji": "👍"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Chat ID |
| `messageId` | string | yes | Message ID to react to |
| `emoji` | string | yes | Emoji character (empty string to remove) |

### CRM Use Case
Allow agents to react to customer messages with emojis from the CRM interface. Quick ack with 👍 or ❤️.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Get Message History

```http
GET /api/sessions/{sessionId}/messages
```

Returns stored message history for a session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `chatId` | string | Filter by chat ID |
| `limit` | number | Max results |
| `offset` | number | Pagination offset |
| `startDate` | string | ISO date filter start |
| `endDate` | string | ISO date filter end |

### CRM Use Case
Populate the CRM conversation history panel. Filter by contact to show the full message thread.

### Response `200 OK`

```json
[
  {
    "id": "message-uuid",
    "messageId": "true_whatsapp_message_id",
    "chatId": "1234567890@c.us",
    "fromMe": true,
    "type": "text",
    "text": "Hello!",
    "timestamp": "2025-01-01T00:00:00.000Z",
    "status": "read"
  }
]
```

---

## Fetch Live Chat History

```http
GET /api/sessions/{sessionId}/messages/{chatId}/history
```

Fetches chat history live from WhatsApp, not from the local database.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `chatId` | string | Chat to fetch history for |
| `limit` | number | Max messages to fetch |
| `offset` | string | Message ID offset for pagination |

### CRM Use Case
When opening a new or unknown chat, pull the live conversation history directly from WhatsApp to display in the CRM.

### Response `200 OK`

```json
{
  "messages": [
    {
      "id": "true_whatsapp_message_id",
      "chatId": "1234567890@c.us",
      "fromMe": true,
      "type": "text",
      "text": "Hello!",
      "timestamp": "2025-01-01T00:00:00.000Z"
    }
  ],
  "hasMore": false
}
```

---

## Get Reactions

```http
GET /api/sessions/{sessionId}/messages/{chatId}/{messageId}/reactions
```

Get all reactions for a specific message.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `chatId` | string | Chat ID |
| `messageId` | string | Message ID |

### CRM Use Case
Display reaction details on messages in the CRM conversation view.

### Response `200 OK`

```json
{
  "messageId": "true_whatsapp_message_id",
  "reactions": [
    {
      "sender": "0987654321@c.us",
      "emoji": "👍",
      "timestamp": "2025-01-01T00:00:00.000Z"
    }
  ]
}
```

---

## Delete Message

```http
POST /api/sessions/{sessionId}/messages/delete
```

Deletes a message (for everyone or just yourself).

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "messageId": "true_whatsapp_message_id",
  "forEveryone": true
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `chatId` | string | yes | Chat ID |
| `messageId` | string | yes | Message ID to delete |
| `forEveryone` | boolean | no | Delete for all participants (default false) |

### CRM Use Case
Allow agents to recall mistakenly sent messages ("unsend").

### Response `200 OK`

```json
{
  "message": "Message deleted"
}
```

---

## Send Bulk Messages

```http
POST /api/sessions/{sessionId}/messages/send-bulk
```

Sends messages to multiple recipients as an async batch process. Returns immediately with a batch ID for status tracking.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "batchId": "my-campaign-id",
  "messages": [
    {
      "chatId": "1234567890@c.us",
      "type": "text",
      "content": {
        "text": "Hello {{customer_name}}!"
      },
      "variables": {
        "customer_name": "John"
      }
    },
    {
      "chatId": "0987654321@c.us",
      "type": "text",
      "content": {
        "text": "Hello {{customer_name}}!"
      },
      "variables": {
        "customer_name": "Jane"
      }
    },
    {
      "chatId": "1122334455@c.us",
      "type": "image",
      "content": {
        "url": "https://example.com/promo.jpg",
        "caption": "Check out our latest offer!"
      }
    }
  ],
  "options": {
    "delayBetweenMessages": 2000,
    "randomizeDelay": true,
    "stopOnError": false
  }
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `batchId` | string | no | Optional custom batch identifier |
| `messages` | array | yes | Array of message objects |
| `messages[].chatId` | string | yes | Recipient WhatsApp ID |
| `messages[].type` | string | yes | `text`, `image`, `video`, `audio`, `document` |
| `messages[].content` | object | yes | Content fields matching the corresponding send type |
| `messages[].variables` | object | no | Template variables ({{var}} in text will be replaced) |
| `options.delayBetweenMessages` | number | no | Delay in ms between messages (default 1000) |
| `options.randomizeDelay` | boolean | no | Randomize delay to appear more natural |
| `options.stopOnError` | boolean | no | Stop batch on first error |

### CRM Use Case
Send promotional campaigns, broadcast announcements, or bulk notifications to customer segments. The CRM can build the message list from a filtered contact list.

### Response `201 Created`

```json
{
  "batchId": "b-uuid-or-custom-id",
  "status": "processing",
  "totalMessages": 3,
  "estimatedCompletionTime": "2025-01-01T00:01:00.000Z",
  "statusUrl": "/api/sessions/{sessionId}/messages/batch/{batchId}"
}
```

---

## Get Batch Status

```http
GET /api/sessions/{sessionId}/messages/batch/{batchId}
```

Gets the processing status of a bulk message batch.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `batchId` | string | Batch ID |

### CRM Use Case
Poll this endpoint to show a progress bar for the bulk campaign in the CRM UI.

### Response `200 OK`

```json
{
  "batchId": "b-uuid",
  "status": "processing",
  "totalMessages": 100,
  "sent": 45,
  "failed": 2,
  "progress": 47,
  "estimatedCompletionTime": "2025-01-01T00:05:00.000Z"
}
```

---

## Cancel Batch

```http
POST /api/sessions/{sessionId}/messages/batch/{batchId}/cancel
```

Cancels a running batch.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `batchId` | string | Batch ID |

### CRM Use Case
If an error is detected in the campaign or a mistake is spotted, allow the CRM user to cancel the batch immediately.

### Response `200 OK`

```json
{
  "batchId": "b-uuid",
  "status": "cancelled",
  "sent": 45,
  "failed": 0
}
```
