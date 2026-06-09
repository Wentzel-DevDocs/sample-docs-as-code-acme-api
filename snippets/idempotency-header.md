Always send a unique key on create/purchase:

```http
Idempotency-Key: 8f3c2a1e-9b4d-4c7a-a1f0-12ab34cd56ef
```

Retries with the same key within 24 hours return the original response body and status.
