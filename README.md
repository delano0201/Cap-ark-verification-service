To make your GitHub documentation look professional and user-friendly, you should utilize Markdown features like syntax highlighting, badges, and clear visual hierarchies.

Here is a revamped version of your documentation that you can copy and paste directly into your README.md.
🛡️ Cap-Ark Documentation

Cap-Ark is a lightweight, mobile-friendly "path-tracing" CAPTCHA. Instead of identifying fire hydrants or crosswalks, users simply trace a generated path.
Why Cap-Ark?

    Mobile First: Designed for touchscreens and fluid mouse movements.

    Motion Analysis: Uses speed variance and path deviation to distinguish humans from bots.

    Privacy Focused: Lightweight implementation without heavy tracking scripts.

🚀 Quick Start (Embed)

Follow these two steps to integrate Cap-Ark into your web application.
1. The HTML

Place the iframe where you want the CAPTCHA challenge to appear.
HTML

<iframe 
  id="capark-captcha" 
  src="https://cap-ark.duckdns.org/gen" 
  style="width: 100%; height: 300px; border: none; border-radius: 8px;">
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
    console.log("✅ Human verified!");
    // Proceed with form submission or content unlocking
  } else {
    console.error("❌ Token invalid or expired.");
  }
});

🛠️ API Reference
Endpoint	Method	Description
/challenge	GET	Generates a new unique challenge and a randomized path.
/verify	POST	Internal. Submits coordinates. If successful, issues a one-time token.
/validate-token	POST	Public. Confirms if a token is legitimate and unused.
Validate Token Request

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

    [!IMPORTANT] One-Time Use: Once a token is validated, it is immediately destroyed to prevent replay attacks.

📜 License

This project is licensed under the MIT License. You are free to use, modify, and host this yourself.

If you use our hosted instance at cap-ark.duckdns.org, please follow fair-use principles (no spamming).

