# CORS Header Validator - Developer Guide

## Features

1. **Request Configuration**: Simulates client-side CORS request
2. **Response Validation**: Checks server CORS headers against request
3. **Scenario Library**: Common CORS patterns and solutions
4. **Real-time Feedback**: Instant validation with detailed messages
5. **Security Checks**: Validates credentials + wildcard combinations

## Validation Logic

### Origin Check
- Compares request origin with Allow-Origin header
- Flags wildcard (*) with credentials
- Handles specific origin matching

### Method Check
- Validates Allow-Methods for non-simple requests
- Simple methods (GET, HEAD, POST) don't require preflight

### Headers Check
- Validates custom headers against Allow-Headers
- Excludes CORS-safe headers from validation
- Case-insensitive matching

### Credentials Check
- Ensures Allow-Credentials: true when needed
- Prevents wildcard origin with credentials

### Additional Checks
- Max-Age recommendations for preflight caching
- Expose-Headers for custom response headers

## Extending

Add new scenarios to `commonScenarios` array:

```javascript
{
  title: "Scenario Name",
  solution: "Solution description",
  example: "Header: value"
}
```

## Technical Notes

- Pure client-side validation (no network requests)
- Parses header text format
- Case-insensitive header name matching
- Supports all standard CORS headers
