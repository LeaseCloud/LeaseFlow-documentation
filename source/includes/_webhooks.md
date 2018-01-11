# Webhooks

```http
POST https://example.com/my/callback/ HTTP/1.1
LeaseCloud-Version: 1.0.0
LeaseCloud-Signature: t=1499785732,v1=[HMAC_SHA-256_PAYLOAD],v1=[HMAC_SHA-256_PAYLOAD_OLD]
```

```json
{
  "id": "UNIQUE_EVENT_ID"
  "action": "NAME_OF_ACTION",
  "data": {
    "FIELDS": "FOR ACTION"
  }
}
```

```php
<?php hash_hmac("sha256", "${TIMESTAMP}.${PAYLOAD}", WEBHOOK_SECRET);
```
When becoming a partner we will ask for a url endpoint where we will make post requests to with updates.
Webhooks are used to communicate events from leasecloud back to the partners site. They always contain the same datastructure.

We will make a POST request to the endpoint with a JSON body

The signature is generated with the timestamp found in the LeaseCloud-Signature header combined with a . and the payload.

## Order status updated
<div class="move-right">
  <h3>Response body</h3>
</div>

```json
{
  "id": "evt:e1f0aed8-883a-9751-74a3-48537d3d94ce",
  "action": "order.created",
  "data": {
    "id": "c1f0acd8-8e3e-4477-85d3-18337c3c92d6",
    "orgNumber": "5590894308",
    "status": "PENDING",
    "totalAmount": 23323,
    "VAT": 32,
    "shippingAmount": 0,
    "shippingVAT": 0,
    "currency": "SEK",
    "months": 24,
    "billing": {
      "firstName": "Ora",
      "lastName": "Roob",
      "email": "Myrtice70@yahoo.com",
      "phone": "(719) 365-7934 x29126",
      "company": "Langosh, Kautzer and Ullrich",
      "address": "1532 Edna Spring",
      "address2": "",
      "city": "East Guidofort",
      "state": "",
      "postalCode": "17875",
      "country": "SE"
    },
    "shipping": {
      "firstName": "Brigitte",
      "lastName": "Wunsch",
      "email": "Jon60@gmail.com",
      "phone": "803-546-0329",
      "company": "Volkman - Goodwin",
      "address": "46995 Sandra Green",
      "address2": "",
      "city": "South Aileenfurt",
      "state": "",
      "postalCode": "47920",
      "country": "SE"
    },
    "items": [
      {
        "name": "Towels",
        "productId": "23",
        "articleNr": "NCC-5347XF",
        "quantity": 42,
        "unitAmount": 34,
        "totalAmount": 8400,
        "VAT": 0
      },
      {
        "name": "NOPE",
        "productId": "233",
        "articleNr": "NCC-5347XF",
        "quantity": 42,
        "unitAmount": 34,
        "totalAmount": 8400,
        "VAT": 0
      }
    ],
    "createdAt": "2018-01-03T15:10:00.000Z",
    "updatedAt": "2018-01-03T15:10:00.000Z"
  }
}
```

We will make a `POST` request to the notification WebHook endpoint provided in the initial checkout request every time the order status is updated.

The signature is generated with the timestamp found in the `LeaseCloud-Signature` header combined with a `.` and the `payload`

An example of the request body is provided on the right

The webhook will be repeatedly called until the partner acknowledges receipt. Acknowledgement is achieved by the store replying to the webhook with a 200 response, and a json object containing the leasecloud orderId and optionally the partners id for the order.

### Actions
 * order.created
 * order.statusUpdated
