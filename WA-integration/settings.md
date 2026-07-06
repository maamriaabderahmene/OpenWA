# Settings — Application Settings

Settings are read-only at runtime and are derived from environment variables. Use this endpoint to view the current runtime configuration of your OpenWA instance.

---

## Get Application Settings

```http
GET /api/settings
```

Returns all application settings.

### CRM Use Case
When diagnosing integration issues, check the OpenWA settings to verify configuration (e.g., whether Swagger is enabled, what engine is in use, rate limits, etc.).

### Response `200 OK`

```json
{
  "nodeEnv": "production",
  "port": 2785,
  "swaggerEnabled": false,
  "engine": "BAILEYS",
  "logLevel": "info",
  "maxSessions": 100,
  "rateLimit": {
    "windowMs": 60000,
    "maxRequests": 100
  }
}
```

Note: Available settings fields depend on the current environment configuration.
