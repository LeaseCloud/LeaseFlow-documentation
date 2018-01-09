
# Config

## Get current config

```http
GET /v1/config HTTP/1.1
Authorization: Bearer [bearer token]
```

<div class="move-right">
  <h3>Response body</h3>
</div>

```json
{
  "financeCompany": {
    "name": "Montly Leasing",
    "logo": "https://example.com/logo.svg"
  },
  "tariffs": [  
    {  
      "months": 24,
      "tariff": 1.47
    },
    {  
      "months": 36,
      "tariff": 1.04
    }
  ]
}
```

Config contains tariffs and info about the finance company
