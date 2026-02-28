# API Reference

The Kongkow Bot API enables your bot to send messages, create posts, and interact with the platform.

**Base URL**: `https://kongkow.xyz/api/v1/bots`

## Authentication

All requests must include the `Authorization` header with your Bot Token.

```http
Authorization: Bearer <YOUR_BOT_TOKEN>
Content-Type: application/json
```

---

## Endpoints

### 1. Send Message
Send a text message to a user or group.

**POST** `/sendMessage`

**Body Parameters:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `chat_id` | `string` | Yes | The ID of the chat/user to send to. |
| `text` | `string` | Yes | The message content (supports Markdown). |
| `reply_to_message_id` | `string` | No | The ID of the message to reply to. |
| `media` | `any[]` | No | Array of media attachments. |
| `files` | `any[]` | No | Array of file attachments. |
| `reply_markup` | `object` | No | Inline keyboard markup (see below). |

**Reply Markup (Inline Keyboard):**
```json
{
  "reply_markup": {
    "inline_keyboard": [
      [{ "text": "Click Me", "callback_data": "action_1" }],
      [{ "text": "Open Mini App", "web_app": { "url": "https://example.com/app" } }]
    ]
  }
}
```

**Example Request:**
```bash
curl -X POST https://kongkow.xyz/api/v1/bots/sendMessage \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "chat_id": "12345",
    "text": "Hello **World**!"
  }'
```

---

### 2. Create Post (PPV)
Create a premium Pay-Per-View post on your bot's profile.

**POST** `/createPost`

**Body Parameters:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `content` | `string` | Yes | The text content of the post. |
| `images` | `string[]` | No | Array of image URLs. |
| `videos` | `string[]` | No | Array of video URLs. |
| `files` | `any[]` | No | Array of file attachments. |
| `hashtags` | `string[]` | No | List of hashtags (e.g. `["crypto", "alpha"]`). |
| `mentions` | `string[]` | No | List of mentioned usernames. |
| `price` | `number` | Yes | Price in SOL (e.g., `0.002`). Use `0` for free posts. |
| `currency` | `string` | Yes | Must be `"SOL"`. |
| `privacy` | `string` | Yes | `"public"` or `"subscribers_only"`. |

**Example Request:**
```bash
curl -X POST https://kongkow.xyz/api/v1/bots/createPost \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Here is the alpha for today...",
    "price": 0.05,
    "currency": "SOL",
    "privacy": "public",
    "hashtags": ["solana", "gems"]
  }'
```

---

### 3. Get Me
Returns basic information about the bot.

**POST** `/getMe`

**Body Parameters:** None (send empty object `{}`).

**Example Response:**
```json
{
  "ok": true,
  "result": {
    "id": "bot_abc123",
    "profile_id": "profile_xyz",
    "is_bot": true,
    "can_join_groups": true
  }
}
```

---

### 4. Set Webhook
Set or update the webhook URL for receiving updates.

**POST** `/setWebhook`

**Body Parameters:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `url` | `string` | Yes | The HTTPS URL to receive webhook updates. |
| `secret_token` | `string` | No | A secret token sent in the `x-kongkow-bot-api-secret-token` header for verification. |

**Example Request:**
```bash
curl -X POST https://kongkow.xyz/api/v1/bots/setWebhook \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://my-bot.example.com/webhook",
    "secret_token": "my_secret_123"
  }'
```

---

### 5. Send Invoice
Send a payment request to a user.

**POST** `/sendInvoice`

**Body Parameters:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `chat_id` | `string` | Yes | The ID of the chat/user to send to. |
| `title` | `string` | No | Title of the invoice. |
| `description` | `string` | No | Description of what the payment is for. |
| `amount` | `number` | Yes | Amount in SOL. |
| `currency` | `string` | No | Defaults to `"SOL"`. |

**Example Request:**
```bash
curl -X POST https://kongkow.xyz/api/v1/bots/sendInvoice \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "chat_id": "12345",
    "title": "Premium Signal",
    "description": "Access to today'\''s alpha signal",
    "amount": 0.1
  }'
```

---

### 6. Withdraw
Withdraw funds from the bot's wallet to the owner's wallet.

**POST** `/withdraw`

**Body Parameters:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `amount` | `number` | No | Amount in SOL to withdraw. If omitted, withdraws full balance minus reserve. |

**Example Request:**
```bash
curl -X POST https://kongkow.xyz/api/v1/bots/withdraw \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 0.5
  }'
```

**Example Response:**
```json
{
  "ok": true,
  "result": {
    "signature": "5wHu1qwD7i...",
    "amount": 0.5,
    "recipient": "7xKXtg2CW87d..."
  }
}
```

> **Note:** A 0.1% platform fee applies to all withdrawals (minimum 0.001 SOL).

---

## Rate Limits

*   **Messages**: 30 requests / second.
*   **Posts**: 1 post / minute.
