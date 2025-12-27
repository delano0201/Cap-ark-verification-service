# 🛡️ Cap-Ark — Path‑tracing CAPTCHA

Cap‑Ark is a lightweight, mobile‑friendly "path‑tracing" CAPTCHA: instead of clicking objects, users trace a generated path. Motion analysis, speed variance and path tolerance are used to distinguish humans from bots.

---

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Quick Start (Embed)](#quick-start-embed)
  - [1. HTML (iframe)](#1-html-iframe)
  - [2. JavaScript (postMessage listener)](#2-javascript-postmessage-listener)
- [API Reference](#api-reference)
- [Security Strictness](#security-strictness)
- [Self‑hosting & Fair Use](#self-hosting--fair-use)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

---

## Overview
Cap‑Ark provides frictionless verification for mobile and desktop users by requiring them to trace a randomized path. The client (iframe) analyses the trace and, when confident a human performed the trace, issues a one‑time Verification Token which your server validates.

---

## Features
- Mobile‑friendly path tracing UI
- Motion analysis and speed variance checks
- One‑time use verification tokens
- Lightweight and easy to embed via an iframe

---

## Quick Start (Embed)

To add Cap‑Ark to your website, paste an iframe into your page and listen for the verification token with `postMessage`. Always validate the token server‑side.

### 1. HTML (iframe)

Place the iframe where you want the CAPTCHA to appear:

```html
<iframe
  id="capark-captcha"
  src="https://cap-ark.duckdns.org/gen"
  style="width: 100%; height: 300px; border: none;">
</iframe>
```

### 2. JavaScript (postMessage listener)

The iframe will send a message containing a `capark_token` after a successful trace. Validate the token on your server using the `POST /validate-token` API.

```javascript
// Listen for messages from the iframe
window.addEventListener("message", async (e) => {
  // IMPORTANT: Verify the origin to ensure messages come from your Cap‑Ark instance
  // e.g. if your Cap‑Ark instance is hosted at https://cap-ark.duckdns.org:
  if (e.origin !== "https://cap-ark.duckdns.org") return;

  // 1. Check if the message contains a Cap‑Ark token
  if (!e.data || !e.data.capark_token) return;

  const token = e.data.capark_token;

  // 2. Send the token to your server for final validation
  const response = await fetch("/api/validate-capark-token", { // route on your server
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ token })
  });

  const result = await response.json();

  if (result.valid) {
    console.log("Human verified!");
    // Unlock your content / allow form submission
  } else {
    console.error("Token invalid or expired.");
  }
});
```

Tip: Always forward the token to your own backend endpoint and validate it against the Cap‑Ark server there (do not rely solely on client‑side checks).

---

## API Reference

- GET /challenge  
  Generates a new challenge and randomized path for a user session.

- POST /verify  
  Internal endpoint used by the iframe to submit user coordinates. If the trace meets thresholds, a one‑time verification token is issued.

- POST /validate-token  
  Used by your server to confirm a token is legitimate and unused.

  Request:
  ```json
  { "token": "string" }
  ```

  Response:
  ```json
  { "valid": true }
  // or
  { "valid": false }
  ```

  Note: Tokens are one‑time use and are destroyed upon successful validation.

Example curl (validate token):
```bash
curl -X POST "https://cap-ark.duckdns.org/validate-token" \
  -H "Content-Type: application/json" \
  -d '{"token":"<TOKEN_HERE>"}'
```

---

## Security Strictness

Cap‑Ark applies multiple heuristics to distinguish humans from bots:

- Point Density: Requires at least 80 recorded data points during the trace.
- Speed Variance: Human movement has natural variance; automated traces are often too smooth.
- Path Tolerance: Maximum allowed deviation from the generated path is 18px.
- Timing: Trace duration must be between 1.2s and 40s.
- One‑time Tokens: Verification tokens are single use and destroyed after validation.

These thresholds are designed to strike a balance between accessibility and bot resistance. If you self‑host, you can tune these parameters.

---

## Self‑hosting & Fair Use
You may self‑host Cap‑Ark and modify it under the MIT License. If you use the hosted instance at https://cap-ark.duckdns.org, please be considerate: avoid automated bulk requests and respect fair‑use limits.

---

## Contributing
Contributions, bug reports, and feature requests are welcome. Please open issues or pull requests against this repository. Include screenshots and reproduction steps for any UI or validation bugs.

---

## License
This project is licensed under the MIT License. See the LICENSE file for details.

---

## Contact
For questions or help, open an issue in this repository or contact the maintainer.
