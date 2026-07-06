# Contacts — Contact Management

The Contacts API allows a CRM to sync WhatsApp contacts, check WhatsApp registration, manage blocking, and retrieve profile information.

---

## Get All Contacts

```http
GET /api/sessions/{sessionId}/contacts
```

Returns all contacts for a session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `limit` | number | Max results |
| `offset` | number | Pagination offset |

### CRM Use Case
Sync the WhatsApp contact list into your CRM's contact database. Run this on a schedule (e.g., daily) to keep contacts up to date.

### Response `200 OK`

```json
[
  {
    "id": "1234567890@c.us",
    "name": "John Doe",
    "pushName": "John",
    "number": "1234567890",
    "isBusiness": false,
    "isMyContact": true
  }
]
```

---

## Get Contact by ID

```http
GET /api/sessions/{sessionId}/contacts/{contactId}
```

Returns details for a specific contact.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `contactId` | string | Contact WhatsApp ID |

### CRM Use Case
Look up a contact's WhatsApp details when opening their CRM profile to verify the right number is being messaged.

### Response `200 OK`

```json
{
  "id": "1234567890@c.us",
  "name": "John Doe",
  "pushName": "John",
  "number": "1234567890",
  "isBusiness": false,
  "isMyContact": true
}
```

---

## Check WhatsApp Registration

```http
GET /api/sessions/{sessionId}/contacts/check/{number}
```

Checks if a phone number is registered on WhatsApp.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `number` | string | Phone number to check |

### CRM Use Case
Before sending a message to a new CRM contact, verify the number is on WhatsApp. If not, flag it in the CRM or try SMS instead.

### Response `200 OK`

```json
{
  "number": "1234567890",
  "registered": true,
  "jid": "1234567890@s.whatsapp.net"
}
```

---

## Get Profile Picture

```http
GET /api/sessions/{sessionId}/contacts/{contactId}/profile-picture
```

Gets the profile picture URL for a contact.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `contactId` | string | Contact WhatsApp ID |

### CRM Use Case
Display the contact's WhatsApp profile picture in the CRM contact detail view for easy recognition.

### Response `200 OK`

```json
{
  "profilePictureUrl": "https://pps.whatsapp.net/v/t61.24694-24/12345.jpg",
  "contactId": "1234567890@c.us"
}
```

---

## Resolve Contact ID to Phone Number

```http
GET /api/sessions/{sessionId}/contacts/{contactId}/phone
```

Resolves a contact ID (e.g., an @lid or JID) to a phone number — best-effort.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `contactId` | string | Contact ID (may be JID, LID, or regular ID) |

### CRM Use Case
When receiving a webhook with a sender ID that isn't a standard phone number format, resolve it to a phone number for CRM matching.

### Response `200 OK`

```json
{
  "contactId": "1234567890@c.us",
  "phoneNumber": "1234567890",
  "lid": "1234567890@lid"
}
```

---

## Block a Contact

```http
POST /api/sessions/{sessionId}/contacts/{contactId}/block
```

Blocks a contact on WhatsApp.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `contactId` | string | Contact to block |

### CRM Use Case
Allow agents to block spam or abusive contacts directly from the CRM interface.

### Response `200 OK`

```json
{
  "message": "Contact blocked",
  "contactId": "1234567890@c.us"
}
```

---

## Unblock a Contact

```http
DELETE /api/sessions/{sessionId}/contacts/{contactId}/block
```

Unblocks a previously blocked contact.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `contactId` | string | Contact to unblock |

### CRM Use Case
If a block was accidental or the issue is resolved, unblock from the CRM.

### Response `200 OK`

```json
{
  "message": "Contact unblocked",
  "contactId": "1234567890@c.us"
}
```
