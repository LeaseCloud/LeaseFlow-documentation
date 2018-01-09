# Errors
```json

{
  "error": {
    "code": "OrderCantBeModified",
    "message": "It's to late to edit the order",
  }
}
```

LeaseCloud are using rest status [codes](http://www.restapitutorial.com/httpstatuscodes.html)

If there is an error we will return a 4xx or 5xx status code

The response will be a json object only containing an error object.

The error object will always contain `code` and `message` and in the case of
`InvalidOrder` it will also contain `fields` which is an array of invalid fields.

### Codes:
**Default responses**

* NotFound **404**
* BadRequest **400**
* Forbidden **403**
* InternalServerError **500**

**Custom errors**

* InvalidOrder **400** = Invalid form input and also contains `fields` attribute
* OrderNotAccepted **400** = When an order is denied instantly like none existing org number or bad credit rating
* OrderCantBeModified **403** = When it's to late or to early to change an order e.g. edit an order that is shipped

```json
{
  "error": {
    "code": "InvalidOrder",
    "message": "Validation error",
    "fields": [
      {
        "message": "totalAmount cannot be null",
        "field": "totalAmount"
      }
    ]
  }
}
```

### Fields
* field = Which field that didn't pass the validation
* message = Human readable reason for invalidation
* code = Only a few errors get's a code at the moment
