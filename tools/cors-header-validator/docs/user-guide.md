# CORS Header Validator - User Guide

## Quick Start

1. Enter your requesting origin (e.g., https://example.com)
2. Select the HTTP method you're using
3. Add any custom headers (comma-separated)
4. Specify if you're sending credentials (cookies, auth)
5. Paste your server's response headers
6. Click "Validate"

## Understanding Results

**Critical Issues** - Must be fixed for CORS to work
**Warnings** - Potential problems or best practice violations
**Passed Checks** - Correctly configured CORS headers

## Common Scenarios

### Simple Requests
GET, HEAD, or POST with standard headers only need:
- Access-Control-Allow-Origin

### Preflight Requests
PUT, DELETE, or custom headers need:
- Access-Control-Allow-Methods
- Access-Control-Allow-Headers
- Response to OPTIONS request

### Credentials
Cookies or Authorization headers require:
- Access-Control-Allow-Credentials: true
- Specific origin (not wildcard *)

## Best Practices

- Use specific origins instead of wildcards when possible
- Set Max-Age to cache preflight responses (86400 = 24 hours)
- Only expose necessary response headers
- Validate origin against whitelist on server
