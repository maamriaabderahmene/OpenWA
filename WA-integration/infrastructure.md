# Infrastructure — Server Management

The Infrastructure API provides server-level management: status checks, engine configuration, data export/import, storage management, and server restart.

---

## Get Infrastructure Status

```http
GET /api/infra/status
```

Returns overall infrastructure status.

### CRM Use Case
Display a system health widget in the CRM admin dashboard showing server resource usage and engine status.

### Response `200 OK`

```json
{
  "server": {
    "uptime": 3600,
    "memory": {
      "used": 256,
      "total": 1024,
      "unit": "MB"
    },
    "cpu": {
      "load": 0.45,
      "cores": 4
    }
  },
  "engine": {
    "current": "BAILEYS",
    "available": ["BAILEYS"],
    "status": "running"
  },
  "sessions": {
    "total": 5,
    "connected": 3,
    "disconnected": 2
  }
}
```

---

## Get Available Engines

```http
GET /api/infra/engines
```

Returns available WhatsApp engines.

### Response `200 OK`

```json
{
  "engines": [
    {
      "name": "BAILEYS",
      "version": "6.7.0",
      "description": "Baileys WhatsApp Web API",
      "default": true
    }
  ]
}
```

---

## Get Current Engine

```http
GET /api/infra/engines/current
```

Returns the currently active engine.

### Response `200 OK`

```json
{
  "name": "BAILEYS",
  "version": "6.7.0",
  "startedAt": "2025-01-01T00:00:00.000Z"
}
```

---

## Read Infrastructure Config

```http
GET /api/infra/config
```

Reads the saved infrastructure configuration (for dashboard form population).

### Response `200 OK`

```json
{
  "engine": "BAILEYS",
  "logLevel": "info",
  "maxSessions": 100
}
```

---

## Save Infrastructure Config

```http
PUT /api/infra/config
```

Saves infrastructure configuration to the `.env` file.

### Request Body

```json
{
  "engine": "BAILEYS",
  "logLevel": "debug",
  "maxSessions": 50
}
```

### Response `200 OK`

```json
{
  "message": "Configuration saved. Restart required for some changes to take effect."
}
```

---

## Restart Server

```http
POST /api/infra/restart
```

Requests a server restart with Docker orchestration.

### CRM Use Case
After changing configuration, trigger a restart from the CRM admin panel.

### Response `200 OK`

```json
{
  "message": "Restart requested"
}
```

---

## Health Check

```http
GET /api/infra/health
```

Infrastructure-specific health check.

### Response `200 OK`

```json
{
  "status": "healthy",
  "uptime": 3600,
  "database": "connected",
  "storage": "accessible"
}
```

---

## Export Data

```http
GET /api/infra/export-data
```

Exports all data from the Data DB for migration.

### CRM Use Case
Before migrating the OpenWA instance to a new server, export all data and import on the destination.

### Response `200 OK`

(JSON payload containing all data)

---

## Import Data

```http
POST /api/infra/import-data
```

Imports data to the Data DB (replaces existing data).

### Request Body

The JSON payload from the export endpoint.

### Response `200 OK`

```json
{
  "message": "Data imported successfully",
  "records": 1500
}
```

---

## Get File Count

```http
GET /api/infra/storage/files/count
```

Returns the count of files in the current storage.

### Response `200 OK`

```json
{
  "fileCount": 342
}
```

---

## Export Storage

```http
GET /api/infra/storage/export
```

Exports all storage files as a tar.gz archive.

### Response `200 OK`

Binary tar.gz stream.

---

## Import Storage

```http
POST /api/infra/storage/import
```

Imports storage files from a tar.gz archive (multipart upload).

### Request Body

Multipart/form-data with the tar.gz file.

### Response `200 OK`

```json
{
  "message": "Storage imported successfully",
  "filesImported": 342
}
```
