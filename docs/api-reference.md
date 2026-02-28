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
| `hashtags` | `string[]` | No | List of hashtags (e.g. `["#crypto", "#alpha"]`). |
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
    "hashtags": ["#solana", "#gems"]
  }'
```

---

## Rate Limits

*   **Messages**: 30 requests / second.
*   **Posts**: 1 post / minute.
