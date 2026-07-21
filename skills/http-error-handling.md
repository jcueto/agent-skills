---
name: http-error-handling
description: Handles HTTP 400 Bad Request and 429 Too Many Requests errors with structured retry logic.
source: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/400, https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429
---

# http-error-handling

Handles HTTP 400 Bad Request and 429 Too Many Requests errors with structured retry logic.

## Instructions

## Overview

This skill teaches agents to handle the two most common HTTP client errors: 400 Bad Request (malformed input) and 429 Too Many Requests (rate limiting).

## Steps

1. Inspect the response status code
2. For 400: fix the request payload
3. For 429: read the Retry-After header and back off

## Parameters

| Name | Description | Required |
|------|-------------|----------|
| `retryAfterHeader` | Value of the Retry-After response header | No |

## Error Handling

| Error | Cause | Resolution |
|-------|-------|------------|
| 400 Bad Request | Malformed request payload | Fix the payload and retry |
| 429 Too Many Requests | Rate limit exceeded | Wait for Retry-After duration and retry with backoff |

### References

- [MDN: 429 Too Many Requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429)
