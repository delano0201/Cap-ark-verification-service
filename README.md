# 🛡️ Cap-Ark

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Mobile Friendly](https://img.shields.io/badge/Mobile-Friendly-brightgreen)

**Cap-Ark** is a lightweight, mobile-friendly "path-tracing" CAPTCHA. Instead of clicking fire hydrants, users trace a generated path. It uses motion analysis and speed variance to distinguish between humans and bots.

---

## 🚀 Quick Start (Embed)

### 1. The HTML
Place the following `iframe` where you want the CAPTCHA to appear on your page.

```html
<iframe 
  id="capark-captcha" 
  src="[https://cap-ark.duckdns.org/gen](https://cap-ark.duckdns.org/gen)" 
  style="width: 100%; height: 300px; border: 2px solid #eaecef; border-radius: 12px;"
  frameborder="0">
</iframe>

2. The JavaScript

Add this script to your parent page. It performs two specific actions:

    Listens for the verification token sent from the iframe via postMessage.

    Validates that token with the Cap-Ark server to ensure it is legitimate.

JavaScript

window.addEventListener("message", async (e) => {
  if (!e.data || !e.data.capark_token) return;
  
  const token = e.data.capark_token;

  const response = await fetch("[https://cap-ark.duckdns.org/validate-token](https://cap-ark.duckdns.org/validate-token)", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ token: token })
  });

  const result = await response.json();

  if (result.valid) {
    console.log("✅ Human verified!");
  } else {
    console.error("❌ Token invalid or expired.");
  }
});

🛠️ API Reference
Endpoint	Method	Description
/challenge	GET	Generates a new unique challenge and path.
/verify	POST	Internal: Used by iframe to submit coordinates.
/validate-token	POST	Public: Confirms if a token is legitimate.
Validation Request Format

Send a POST request to the validation endpoint with the following JSON body:
JSON

{ 
  "token": "string" 
}

Validation Response Format

The server will return a boolean indicating if the verification was successful:
JSON

{ 
  "valid": true 
}

    [!IMPORTANT] One-Time Use: Once a token is validated, it is destroyed immediately to prevent replay attacks.

📜 License

This project is licensed under the MIT License. You are free to use, modify, and host this yourself.

    [!TIP] If you use our hosted instance at cap-ark.duckdns.org, please follow fair-use principles.
