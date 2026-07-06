# Health — Health Check Endpoints

Health endpoints are used for monitoring and orchestration (e.g., Kubernetes probes, load balancer checks, uptime monitoring). These are the only endpoints that do **not** require an API key.

---

## Basic Health Check

```http
GET /api/health
```

Returns a simple health status. Useful for basic uptime monitoring.

### Response `200 OK`

```json
{
  "status": "ok",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

---

## Liveness Probe

```http
GET /api/health/live
```

Kubernetes liveness probe. Indicates whether the application process is alive.

### Response `200 OK`

```json
{
  "status": "alive"
}
```

---

## Readiness Probe

```http
GET /api/health/ready
```

Kubernetes readiness probe. Verifies the auth/audit database and data database are responding.

### Response `200 OK`

```json
{
  "status": "ready",
  "checks": {
    "authDatabase": "connected",
    "dataDatabase": "connected"
  }
}
```

### Response `503 Service Unavailable`

```json
{
  "status": "not ready",
  "checks": {
    "authDatabase": "connected",
    "dataDatabase": "disconnected"
  }
}
```
