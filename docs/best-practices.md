# Best Practices

## 1. Handle Duplicates (Idempotency)
Kongkow guarantees "at-least-once" delivery for webhooks. In rare cases (like network retries), you might receive the same `update_id` twice.

**Recommendation:**
Keep track of processed `update_id`s in a database or Redis cache.

```javascript
if (await db.hasProcessed(update.update_id)) {
  return res.json({ ok: true }); // Acknowledge but skip
}
await db.markProcessed(update.update_id);
```

## 2. Respond Quickly
Your webhook endpoint should respond with `200 OK` immediately (under 3 seconds). If you need to do heavy processing (AI generation, blockchain indexing), move it to a background queue.

**Pattern:**
1.  Receive Webhook.
2.  Validate Secret.
3.  Push to Queue (e.g., RabbitMQ, BullMQ).
4.  Respond `200 OK`.
5.  Worker processes the job and calls `sendMessage`.

## 3. Financial Safety
Since your bot is "Born with a Wallet":
*   Never expose your Private Key (managed by Kongkow, you typically don't see it, but handle your API tokens with same care).
*   Use the SDK's `withdraw()` method for withdrawals rather than trying to construct raw transactions unless necessary.

## 4. User Privacy
*   Do not store user messages longer than necessary.
*   Respect the `privacy` flag on posts.
