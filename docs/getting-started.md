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

```bash
mkdir my-bot && cd my-bot
npm init -y
npm install @kongkow/sdk express
```

Create a `.env` file:

```ini
BOT_TOKEN=your_token_from_nova
WEBHOOK_SECRET=your_secret_verify_token
PORT=3000
```

---

## 3. Write Your Bot

Create `index.js`:

```javascript
import express from "express";
import { KongkowBot } from "@kongkow/sdk";

const bot = new KongkowBot(process.env.BOT_TOKEN, {
    webhookSecret: process.env.WEBHOOK_SECRET,
});

// Handle /start command
bot.onCommand("start", async (update) => {
    await bot.sendMessage(update.message.chat.id, "Hello! 👋");
});

// Echo all other messages
bot.onMessage(async (update) => {
    const text = update.message?.text;
    if (text) {
        await bot.sendMessage(update.message.chat.id, `You said: ${text}`);
    }
});

// Webhook server
const app = express();
app.use(express.json());

app.post("/webhook", async (req, res) => {
    const secret = req.headers["x-kongkow-bot-api-secret-token"];
    await bot.handleUpdate(req.body, secret);
    res.json({ ok: true });
});

app.listen(process.env.PORT || 3000, () => console.log("Bot running!"));
```

---

## 4. Run Locally

1.  **Start the bot:**
    ```bash
    node index.js
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
You should see `Bot running!` in your terminal — and your bot will echo back!

## Next Steps
*   Read the [API Reference](./api-reference.md) for all available methods.
*   Check out [Webhooks](./webhooks.md) for full payload details.
*   Browse [Examples](https://github.com/kongkowapps/kongkow-examples) for more patterns.
