# Webhooks

```http
POST https://example.com/my/callback/ HTTP/1.1
LeaseCloud-Version: 1.0.0
LeaseCloud-Signature: t=1499785732,v1=[HMAC_SHA-256_PAYLOAD],v1=[HMAC_SHA-256_PAYLOAD_OLD]

{
  "id": "UNIQUE_EVENT_ID"
  "action": "NAME_OF_ACTION",
  "data": {
    "FIELDS": "FOR ACTION"
  }
}
```

/mock/:id/webhook

```php
<?php hash_hmac("sha256", "${TIMESTAMP}.${PAYLOAD}", WEBHOOK_SECRET);
```

## Order status updated

We will make a `POST` request to the endpoint provided in the initial checkout request every time the order status is updated.

The signature is generated with the timestamp found in the `LeaseCloud-Signature` header combined with a `.` and the `payload`

The webhook will be repeatedly called until the partner acknowledges receipt. Acknowledgement is achieved by the store replying to the webhook with a 200 response, and a json object containing the leasecloud orderid and optionally the partners id for the order.
