# Webhooks

Webhooks are how Kongkow notifies your bot of new events (messages, commands, payments).

## Configuration

You must set your webhook URL using `@nova`:
`/set_webhook @<bot_username> <your_https_url>`

## Security

To prevent spoofing, Kongkow sends a secret token in the headers if you configured one.

**Header:**
`x-kongkow-bot-secret`

**Verification Code (Node.js):**
```javascript
const secret = req.headers["x-kongkow-bot-secret"];
if (secret !== process.env.WEBHOOK_SECRET) {
  // Reject request
  return res.status(403).send("Unauthorized");
}
```

---

## Event Payloads

### 1. Message Event
Received when a user sends a message to your bot.

```json
{
  "update_id": 1024,
  "message": {
    "message_id": "msg_123",
    "from": "username_of_sender",
    "chat": {
      "id": "chat_123",
      "type": "private"
    },
    "text": "/start",
    "timestamp": "2024-01-01T12:00:00Z"
  }
}
```

### 2. Payment Event (Coming Soon)
Received when a user pays for your PPV post or tips you.

```json
{
  "update_id": 1025,
  "payment": {
    "amount": 0.1,
    "currency": "SOL",
    "from": "rich_user",
    "reference": "post_555"
  }
}
```
