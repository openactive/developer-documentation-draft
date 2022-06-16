---
description: Displaying terms and policies
---

# Terms

The Broker _must_ make the Customer aware of any `termsOfService` provided within the `seller`, `broker`, and `bookingService` (which may include Terms and Conditions and Privacy Policy) before the Customer confirms their purchase.

If `requiresExplicitConsent` is `true` for any `termsOfService` then the Customer _must_ only be allowed to proceed if they have explicitly acted to consent to the most recently modified version of such terms, identified by `dateModified` and the `url`, before the Customer completes **B**.

The Broker _may_ remember the consent previously given by the Customer based on the `url` and `dateModified` of the `Terms`, only if `dateModified` is provided, and if consent for storing such preferences is given by the Customer.

Since, during booking, the details of the Customer are provided to the Booking System and the Seller, the Broker _must_ inform the Customer of any implications with respect to the GDPR or other data protection legislation.

Note that `termsOfService` may only be specified at the `seller`, `broker` or `bookingService` level within the `Order`, and may not be provided for a specific `Offer` or `OrderItem`.

Here is an example of the properties for Terms and Conditions and Privacy Policy:

```
"termsOfService": [
  {
    "@type": "Terms",
    "name": "Privacy Policy",
    "url": "https://example.com/privacy-policy",
    "requiresExplicitConsent": false
  },
  {
    "@type": "Terms",
    "name": "Terms and Conditions",
    "url": "https://example.com/terms-and-conditions",
    "dateModified": "2020-02-16T20:31:13Z",
    "requiresExplicitConsent": true
  }
]
```

The URLs in the example are only illustrative.
