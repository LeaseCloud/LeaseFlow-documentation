---
title: LeaseCloud API Reference

language_tabs:
toc_footers:
  - <a href='https://www.leasecloud.se'>LeaseCloud AB</a>

includes:
  - checkout
  - orders
  - webhooks
  - errors

search: false
---

# Introduction

Welcome to LeaseCloud API reference site. Please feel free to contact us if you have any questions or feedback.

Here you will find everything you need to develop LeaseCloud modules for different e-commerce platforms or to integrate LeaseCloud in your checkout.

<!---
# SDK
* [PHP SDK](https://github.com/LeaseCloud/leasecloud-php-sdk)
* [.NET SDK](https://github.com/LeaseCloud/leasecloud-dotnet-sdk)

# Plugins
* [Woocommerce](https://wordpress.org/plugins/leasecloud-for-woocommerce/)
* Magento [M1](https://www.leasecloud.se/leasing-magento-m1/) [M2](https://www.leasecloud.se/leasing-magento-2)
-->
# Authorization
<div class="move-right">
  <h3>Required http header</h3>
</div>

```yaml
Authorization: Bearer [bearer token]
```

All requests requires `Authorization` header to be set with `Bearer [ACCESS_TOKEN]`.
When you are becoming a partner we will give you a access token and a webhook token

Use this [contact form](https://www.leasecloud.se/#lc-contact) to get a API key or give us a call at +46844688110.

Until we have a signed partner agreement you will use a sandbox API key that is fully functional but doesn’t create live orders.

# Data formats

### Amount
All amounts will be in the smallest currency amount. E.g. 1 SEK = 100 ören, 1 USD = 100 cent

### Strings
The maximum length of a string may be 255 characters.

### Currency
All currencies is in ISO 4217 standard. E.g. SEK, EUR

### Country
All Countries are is in ISO 3166 alpha-2. E.g. se = Sweden, no = Norway.

