# API Contract: [API NAME]

**Author:** [YOUR NAME]
**Date:** [YYYY-MM-DD]
**Status:** Draft | In Review | Approved | Implemented
**Consumers:** [TEAMS OR SERVICES THAT CALL THIS API]

---

## Endpoint overview

| Method | Path | Description |
|---|---|---|
| [GET] | [/api/v1/resource] | [What it returns] |
| [POST] | [/api/v1/resource] | [What it creates] |
| [PUT] | [/api/v1/resource/:id] | [What it updates] |
| [DELETE] | [/api/v1/resource/:id] | [What it removes] |

## Authentication

[How callers authenticate. API key, OAuth token, service-to-service credentials, etc.]

## Request / Response schemas

### [ENDPOINT NAME]

**Request**

```
[METHOD] [PATH]
Content-Type: application/json
Authorization: Bearer [TOKEN]
```

```json
{
  "[FIELD_1]": "[TYPE -- string, integer, etc.]",
  "[FIELD_2]": "[TYPE]",
  "[FIELD_3]": {
    "[NESTED_FIELD]": "[TYPE]"
  }
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| [FIELD_1] | [TYPE] | Yes | [DESCRIPTION] |
| [FIELD_2] | [TYPE] | No | [DESCRIPTION, DEFAULT VALUE] |

**Response (200)**

```json
{
  "id": "[TYPE]",
  "[FIELD_1]": "[TYPE]",
  "[FIELD_2]": "[TYPE]",
  "created_at": "ISO 8601 timestamp",
  "updated_at": "ISO 8601 timestamp"
}
```

### [NEXT ENDPOINT -- repeat the block above for each endpoint]

## Error responses

All errors return a consistent body format:

```json
{
  "error": {
    "code": "[ERROR_CODE]",
    "message": "[HUMAN-READABLE DESCRIPTION]",
    "details": {}
  }
}
```

| Status code | Error code | When it happens |
|---|---|---|
| 400 | `bad_request` | [DESCRIBE CONDITION] |
| 401 | `unauthorized` | Missing or invalid authentication |
| 403 | `forbidden` | Valid auth but insufficient permissions |
| 404 | `not_found` | Resource does not exist |
| 409 | `conflict` | [DESCRIBE CONDITION -- e.g., duplicate entry] |
| 422 | `validation_error` | Request body fails validation |
| 429 | `rate_limited` | Caller exceeded rate limit |
| 500 | `internal_error` | Unexpected server failure |

## Rate limiting

| Tier | Limit | Window |
|---|---|---|
| [DEFAULT] | [N requests] | [per second / per minute] |
| [AUTHENTICATED] | [N requests] | [per second / per minute] |

Rate limit headers returned on every response:

```
X-RateLimit-Limit: [MAX REQUESTS]
X-RateLimit-Remaining: [REMAINING]
X-RateLimit-Reset: [UNIX TIMESTAMP]
```

## Pagination

[Describe pagination strategy: cursor-based, offset-based, or none. Include example query parameters and response metadata.]

```json
{
  "data": [],
  "pagination": {
    "next_cursor": "[CURSOR_VALUE]",
    "has_more": true
  }
}
```

## Versioning strategy

- API version is specified via: [URL path `/v1/`, header `API-Version`, query param, etc.]
- Current version: [v1]
- Supported versions: [v1]

## Breaking change policy

A breaking change is any of the following:
- Removing or renaming a field from a response
- Changing the type of an existing field
- Adding a new required field to a request
- Changing the meaning of an existing field
- Removing an endpoint

When a breaking change is needed:
1. Release it under a new version number.
2. Support the old version for at least [N MONTHS].
3. Notify consumers at least [N WEEKS] before deprecating an old version.

## Consumer notification

| Event | Notification method | Lead time |
|---|---|---|
| New version released | [EMAIL / SLACK / CHANGELOG] | Same day |
| Version deprecation scheduled | [EMAIL / SLACK] | [N WEEKS] before removal |
| Version removed | [EMAIL / SLACK] | Day of removal |
| Unplanned downtime | [STATUS PAGE / SLACK] | As it happens |
