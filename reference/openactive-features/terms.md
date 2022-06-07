---
description: Displaying terms and policies
---

# Terms

The [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) _must_ make the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) aware of any `termsOfService` provided within the `seller`, `broker`, and `bookingService` (which may include Terms and Conditions and Privacy Policy) before the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) confirms their purchase.

If `requiresExplicitConsent` is `true` for any `termsOfService` then the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) _must_ only be allowed to proceed if they have explicitly acted to consent to the most recently modified version of such terms, identified by `dateModified` and the `url`, before the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) completes [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b).

The [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) _may_ remember the consent previously given by the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) based on the `url` and `dateModified` of the `Terms`, only if `dateModified` is provided, and if consent for storing such preferences is given by the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer).

Since, during booking, the details of the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) are provided to the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) and the [Seller](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-seller), the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) _must_ inform the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) of any implications with respect to the GDPR or other data protection legislation.

Note that `termsOfService` may only be specified at the `seller`, `broker` or `bookingService` level within the `Order`, and may not be provided for a specific `Offer` or `OrderItem`.

[EXAMPLE 18](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#example-18-properties-for-terms-and-conditions-and-privacy-policy): Properties for Terms and Conditions and Privacy Policy

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

\
