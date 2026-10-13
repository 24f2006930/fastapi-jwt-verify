# FastAPI RS256 JWT Verification

A FastAPI service that validates RS256-signed JSON Web Tokens (JWTs) using an assigned identity provider's public key. It verifies the token signature, issuer, audience, and expiration before returning the verified claims.

**Live endpoint:** https://fastapi-jwt-verify-ewgg.onrender.com/verify

## API Endpoint

### `POST /verify`

Accepts a JWT in the request body as JSON.

**Request:**

```json
{
  "token": "<JWT string>"
}
```

### Successful verification

A valid token returns HTTP `200 OK` with the verified claims:

```json
{
  "valid": true,
  "email": "claim-from-token",
  "sub": "claim-from-token",
  "aud": "claim-from-token"
}
```

The `email`, `sub`, and `aud` fields are taken from the verified token, not hard-coded.

### Failed verification

Invalid, expired, tampered, wrong-issuer, or wrong-audience tokens return HTTP `401 Unauthorized`:

```json
{
  "valid": false
}
```

## Verification Rules

- **Signature:** Must be valid against the assigned RSA public key.
- **Algorithm:** Only RS256 is accepted.
- **Issuer (`iss`):** Must equal `https://idp.exam.local`.
- **Audience (`aud`):** Must equal `tds-k1o47643.apps.exam.local`.
- **Expiration (`exp`):** Must be in the future.
- **Required claims:** `exp`, `iss`, `aud`, `sub`, and `email` must be present.

## Technology Stack

- **FastAPI** — API framework
- **PyJWT with cryptography support** — JWT decoding and signature verification
- **Uvicorn** — ASGI server
- **Render** — Cloud deployment

## Deployment

The application is deployed on Render using a Blueprint configuration in `render.yaml`.

## Testing

### 1. Test invalid-token rejection

```bash
curl -i -X POST "https://fastapi-jwt-verify-ewgg.onrender.com/verify" \
  -H "Content-Type: application/json" \
  -d '{"token":"invalid-token"}'
```

Expected: HTTP `401 Unauthorized` with:

```json
{
  "valid": false
}
```

### 2. Test a valid token

Replace `<JWT_STRING>` with a valid token issued by the mock identity provider:

```bash
curl -i -X POST "https://fastapi-jwt-verify-ewgg.onrender.com/verify" \
  -H "Content-Type: application/json" \
  -d '{"token":"<JWT_STRING>"}'
```

Expected: HTTP `200 OK` with `valid: true` and the verified claims.

## Project Structure

```text
fastapi-jwt-verify/
├── main.py
├── requirements.txt
├── render.yaml
└── README.md
```
