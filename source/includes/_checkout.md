#Checkout
## Introduction

LeaseCloud checkout is designed to be used either as a complete checkout solution for leasing only sites or as a payment provider within an existing checkout-flow for sites that wish to provide other payment methods.

The following description describes the initial implementation that will be checkout only.

The url to staging is https://checkout.staging.leasecloud.com
The production url is https://checkout.leasecloud.com

## Partner site setup

The following items need to be setup by the partner in order to integrate with the LeaseCloud checkout.

Page | Description
------ | -----------
Checkout page | A URL to the page used to embed the LeaseCloud checkout. Includes a div that will be populated with HTML code received from the LeaseCloud checkout api.
Confirmation page | A URL to the page the LeaseCloud checkout will redirect to when the checkout process completes. `leasecloud_order_id={ID}` will be added to the url so the order can be fetched and created
Terms and conditions page | A URL to the partners terms and conditions that any customer agrees to by purchasing via their site

## Starting the checkout process

Before starting the checkout process the customer must have selected one or more items they wish to buy. The partners site may then trigger a checkout by making a http request to the [checkout API entry point](#checkout-api-entry-point).
The LeaseCloud orderId should be saved by the partner and connected to the checkout (to allow the order to be updated before checkout completion) and the partners checkout page should be displayed with the html code snippet received from the entry point embedded in it.

## Completing the checkout process

Once the LeaseCloud checkout process has been successfully completed the customer will be redirected to the confirmation url sent during the initial checkout request.
The redirect will also include the GET query parameter `leasecloud_order_id` containing the orderId.

The partner can then retrieve the completed checkout information via a request to the [order API](#retrieve-an-order). Note that when the checkout is complete the item may still not be ready to send, the status of the order retrieved is provided in the response and the order should only be dispatched to the customer when the status is OK.

The information will also be synced back to the shop via the notification webhook sent in the initial request to the checkout API entry point.

## Updating a checkout process

If the customer does not fulfil the checkout process and returns to the shop to modify items or shipping information. The partners site can update the order on the LeaseCloud checkout by performing a http request containing the new information to the  LeaseCloud [checkout API update end point](#checkout-api-update-end-point).

## Checkout API entry point

```http
POST /v1/orders HTTP/1.1
Content-Type: application/json
Authorization: Bearer [bearer token]
```

<div class="move-right">
  <h3>Request body example</h3>
</div>

```json
{
  "totalAmount": 12000,
  "shippingAmount": 0,
  "shippingVAT": 0,
  "currency": "SEK",
  "locale": "sv-se",
  "country": "SE",
  "VAT": 0,
  "months": 24,
  "items":
  [
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
      "articleNr": "ABC-53RF7XF",
      "quantity": 2,
      "unitAmount": 1200000,
      "totalAmount": 2400000,
      "VAT": 0
    }
  ],
  "partner": {
    "name": "Permånad",
    "domain": "permanad.se",
    "urls": {
      "terms": "https://permanad.se/terms",
      "checkout": "https://permanad.se/checkout",
      "confirmation": "https://permanad.se/yay/you/did/it"
    }
  },
  "reseller": {
    "id": "some-id-123",
    "name": "Store name AB"
  }
}
```

<div class="move-right">
  <h3>Response body</h3>
</div>

```json
{
  "html": "<div id=’leasecloud_leaseflow_container’>
        <script>(function() {
          // Some script to load the iframe into the page
          var conf = {
            orderID: '1293u924972439'
          }
          createElement(script)
        })()</script>
        <noscript>Please <a href=’https://enable-javascript.com/’>enable javascript</a>.</noscript>
      </div>",
  "orderId": "1293u924972439"
}
```

<div class="move-right">
  <h3>Error response body</h3>
</div>

```json
{
  "error": {
    "code": "InvalidOrder",
    "message": "Validation error",
    "fields": [
      {
        "title": "notNull Violation",
        "message": "totalAmount cannot be null",
        "field": "totalAmount"
      },
    ]
  }
}
```

### HTTP Request

`POST /v1/orders`

### HTTP Body

The body of the http request contains the order information.

Parameter | Type        | Required | Description
--------- | ----------- | -------- | -----------
partner | object | ✔︎ | An object containing information relevant to the partner
 • name | string | ✔︎ | The display name to be used for the partner in the checkout
 • domain | string | ✔︎ | The name of the top domain that will be hosting the checkout
 • urls | object | ✔︎ | An object containing urls to the relevant partner pages
 &nbsp; • terms | string | ✔︎ | The terms and conditions url
 &nbsp; • checkout | string | ✔︎ | The checkout page url
 &nbsp; • confirmation | string | ✔︎ | The confirmation page url
reseller | object | | An object containing information relevant to the reseller
 • id | string | | A unique id to the reseller, used later to fetch more info
 • name | string | | The display name to be used in the checkout
totalAmount | integer | ✔︎ | Total amount the customer will pay Ex. VAT <br> Minimum 6000 * 100
VAT | integer | ✔︎ | Total VAT amount
shippingAmount | integer | ✔︎ | The shipping cost (excluding VAT)
shippingVAT | integer | ✔︎ | The shipping cost VAT
currency | enum | ✔︎ | In which currency is the amounts. We only support `SEK` at the moment
locale | enum | ✔︎ | Purchase language string compliant with RFC 1766
country | enum | ✔︎ | The purchase country 2 letter code e.g. SE
months | integer | | The leasing period in months, if known
items | Item object | ✔︎ | See below

### Item object

The items object contains an array of items.

#### Bundled items

When items are part of a bundle, a bundle id should be created for the bundle and the bundle should be added to the items array as if it was a normal item. The items that make up the bundle should also be added to the array but with a 0 for toal cost and a bundle id attribute linking to the bundle.

Parameter | Type        | Required | Description
--------- | ----------- | -------- | -----------
name | string | ✔︎ | Name / title of the product
productId | string | ✔︎ | Product id
articleNr | string | | Article number
bundleId | string | | If the item is part of a bundle or is the bundle add the id
quantity | integer | ✔︎ | Quantity
unitAmount | integer | ✔︎ | Price for a single unit ex VAT
totalAmount | integer | ✔︎ | Total amount (quantity * unitAmount) ex VAT
VAT | integer | ✔︎ | VAT for the total amount

### HTTP Response

Parameter | Type        | Description
--------- | ----------- | -----------
html | string | The HTML snippet to load the checkout iframe
orderId | string | The leasecloud order id

## Checkout API update end point

```http
PATCH /v1/orders/{orderId} HTTP/1.1
Content-Type: application/json
Authorization: Bearer [bearer token]
```

<div class="move-right">
  <h3>Request body example</h3>
</div>

```json
{
  "totalAmount": 12000,
  "shippingAmount": 0,
  "shippingVAT": 0,
  "currency": "SEK",
  "locale": "sv-se",
  "country": "SE",
  "VAT": 0,
  "months": 36,
  "items":
  [
    {
      "name": "MacBook Pro 15",
      "productId": "13",
      "quantity": 1,
      "unitAmount": 3400000,
      "totalAmount": 3400000,
      "VAT": 0
    },
    {
      "name": "iPhone X",
      "productId": "233",
      "quantity": 2,
      "unitAmount": 1200000,
      "totalAmount": 2400000,
      "VAT": 0
    }
  ],
  "partnerUrls": {
    "terms": "https://www.example.se/terms",
    "checkout": "https://www.example.se/checkout",
    "confirmation": "https://www.example.se/confirmation"
  }
}
```

<div class="move-right">
  <h3>Response body</h3>
</div>

```json
{
  "html": "<div id=’leasecloud_leaseflow_container’>
        <script>(function() {
          // Some script to load the iframe into the page
          var conf = {
            orderID: '1293u924972439'
          }
          createElement(script)
        })()</script>
        <noscript>Please <a href=’https://enable-javascript.com/’>enable javascript</a>.</noscript>
      </div>",
  "orderId": "1293u924972439"
}
```

<div class="move-right">
  <h3>Error response body</h3>
</div>

```json
{
  "error": {
    "code": "InvalidOrder",
    "message": "Validation error",
    "fields": [
      {
        "title": "notNull Violation",
        "message": "totalAmount cannot be null",
        "field": "totalAmount"
      },
    ]
  }
}
```

### HTTP Request

`POST /v1/orders/{orderId}`

### HTTP Body

Same as create

### HTTP Response

Same as create
