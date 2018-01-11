# Orders

Orders api provides the end points for retreiving and manipulating orders after they have been added to leasecloud via the checkout api.

The url to staging is https://api.staging.leasecloud.com

```http
GET /v2/orders HTTP/1.1
Content-Type: application/json
Authorization: Bearer [bearer token]
```

<div class="move-right">
  <h3>Response body</h3>
</div>

```json
{
  "id": "c1f0acd8-8e3e-4477-85d3-18337c3c92d6",
  "orgNumber": "5590894308",
  "status": "PENDING"
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
      "name": "MacBook Pro 15",
      "productId": "13",
      "articleNr": "NCC-5347XF",
      "quantity": 1,
      "unitAmount": 3400000,
      "totalAmount": 3400000,
      "VAT": 0
    },
    {
      "name": "iPhone X",
      "productId": "233",
      "articleNr": "ABC-5347XF",
      "quantity": 2,
      "unitAmount": 1200000,
      "totalAmount": 2400000,
      "VAT": 0
    }
  ],
  "createdAt": "2018-01-03T15:10:00.000Z",
  "updatedAt": "2018-01-03T15:10:00.000Z"
}
```
## Retrieve an order

To retrieve a order after checkout has been completed make a `GET` request `/v2/orders/{orderId}`

An order information will be returned, including it's current status. See example on right.


### Order statuses

Status | Description
------ | -----------
PENDING | The order has been received but the customer has not signed or the leasing company has not yet authorized the transaction. The order should not be shipped
OK | All requirements have been forfilled and the order can be shipped
DONE | The order has been forfilled and the contract started, changes are no longer possible by the partner
CANCELLED | The order has been cancelled

## Cancel an order

```http
POST /v2/orders/{orderId}/cancel HTTP/1.1
Authorization: Bearer [bearer token]
```

To cancel an order make a `POST` request `/v2/orders/{orderId}/cancel`
which will respond with 200 and an empty body

An order can be canceled until the user has signed the order


## Order shipped

```http
POST /v2/orders/{orderId}/shipped HTTP/1.1
Authorization: Bearer [bearer token]
```

<div class="move-right">
  <h3>(Optional) Request body example</h3>
</div>

```json
{
  "shippedAt": "2017-07-20T13:37:00.000Z",
  "trackingLink": "https://some-postal.com/kolli/124258652346",
	"trackingNr": "124258652346"
}
```

Make a `POST` request to `/v2/orders/{orderId}/shipped` when an order has been shipped.
When the request is received we will set the current time as the shipped date
and time but you can send the body `shippedAt` and ISO-8601 date to override the date.

Parameter | Type        | Required | Description
--------- | ----------- | -------- | -----------
shippedAt | string | | Date and time shipped ISO-8601
trackingLink | string | | A link to the postal companies tracking page for this shippment
trackingNr | string | | The tracking number for the package

## Order serial numbers

```http
POST /v2/orders/{orderId}/serial-numbers HTTP/1.1
Authorization: Bearer [bearer token]
```

<div class="move-right">
  <h3>Request body example</h3>
</div>

```json
{
  "serials": [{
		"productId": "13",
		"description": "MacBook Pro 15",
		"serialNumber": "C02SP3SRKJKNFVFB23"
	}]
}
```

Make a `POST` request to `/v2/orders/{orderId}/serial-numbers` when the serial numbers are known.

Parameter | Type        | Required | Description
--------- | ----------- | -------- | -----------
serials | array | ✔︎ | An array of one or more serials
 • productId | string | ✔︎ | Product id
 • description | string | ✔︎ | Product description
 • serialNumber | string | ✔︎ | Product serial number



