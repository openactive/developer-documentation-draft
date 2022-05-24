---
description: Two optional endpoints that enable sellers to approve or reject bookings.
---

# Approval booking flow

We've previously discussed the approval booking flow (??? LINK) and how it enables Sellers using your booking system to approve or reject bookings, now it's time to actually create the endpoints required.

## Order Proposal (P)

An `orderProposal` is sent as a PUT request by the Broker to your booking system to request approval of an order. The broker will notify the customer that their order is pending approval.

### Request

You should create an end point `{baseURi}/order-proposals/{uuid}`

This endpoint will receive a PUT request, like the example below, the details submitted are identical to those required for B with the addition of an optional `lease`.

<details>

<summary>Example request</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderProposal",
  "brokerRole": "https://openactive.io/AgentBroker",
  "broker": {
    "@type": "Organization",
    "name": "MyFitnessApp",
    "url": "https://myfitnessapp.example.com",
    "description": "A fitness app for all the community",
    "logo": {
      "@type": "ImageObject",
      "url": "http://data.myfitnessapp.org.uk/images/logo.png"
    },
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "Alan Peacock Way",
      "addressLocality": "Village East",
      "addressRegion": "Middlesbrough",
      "postalCode": "TS4 3AE",
      "addressCountry": "GB"
    }
  },
  "seller": {
    "@type": "Organization",
    "@id": "https://example.com/api/organisations/123"
  },
  "customer": {
    "@type": "Person",
    "email": "geoffcapes@example.com",
    "telephone": "020 811 8055",
    "givenName": "Geoff",
    "familyName": "Capes"
  },
  "orderedItem": [
    {
      "@type": "OrderItem",
      "position": 0,
      "acceptedOffer": {
        "@type": "Offer",
        "@id": "https://example.com/events/452#/offers/878"
      },
      "orderedItem": {
        "@type": "ScheduledSession",
        "@id": "https://example.com/events/452/subEvents/132"
      }
    }
  ],
  "totalPaymentDue": {
    "@type": "PriceSpecification",
    "price": 5,
    "priceCurrency": "GBP"
  },
  "payment": {
    "@type": "Payment",
    "name": "AcmeBroker Points",
    "identifier": "1234567890npduy2f"
  }
}
```

</details>

??? Postman link to check this endpoint can be hit ???

### Response

The response should contain the`OrderProposal` with an `orderProposalStatus` of `https://openactive.io/AwaitingSellerConfirmation`.

<details>

