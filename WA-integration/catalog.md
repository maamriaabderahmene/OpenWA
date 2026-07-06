# Catalog — Business Catalog & Product Messaging

The Catalog API enables a CRM to interact with the WhatsApp Business catalog — retrieve product information and send product/catalog messages to contacts.

---

## Get Business Catalog Info

```http
GET /api/sessions/{sessionId}/catalog
```

Returns the business catalog information for the session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Sync the WhatsApp Business catalog into the CRM product database so agents can browse and share products during conversations.

### Response `200 OK`

```json
{
  "catalogId": "catalog-id",
  "businessName": "Your Business Name",
  "totalProducts": 45
}
```

---

## List Catalog Products

```http
GET /api/sessions/{sessionId}/catalog/products
```

Lists all products in the catalog.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Display the product catalog in the CRM product picker so agents can select and share products with customers during chats.

### Response `200 OK`

```json
[
  {
    "id": "product-id",
    "name": "Premium Plan",
    "description": "Our best-selling premium subscription",
    "price": "$29.99/month",
    "currency": "USD",
    "url": "https://example.com/product/premium",
    "thumbnailUrl": "https://example.com/images/premium-thumb.jpg"
  }
]
```

---

## Get Product Details

```http
GET /api/sessions/{sessionId}/catalog/products/{productId}
```

Returns details for a specific product.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |
| `productId` | string | Product ID |

### CRM Use Case
Show full product details in the CRM when an agent is preparing to share a product with a customer.

### Response `200 OK`

```json
{
  "id": "product-id",
  "name": "Premium Plan",
  "description": "Our best-selling premium subscription with all features",
  "price": "$29.99/month",
  "currency": "USD",
  "url": "https://example.com/product/premium",
  "images": ["https://example.com/images/premium-1.jpg"],
  "retailerId": "SKU-001"
}
```

---

## Send Product Message

```http
POST /api/sessions/{sessionId}/messages/send-product
```

Sends a product message to a chat. See [Messages](./messages.md) for details.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us",
  "productId": "product-id"
}
```

### CRM Use Case
After an agent selects a product from the CRM catalog and clicks "Share", send it to the customer as a rich product message.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Send Catalog Link

```http
POST /api/sessions/{sessionId}/messages/send-catalog
```

Sends a catalog link message, allowing the customer to browse the entire catalog.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### Request Body

```json
{
  "chatId": "1234567890@c.us"
}
```

### CRM Use Case
Send a link to the full product catalog so customers can browse and shop directly in WhatsApp.

### Response `201 Created`

```json
{
  "messageId": "true_whatsapp_message_id",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```
