# API Design Document Template

## Document Header

```markdown
# {Product Name} — API Design Document

| Item | Detail |
|------|--------|
| Version | v1.0 |
| Base URL | `https://api.{domain}/v1` |
| Auth | Bearer Token (JWT) |
| Date | {date} |
```

## Section Structure

### Per-Module Sections (one per functional module)

```markdown
## Section N: {Module Name}

### N.1 {Endpoint Title}

`{METHOD} {/path/to/resource}`

**Description:** What this endpoint does.

**Auth:** `{permission_required}`

**Request:**
```json
{
  "field1": "string — description",
  "field2": 0
}
```

**Response (200):**
```json
{
  "code": 0,
  "message": "success",
  "data": { ... }
}
```

**Error Codes:**

| Code | Description |
|------|-------------|
| 40001 | Invalid parameter: field1 |
| 40401 | Resource not found |
```

### Common Sections (append after module sections)

**Pagination & Filtering Specification**
- Standard query params: `page`, `page_size`, `sort_by`, `sort_order`
- Cursor-based pagination for large datasets: `cursor`, `limit`
- Filter operators: `eq`, `neq`, `gt`, `gte`, `lt`, `lte`, `in`, `like`

**Authentication & Authorization Matrix**

| Endpoint Group | Role Required | Scope |
|---------------|---------------|-------|
| Module A | admin, operator | read, write |
| Module B | admin | read, write |
| Module C | viewer | read |

**Rate Limiting Policy**
- Default: {N} requests/minute per API key
- Burst: {N} requests/second
- Rate limit headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`

**Webhook Events**

| Event | Trigger | Payload Key Fields |
|-------|---------|-------------------|
| `{event.name}` | Description | field1, field2 |

**Error Code Catalog**

| Code Range | Category |
|-----------|----------|
| 40000-40099 | Parameter validation errors |
| 40100-40199 | Authentication errors |
| 40300-40399 | Authorization errors |
| 40400-40499 | Resource not found |
| 40900-40999 | Conflict / duplicate |
| 50000-50099 | Internal server errors |

**Appendix: Endpoint Index**

| # | Method | Path | Section | Auth |
|---|--------|------|---------|------|
| 1 | POST | /resource | S1.1 | admin |

## Formatting Rules

- Use JSON code blocks for all request/response schemas
- Include realistic example values (not just type placeholders)
- Document every field in request/response with inline comments
- Error codes must be specific (not generic 400/500 descriptions)
- Target: 30-50 lines per endpoint (including schemas)