<summary>Example response</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderProposal",
  "@id": "https://example.com/api/order-proposals/e11429ea-467f-4270-ab62-e47368996fe8",
  "orderNumber": "AB000001",
  "orderProposalVersion": "https://example.com/api/order-proposals/e11429ea-467f-4270-ab62-e47368996fe8/versions/8eb1a6ce-3f5b-40b0-87a7-bddb4c5518bd",
  "orderProposalStatus": "https://openactive.io/AwaitingSellerConfirmation",
  "brokerRole": "https://openactive.io/AgentBroker",
  "broker": {
    "@type": "Organization",
    "name": "MyFitnessApp",
    "url": "https://myfitnessapp.example.com",
    "description": "A fitness app for all the community",
    "logo": {
      "@type": "ImageObject",
      "url": "http://data.myfitnessapp.org.uk/images/logo.png"
    },
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "Alan Peacock Way",
      "addressLocality": "Village East",
      "addressRegion": "Middlesbrough",
      "postalCode": "TS4 3AE",
      "addressCountry": "GB"
    }
  },
  "seller": {
    "@type": "Organization",
    "@id": "https://example.com/api/organisations/123",
    "identifier": "CRUOZWJ1",
    "name": "Better",
    "taxMode": "https://openactive.io/TaxGross",
    "legalName": "Greenwich Leisure Limited",
    "description": "A charitable social enterprise for all the community",
    "url": "https://www.better.org.uk",
    "logo": {
      "@type": "ImageObject",
      "url": "http://data.better.org.uk/images/logo.png"
    },
    "telephone": "020 3457 8700",
    "email": "customerservices@gll.org",
    "vatID": "GB 789 1234 56",
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "Alan Peacock Way",
      "addressLocality": "Village East",
      "addressRegion": "Middlesbrough",
      "postalCode": "TS4 3AE",
      "addressCountry": "GB"
    },
    "termsOfService": [
      {
        "@type": "PrivacyPolicy",
        "name": "Privacy Policy",
        "url": "https://example.com/privacy-policy",
        "requiresExplicitConsent": false
      },
      {
        "@type": "TermsOfUse",
        "name": "Terms and Conditions",
        "url": "https://example.com/terms-and-conditions",
        "dateModified": "2019-04-16T20:31:13Z",
        "requiresExplicitConsent": true
      }
    ]
  },
  "customer": {
    "@type": "Person",
    "email": "geoffcapes@example.com",
    "telephone": "020 811 8055",
    "givenName": "Geoff",
    "familyName": "Capes"
  },
  "bookingService": {
    "@type": "BookingService",
    "name": "Playwaze",
    "url": "http://www.playwaze.com",
    "termsOfService": [
      {
        "@type": "Terms",
        "name": "Terms of Service",
        "url": "https://brokerexample.com/terms.html",
        "requiresExplicitConsent": false
      }
    ]
  },
  "orderedItem": [
    {
      "@type": "OrderItem",
      "position": 0,
      "orderItemStatus": "https://openactive.io/OrderItemProposed",
      "unitTaxSpecification": [
        {
          "@type": "TaxChargeSpecification",
          "name": "VAT at 20%",
          "price": 1,
          "priceCurrency": "GBP",
          "rate": 0.2
        }
      ],
      "acceptedOffer": {
        "@type": "Offer",
        "@id": "https://example.com/events/452#/offers/878",
        "description": "Winger space for Speedball.",
        "name": "Speedball winger position",
        "price": 10,
        "priceCurrency": "GBP",
        "validFromBeforeStartDate": "P6D",
        "allowCustomerCancellationFullRefund": true,
        "latestCancellationBeforeStartDate": "P1D"
      },
      "orderedItem": {
        "@type": "ScheduledSession",
        "@id": "https://example.com/events/452/subEvents/132",
        "identifier": 123,
        "eventStatus": "https://schema.org/EventScheduled",
        "startDate": "2018-10-30T11:00:00Z",
        "endDate": "2018-10-30T12:00:00Z",
        "duration": "PT1H",
        "superEvent": {
          "@type": "SessionSeries",
          "@id": "https://api.example.com/events/452",
          "name": "Bodypump",
          "activity": [
            {
              "type": "Concept",
              "id": "https://openactive.io/activity-list#5e78bcbe-36db-425a-9064-bf96d09cc351",
              "prefLabel": "Bodypump™",
              "inScheme": "https://openactive.io/activity-list"
            }
          ],
          "url": "https://example.com/events/452",
          "location": {
            "@type": "Place",
            "url": "https://www.everyoneactive.com/centres/Middlesbrough-Sports-Village",
            "name": "Middlesbrough Sports Village",
            "identifier": "0140",
            "address": {
              "@type": "PostalAddress",
              "streetAddress": "Alan Peacock Way",
              "addressLocality": "Village East",
              "addressRegion": "Middlesbrough",
              "postalCode": "TS4 3AE",
              "addressCountry": "GB"
            },
            "geo": {
              "@type": "GeoCoordinates",
              "latitude": 54.543964,
              "longitude": -1.20978500000001
            }
          }
        }
      }
    }
  ],
  "totalPaymentDue": {
    "@type": "PriceSpecification",
    "price": 5,
    "priceCurrency": "GBP"
  },
  "totalPaymentTax": [
    {
      "@type": "TaxChargeSpecification",
      "name": "VAT at 20%",
      "price": 1,
      "priceCurrency": "GBP",
      "rate": 0.2
    }
  ],
  "payment": {
    "@type": "Payment",
    "name": "AcmeBroker Points",
    "identifier": "1234567890npduy2f"
  },
  "lease": {
    "@type": "Lease",
    "leaseExpires": "2018-10-01T11:00:00Z"
  }
}
```



</details>

??? explain fields if necessary ???

??? postman link to check response valid ???

### Error handling&#x20;

If there are any errors with the orderProposal or orderItems you must respond with a 400 error, the broker can try a different endpoint to get more specific errors. It is unlikely that this error will ever be raised, as similar errors should have been raised in C1 and C2 prior to the approval process.

Here is an example of the error to return:&#x20;

<details>

<summary>Error example</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "IncompleteBrokerDetailsError",
  "description": "Only 'https://openactive.io/CustomerRejected' is permitted for this property."
}
```

</details>

??? postman link to check valid error returned ???

## Order proposal update (U)

If an orderProposal is accepted by the seller the order will be successfully created in your booking system and added to the order feed (another endpoint you will be creating shortly, and not to be confused with the opportunity feed). If however the Seller chooses to reject the orderProposal, i.e. they do not approve it, they will use this order proposal update endpoint to reject the proposal.&#x20;

### Request

You should create the endpoint `/order-proposals/{uuid}`&#x20;

This end point should receive a PATCH request, here's an example of the request that will be made:

<details>

<summary>Example request</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderProposal",
  "orderProposalStatus": "https://openactive.io/CustomerRejected",
  "orderCustomerNote": "Sorry I've actually made other plans, hope you find someone!"
}
```

</details>

??? explain fields ???

??? postman link for valid endpoint ???

### Response

Since the only request made to this endpoint is a rejection of an orderProposal the response can be empty and carry a 204 status to indicate the rejection was successfully received.&#x20;

??? Postman link for valid response ???

### Error handling

If the request made is not a rejection of an orderProposal you should return the following error:&#x20;

<details>

<summary>Example error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "PatchNotAllowedOnProperty",
  "description": "Only 'https://openactive.io/CustomerRejected' is permitted for this property."
}
```

</details>

??? post man link for valid error ???

## Up next

Now you've worked through the approval booking flow, it's not time to utilise the testsuite to check your implementation.&#x20;
