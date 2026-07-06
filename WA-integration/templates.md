# Templates — Message Templates

The Templates API allows a CRM to create, manage, and use reusable message templates. Templates are stored per-session and can contain variable placeholders for dynamic content.

---

## Template Syntax

Templates use `{{variable_name}}` syntax for placeholders:

```
Hello {{customer_name}}, your order {{order_number}} has been confirmed!
```

When sending via `send-template`, variables are substituted at send time.

---

## Create a Template

```http
POST /api/sessions/{sessionId}/templates
```

Creates a new message template for the session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "name": "order-confirmation",
  "body": "Hello {{customer_name}}, your order {{order_number}} has been confirmed! Thank you for shopping with us.",
  "header": "Order Confirmation",
  "footer": "Need help? Reply to this message."
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | yes | Unique template name (lowercase, hyphens) |
| `body` | string | yes | Template body with `{{variable}}` placeholders |
| `header` | string | no | Optional header text |
| `footer` | string | no | Optional footer text |

### CRM Use Case
Create templates for common CRM workflows: order confirmations, appointment reminders, lead follow-ups, support acknowledgements, etc.

### Response `201 Created`

```json
{
  "id": "template-uuid",
  "sessionId": "session-uuid",
  "name": "order-confirmation",
  "body": "Hello {{customer_name}}, your order {{order_number}} has been confirmed!",
  "header": "Order Confirmation",
  "footer": "Need help? Reply to this message.",
  "createdAt": "2025-01-01T00:00:00.000Z",
  "updatedAt": "2025-01-01T00:00:00.000Z"
}
```

---

## List Templates

```http
GET /api/sessions/{sessionId}/templates
```

Lists all templates for a session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Show available templates in the CRM message composer as quick-reply options.

### Response `200 OK`

```json
[
  {
    "id": "template-uuid",
    "name": "order-confirmation",
    "body": "Hello {{customer_name}}, your order {{order_number}} has been confirmed!",
    "header": "Order Confirmation",
    "createdAt": "2025-01-01T00:00:00.000Z"
  }
]
```

---

## Get Template by ID

```http
GET /api/sessions/{sessionId}/templates/{id}
```

Returns a specific template.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Template UUID |

### Response `200 OK`

Full template object.

---

## Update Template

```http
PUT /api/sessions/{sessionId}/templates/{id}
```

Updates a template.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Template UUID |

### Request Body

```json
{
  "name": "order-confirmation-v2",
  "body": "Hi {{customer_name}}, order {{order_number}} is confirmed 🎉",
  "header": "Order Confirmed!"
}
```

### CRM Use Case
Update template wording when business processes change, without needing to delete and recreate.

### Response `200 OK`

Updated template object.

---

## Delete Template

```http
DELETE /api/sessions/{sessionId}/templates/{id}
```

Deletes a template.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `id` | string | Template UUID |

### CRM Use Case
Remove outdated templates that are no longer used.

### Response `204 No Content`

---

## Suggested CRM Templates

| Template Name | Body |
|--------------|------|
| `welcome` | Welcome {{name}}! Thanks for joining {{company}}. How can we help you today? |
| `order-confirmation` | Hi {{name}}, your order #{{order_id}} is confirmed. Estimated delivery: {{delivery_date}}. |
| `appointment-reminder` | Reminder: You have an appointment on {{date}} at {{time}}. Reply to reschedule. |
| `support-ticket` | Hi {{name}}, we've received your support request (#{{ticket_id}}). Our team will respond within {{response_time}}. |
| `invoice` | Hi {{name}}, your invoice #{{invoice_id}} for {{amount}} is due on {{due_date}}. View: {{link}} |
| `feedback` | Hi {{name}}, how would you rate your recent experience with us? Reply with 1-5. |
