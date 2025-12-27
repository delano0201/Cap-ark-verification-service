# 🛡️ Cap-Ark — Mobile-first Trace CAPTCHA

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Mobile Friendly](https://img.shields.io/badge/Mobile-Friendly-brightgreen)](https://github.com/delano0201/Cap-ark-verification-service)

Cap-Ark is a lightweight, mobile-first path-tracing CAPTCHA designed to be easy to embed, resilient to bots, and friendly for touch devices. Instead of clicking images, users trace a generated path; the server scores motion, timing, and coverage and issues one-time verification tokens for successful human checks.



---

## Table of Contents

- [About](#about)
- [Live Demo / Embed (Quick Start)](#live-demo--embed-quick-start)
- [API Reference](#api-reference)
  - [/challenge](#challenge)
  - [/verify](#verify)
  - [/validate-token](#validate-token)
- [Verification Logic](#verification-logic)
- [Token Lifecycle](#token-lifecycle)
- [License](#license)

---

> ## About
> Cap-Ark generates short path-tracing challenges optimized for mobile and desktop. The verification focuses on human-like motion characteristics: path proximity, coverage, timing, and speed variability. Tokens are signed, one-time use, and short lived for maximum security.

---

> ## Live Demo / Embed (Quick Start)
> Embed Cap-Ark with a single iframe and listen for a token via postMessage.
>
> Example iframe (responsive container recommended):
>
> ```html
> <iframe
>   id="capark-captcha"
>   src="https://cap-ark.duckdns.org/gen"
>   style="width:100%;height:320px;border:2px solid #e9e9ef;border-radius:12px;"
>   frameborder="0"
>   title="Cap-Ark CAPTCHA">
> </iframe>
> ```
>
> Parent page example: validate the token sent from the iframe
>
> ```js
> window.addEventListener("message", async (e) => {
>   if (!e.data || !e.data.capark_token) return;
>
>   const resp = await fetch("https://cap-ark.duckdns.org/validate-token", {
>     method: "POST",
>     headers: { "Content-Type": "application/json" },
>     body: JSON.stringify({ token: e.data.capark_token })
>   });
>
>   const result = await resp.json();
>   if (result.valid) {
>     // Human verified
>   } else {
>     // Verification failed
>   }
> });
> ```

---

> ## API Reference
> All responses use JSON. Use HTTPS in production.

### GET /challenge
Generates a new tracing challenge.

Response (200):
```json
{
  "challenge_id": "string",
  "path": [
    { "x": 40, "y": 100 },
    { "x": 90, "y": 120 }
  ],
  "expires_at": "2025-12-27T12:00:00Z"
}
```

### POST /verify
Submit recorded trace points for scoring.

Request:
```json
{
  "challenge_id": "string",
  "points": [
    { "x": 120.5, "y": 98.2, "t": 1532.4 },
    { "x": 121.8, "y": 99.1, "t": 1548.7 }
  ]
}
```

Success Response:
```json
{
  "success": true,
  "token": "signed_one_time_token",
  "score": 0.82
}
```

Failure Response:
```json
{
  "success": false,
  "score": 0.31,
  "reason": "low_coverage"
}
```

### POST /validate-token
Confirm that a token is valid (one-time validation).

Request:
```json
{ "token": "signed_one_time_token" }
```

Response:
```json
{ "valid": true }
```

---

> ## Verification Logic
> Each trace is scored [0.0 — 1.0]. Acceptance is controlled by a threshold.
>
> Key checks:
> - Minimum number of recorded points
> - Total trace duration within allowed bounds
> - Proximity of points to generated path (tolerance radius)
> - Coverage of the full path (not just a segment)
> - Natural speed variation (not perfectly uniform movement)
> - Final score meets the configured acceptance threshold

---

> ## Token Lifecycle
> Tokens are intentionally short-lived and single-use to prevent replay attacks.
>
> - Issued after successful verification
> - Valid for a limited time window (configurable)
> - Validated once; on successful validation the token is invalidated/deleted
> - Tokens are signed to prevent tampering

---

> ## License
> MIT License — see LICENSE file.
