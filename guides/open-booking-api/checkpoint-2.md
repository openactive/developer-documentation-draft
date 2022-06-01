# Checkpoint 2 (C2)

Now you have completed checkpoint one (C1), it's time to move on to checkpoint 2 (C2). In this endpoint the broker will make a similar request except this time it will confirm the opportunity and the customer information.

### Request

Start by creating an endpoint `{baseUri}/order-quotes/{uuid}`

{% hint style="info" %}
C1 and C2 are very similar endpoints. Most of the logic in your booking system can probably be shared.
{% endhint %}

Here is an example of a C2 request that will be made to this endpoint.

<details>

<summary>Example C2 Request</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderQuote",
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
  ]
}
```

</details>

The key difference between the C1 and C2 request is that it comes with a `customer` property which contains personal details, such as the customers `givenName` and `email` .

### Response

Here is an example of what your response will look like:

<details>

<summary>Example C2 response</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderQuote",
  "@id": "https://example.com/api/order-quotes/e11429ea-467f-4270-ab62-e47368996fe8",
  "orderRequiresApproval": false,
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
  "lease": {
    "@type": "Lease",
    "leaseExpires": "2018-10-01T11:00:00Z"
  },
  "orderedItem": [
    {
      "@type": "OrderItem",
      "position": 0,
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
        "maximumAttendeeCapacity": 30,
        "remainingAttendeeCapacity": 20,
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
          "duration": "PT1H",
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
  ]
}
```

</details>

This response contains the same properties as in C1 except in addition it confirms the customer details.

You can now run the `PUT Checkpoint 2 (C2)` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a response that matches the example above. You may wish to run the requests as follows, using the simple booking flow:&#x20;

* `GET Sample requests`
* `PUT Checkpoint 1 (C1)`
* `PUT Checkpoint 2 (C2)`

You can run the [OpenActive Validator](https://validator.openactive.io/) to check that your response is formatted correctly. You can remind yourself of how to use the OpenActive Validator [here](../../getting-started/tools-and-resources/#openactive-validator). &#x20;

{% hint style="info" %}
A lease can be created or extended in this response
{% endhint %}

{% hint style="info" %}
User data must not persist beyond the lease expiry
{% endhint %}

### Error handling

Issues with the orderItem's themselves can be responded to in the same way as in C1 - with a 409 error, indicating a conflict that can be resolved by the Customer.&#x20;

<details>

<summary>Example 409 error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderQuote",
  "@id": "https://example.com/api/order-quotes/e11429ea-467f-4270-ab62-e47368996fe8",
  "orderRequiresApproval": false,
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
  "lease": {
    "@type": "Lease",
    "leaseExpires": "2018-10-01T11:00:00Z"
  },
  "orderedItem": [
    {
      "@type": "OrderItem",
      "position": 0,
      "orderItemStatus": "https://openactive.io/OrderItemConfirmed",
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
        "maximumAttendeeCapacity": 30,
        "remainingAttendeeCapacity": 20,
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
          "duration": "PT1H",
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
      },
      "error": [
        {
          "@type": "OpportunityIsFullError",
          "description": "There are no spaces remaining in this opportunity"
        }
      ]
    }
  ],
  "totalPaymentDue": {
    "@type": "PriceSpecification",
    "price": 0,
    "priceCurrency": "GBP"
  },
  "totalPaymentTax": [
    {
      "@type": "TaxChargeSpecification",
      "name": "VAT at 20%",
      "price": 0,
      "priceCurrency": "GBP",
      "rate": 0.2
    }
  ]
}
```

</details>

Issues outside of the orderItem's for example a connectivity error should be responded to with a 500 error.&#x20;

??? If there are issues with other properties of the `OrderQuote` outside of `orderedItem`, the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) _must_ respond with a JSON-LD response which includes only the appropriate `OpenBookingError` and the appropriate status code. ???

<details>

<summary>Example 500 error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "TemporarilyUnableToProduceOrderQuoteError",
  "description": "Temporary error occurred in the database"
}
```

</details>

### Up next

Now you have endpoints that allow a customer and a broker to select an opportunity to book, it's time to move on to completing the booking with the booking endpoint B
