# Authentication - Bearer Token

Bearer Token authentication configured at both request and collection level.

## Request-Level Auth

Setting Bearer Token directly on an individual request:

Auth Type: Bearer Token
Token: {{accessToken}}

Useful when a collection mixes public and protected endpoints - each request can have different auth (or none).

## Collection-Level Auth (Inherited)

Setting Bearer Token once at the collection level, so every request inherits it automatically:

```json
"auth": {
    "type": "bearer",
    "bearer": [
      {
        "key": "token",
        "value": "{{accessToken}}",
        "type": "string"
      }
    ]
}
```

Each request in the collection is then set to **"Inherit auth from parent"** - no need to repeat the token on every request.

## Example - Authenticated Request

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("ID exists", () => {
    pm.expect(response).to.have.property("id");
    pm.expect(response.id).to.exist;
});
```

## What This Demonstrates
- Difference between request-level and collection-level authentication
- When to use each approach: collection-level for consistent auth across all requests, request-level when access varies by endpoint
- Storing tokens as environment variables instead of hardcoding them
