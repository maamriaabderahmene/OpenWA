# Groups — Group Management

The Groups API enables a CRM to manage WhatsApp groups directly — create, update, moderate, and retrieve group information.

---

## Get All Groups

```http
GET /api/sessions/{sessionId}/groups
```

Returns all WhatsApp groups the session is a member of.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `limit` | number | Max results |
| `offset` | number | Pagination offset |

### CRM Use Case
Display all groups a phone number belongs to. Useful for routing group messages and showing group membership in the CRM.

### Response `200 OK`

```json
[
  {
    "id": "group-id@g.us",
    "name": "Project Alpha",
    "subject": "Project Alpha Discussion",
    "participantCount": 15,
    "desc": "Discussion group for Project Alpha",
    "descId": "description-version-id"
  }
]
```

---

## Create a Group

```http
POST /api/sessions/{sessionId}/groups
```

Creates a new WhatsApp group.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "name": "Project Alpha Team",
  "participants": ["1234567890@c.us", "0987654321@c.us"]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | yes | Group subject/name |
| `participants` | string[] | yes | Array of WhatsApp IDs to add |

### CRM Use Case
Create a WhatsApp group for a project team, sales team, or customer support group directly from the CRM. For example, when a new project is created in the CRM, automatically create the corresponding WhatsApp group.

### Response `201 Created`

```json
{
  "id": "group-id@g.us",
  "name": "Project Alpha Team",
  "participantCount": 3,
  "createdAt": "2025-01-01T00:00:00.000Z"
}
```

---

## Get Group Details

```http
GET /api/sessions/{sessionId}/groups/{groupId}
```

Returns detailed information about a group.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID (`group-id@g.us`) |

### CRM Use Case
View group details from a CRM group profile — see all participants, description, and metadata.

### Response `200 OK`

```json
{
  "id": "group-id@g.us",
  "name": "Project Alpha",
  "subject": "Project Alpha Discussion",
  "desc": "Discussion group for the project",
  "participants": [
    {
      "id": "1234567890@c.us",
      "admin": true,
      "name": "John Doe"
    },
    {
      "id": "0987654321@c.us",
      "admin": false,
      "name": "Jane Smith"
    }
  ],
  "participantCount": 15,
  "createdAt": "2025-01-01T00:00:00.000Z"
}
```

---

## Add Participants

```http
POST /api/sessions/{sessionId}/groups/{groupId}/participants
```

Adds participants to a group.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### Request Body

```json
{
  "participants": ["1122334455@c.us", "5544332211@c.us"]
}
```

### CRM Use Case
When a new team member joins a project in the CRM, automatically add them to the corresponding WhatsApp group.

### Response `200 OK`

```json
{
  "message": "Participants added",
  "added": ["1122334455@c.us"],
  "failed": ["5544332211@c.us"]
}
```

---

## Remove Participants

```http
DELETE /api/sessions/{sessionId}/groups/{groupId}/participants
```

Removes participants from a group.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### Request Body

```json
{
  "participants": ["1122334455@c.us"]
}
```

### CRM Use Case
When someone leaves the team in the CRM, remove them from the corresponding WhatsApp group.

### Response `200 OK`

```json
{
  "message": "Participants removed",
  "removed": ["1122334455@c.us"]
}
```

---

## Promote Participants to Admin

```http
POST /api/sessions/{sessionId}/groups/{groupId}/participants/promote
```

Promotes participants to group admin.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### Request Body

```json
{
  "participants": ["1234567890@c.us"]
}
```

### CRM Use Case
When a project lead is assigned in the CRM, promote them to group admin automatically.

### Response `200 OK`

```json
{
  "message": "Participants promoted",
  "promoted": ["1234567890@c.us"]
}
```

---

## Demote Participants from Admin

```http
POST /api/sessions/{sessionId}/groups/{groupId}/participants/demote
```

Demotes participants from group admin.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### Request Body

```json
{
  "participants": ["1234567890@c.us"]
}
```

### CRM Use Case
When a team lead role changes, demote from admin status.

### Response `200 OK`

```json
{
  "message": "Participants demoted",
  "demoted": ["1234567890@c.us"]
}
```

---

## Change Group Subject

```http
PUT /api/sessions/{sessionId}/groups/{groupId}/subject
```

Changes the group name/subject.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### Request Body

```json
{
  "subject": "Project Alpha - Q1 2025"
}
```

### CRM Use Case
Update the group name when a project phase changes or the team name is updated in the CRM.

### Response `200 OK`

```json
{
  "message": "Group subject updated"
}
```

---

## Change Group Description

```http
PUT /api/sessions/{sessionId}/groups/{groupId}/description
```

Changes the group description.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### Request Body

```json
{
  "description": "Q1 2025 planning and updates for the Project Alpha team."
}
```

### CRM Use Case
Update the group description to reflect current project status, pinned links, or guidelines.

### Response `200 OK`

```json
{
  "message": "Group description updated"
}
```

---

## Leave a Group

```http
POST /api/sessions/{sessionId}/groups/{groupId}/leave
```

Leaves a group.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### CRM Use Case
When a phone number is being reassigned or decommissioned, automatically leave all groups before removal.

### Response `200 OK`

```json
{
  "message": "Left the group"
}
```

---

## Get Invite Code

```http
GET /api/sessions/{sessionId}/groups/{groupId}/invite-code
```

Gets the group invite code/link.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### CRM Use Case
Share the invite link in the CRM so team members can join the WhatsApp group.

### Response `200 OK`

```json
{
  "inviteCode": "ABCDEF1234",
  "inviteLink": "https://chat.whatsapp.com/ABCDEF1234"
}
```

---

## Revoke Invite Code

```http
POST /api/sessions/{sessionId}/groups/{groupId}/invite-code/revoke
```

Revokes the current invite code and generates a new one.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `groupId` | string | Group ID |

### CRM Use Case
When a previous invite link was shared publicly and needs to be invalidated for security.

### Response `200 OK`

```json
{
  "inviteCode": "NEWCODE5678",
  "inviteLink": "https://chat.whatsapp.com/NEWCODE5678"
}
```
