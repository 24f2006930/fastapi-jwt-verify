# FastAPI RS256 JWT Verification

A FastAPI service that verifies RS256-signed JWTs using the assigned IdP public key and validates issuer, audience, and expiration claims.

## Endpoint

`POST /verify`

Request JSON:

```json
{
  "token": "<JWT string>"
}
```

A valid token returns HTTP 200:

```json
{
  "valid": true,
  "email": "claim-from-token",
  "sub": "claim-from-token",
  "aud": "claim-from-token"
}
```

Invalid, expired, tampered, wrong-issuer, or wrong-audience tokens are rejected with HTTP 401 and the JSON response body `{"valid": false}`.

## Verification rules

- Signature must validate using the supplied RSA public key.
- Only the `RS256` algorithm is accepted.
- `iss` must equal `https://idp.exam.local`.
- `aud` must equal `tds-k1o47643.apps.exam.local`.
- `exp` must be in the future.
- `exp`, `iss`, `aud`, `sub`, and `email` claims are required.

## Deploy on Render

This repository includes `render.yaml` for Render Blueprint deployment. Create a Blueprint from the repository and apply the configuration.

## Quick test

```bash
curl -i -X POST "https://YOUR-SERVICE.onrender.com/verify" \
  -H "Content-Type: application/json" \
  -d '{"token":"<JWT string>"}'
```
