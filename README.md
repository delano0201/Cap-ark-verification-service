 Cap-Ark Documentation

Cap-Ark is a lightweight, mobile-friendly "path-tracing" CAPTCHA. Instead of clicking fire hydrants, users trace a generated path. It uses motion analysis and speed variance to distinguish between humans and bots.
 Quick Start (Embed)

To add Cap-Ark to your website, copy and paste the following implementation.
1. The HTML

Place the iframe where you want the CAPTCHA to appear.
HTML

<iframe 
  id="capark-captcha" 
  src="https://cap-ark.duckdns.org/gen" 
  style="width: 100%; height: 300px; border: none;">
</iframe>

2. The JavaScript

The Cap-Ark iframe communicates with your parent page using postMessage. You must listen for the token and then validate it with the Cap-Ark server.
JavaScript

window.addEventListener("message", async (e) => {
  // 1. Check if the message contains a Cap-Ark token
  if (!e.data || !e.data.capark_token) return;

  const token = e.data.capark_token;

  // 2. Send the token to the Cap-Ark API for final validation
  const response = await fetch("https://cap-ark.duckdns.org/validate-token", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ token: token })
  });

  const result = await response.json();

  if (result.valid) {
    console.log("Human verified!");
    // Logic to unlock your content here
  } else {
    console.error("Token invalid or expired.");
  }
});

   API Reference
GET /challenge

Generates a new unique challenge and a randomized path for the user to follow.
POST /verify

Internal endpoint used by the iframe to submit user coordinates. If the score meets the requirements, it issues a one-time-use Verification Token.
POST /validate-token

Used by the developer's server/frontend to confirm the token is legitimate.

    Body: { "token": "string" }

    Response: { "valid": true } or { "valid": false }

    Note: Once a token is validated, it is destroyed (one-time use only).

📜 License

This project is licensed under the MIT License. You are free to use, modify, and host this yourself. If you use our hosted instance at cap-ark.duckdns.org, please follow fair-use principles (no spamming).
