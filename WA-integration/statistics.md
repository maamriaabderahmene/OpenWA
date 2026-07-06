# Statistics — Usage Analytics

The Statistics API provides aggregated usage data for monitoring and reporting. Use these endpoints to build CRM dashboards and analytics.

---

## Get Overall Statistics

```http
GET /api/stats/overview
```

Returns overall system statistics.

### CRM Use Case
Display a high-level analytics widget in the CRM dashboard showing total messages, active sessions, and daily averages.

### Response `200 OK`

```json
{
  "totalMessagesSent": 15234,
  "totalMessagesReceived": 8921,
  "activeSessions": 3,
  "totalSessions": 5,
  "messagesToday": 247,
  "messagesThisWeek": 1823,
  "messagesThisMonth": 7234
}
```

---

## Get Message Statistics with Time Series

```http
GET /api/stats/messages
```

Returns message statistics with time series data.

### Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| `from` | string | ISO start date |
| `to` | string | ISO end date |
| `granularity` | string | `hour`, `day`, `week`, `month` |

### CRM Use Case
Generate a message volume chart in the CRM analytics section. Display message trends over time by day or week.

### Response `200 OK`

```json
{
  "series": [
    {
      "date": "2025-01-01",
      "sent": 120,
      "received": 85
    },
    {
      "date": "2025-01-02",
      "sent": 145,
      "received": 92
    }
  ],
  "totals": {
    "sent": 15234,
    "received": 8921
  }
}
```

---

## Get Session-Specific Statistics

```http
GET /api/stats/sessions/{sessionId}
```

Returns statistics for a specific session.

| Param | Type | Description |
|-------|------|-------------|
| `sessionId` | string | Session UUID |

### CRM Use Case
Per-agent analytics — show each sales rep or support agent their message volume, response rate, and busiest hours.

### Response `200 OK`

```json
{
  "sessionId": "session-uuid",
  "sessionName": "support-line-1",
  "messagesSent": 5234,
  "messagesReceived": 3120,
  "activeChats": 45,
  "lastActive": "2025-01-01T12:00:00.000Z"
}
```
