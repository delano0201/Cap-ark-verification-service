# 🛡️ Cap-Ark

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Mobile Friendly](https://img.shields.io/badge/Mobile-Friendly-brightgreen)

**Cap-Ark** is a lightweight, mobile-friendly "path-tracing" CAPTCHA. Instead of clicking fire hydrants, users trace a generated path. It uses motion analysis and speed variance to distinguish between humans and bots.

---

## 🚀 Quick Start (Embed)

To add Cap-Ark to your website, copy and paste the following implementation.

### 1. The HTML
Place the iframe where you want the CAPTCHA to appear.

```html
<iframe 
  id="capark-captcha" 
  src="[https://cap-ark.duckdns.org/gen](https://cap-ark.duckdns.org/gen)" 
  style="width: 100%; height: 300px; border: 2px solid #eaecef; border-radius: 12px;"
  frameborder="0">
</iframe>

2. The JavaScript

The Cap-Ark iframe communicates with your parent page using postMessage. You must listen for the token and then validate it with the Cap-Ark server.
JavaScript

window.addEventListener("message", async (e) => {
  // 1. Check if the message contains a Cap-Ark token
  if (!e.data || !e.data.capark_token) return;
  
  const token = e.data.capark_token;

  // 2. Send the token to the Cap-Ark API for final validation
  const response = await fetch("[https://cap-ark.duckdns.org/validate-token](https://cap-ark.duckdns.org/validate-token)", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ token: token })
  });

  const result = await response.json();

  if (result.valid) {
    console.log("✅ Human verified!");
    // Logic to unlock your content here
  } else {
    console.error("❌ Token invalid or expired.");
  }
});

🛠️ API Reference
Endpoint	Method	Description
/challenge	GET	Generates a new unique challenge and a randomized path for the user to follow.
/verify	POST	Internal: Used by the iframe to submit user coordinates. If the score meets requirements, it issues a one-time Verification Token.
/validate-token	POST	Public: Used by the developer's server/frontend to confirm the token is legitimate.
Token Validation Details

POST https://cap-ark.duckdns.org/validate-token

Request Body:
JSON

{ 
  "token": "string" 
}

Response:
JSON

{ 
  "valid": true 
}

    [!NOTE] Security: Once a token is validated, it is destroyed immediately (one-time use only).

📜 License

This project is licensed under the MIT License. You are free to use, modify, and host this yourself.

If you use our hosted instance at cap-ark.duckdns.org, please follow fair-use principles (no spamming).
