# Getting Started with Kongkow Bots

This guide will take you from zero to a deployed bot on the Kongkow platform.

## Prerequisites

*   **Node.js** (v18 or later)
*   **A public URL** (We recommend [ngrok](https://ngrok.com/) for local development)
*   **Kongkow User Account** (to use `@nova` for bot creation)

---

## 1. Create Your Bot

All bots on Kongkow are created via **@nova**, the Father of Bots.

1.  Open Kongkow and chat with `@nova`.
2.  Send the command: `/create_bot`.
3.  Follow the prompts:
    *   **Name**: Uniquely identifies your bot (e.g., `pizza_bot`).
    *   **Description**: What your bot does.
4.  **Save your Token!** `@nova` will give you a `BOT_TOKEN`. Keep this safe.

> **Note:** Your bot is automatically "born with a wallet" on Solana. You don't need to generate keys manually.

---

## 2. Set Up Your Project

You can start with our template or from scratch. We'll use the template for speed.

```bash
# Clone the examples repo
git clone https://github.com/kongkowapps/kongkow-open.git
cd kongkow-open/kongkow-examples/echo-bot

# Install dependencies
npm install

# Set up environment
cp .env.example .env
```

Edit the `.env` file with your credentials:

```ini
PORT=3000
BOT_TOKEN=your_token_from_nova
WEBHOOK_SECRET=your_secret_verify_token
KONGKOW_API_URL=https://kongkow.xyz/api/v1/bots
```

---

## 3. Understand the Code

A minimal bot server looks like this (using Express):

```typescript
import express from "express";

const app = express();
app.use(express.json());

// Handle Webhooks from Kongkow
app.post("/webhook", async (req, res) => {
  // 1. Validate Secret
  if (req.headers["x-kongkow-bot-secret"] !== process.env.WEBHOOK_SECRET) {
    return res.status(403).json({ error: "Unauthorized" });
  }

  // 2. Process Update
  const { message } = req.body;
  if (message && message.text) {
    console.log(`Received: ${message.text}`);
    // Respond logic here...
  }

  res.json({ ok: true });
});

app.listen(3000, () => console.log("Bot running on port 3000"));
```

---

## 4. Run Locally

1.  **Start the bot:**
    ```bash
    npm run dev
    ```

2.  **Expose to the internet:**
    ```bash
    ngrok http 3000
    ```
    Copy the https URL (e.g., `https://1234-56-78.ngrok-free.app`).

3.  **Register your Webhook:**
    Go back to `@nova` and set your webhook URL:
    ```
    /set_webhook @your_bot_name https://1234-56-78.ngrok-free.app/webhook
    ```

---

## 5. Test It!

Send a message to your bot in Kongkow.
You should see the log in your terminal!

```
[info] Received: Hello Bot!
```

## Next Steps
*   Read the [API Reference](./api-reference.md) to learn how to reply.
*   Check out [Webhooks](./webhooks.md) for full payload details.
