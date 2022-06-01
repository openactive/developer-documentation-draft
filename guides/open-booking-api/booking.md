# Booking (B)

Now the orderQuote has created and the orderItems and customer confirmed by your first two endpoints C1 and C2 it's time to make the booking. You will be building booking endpoint (B) which will receive a PUT request which should create an order in your booking system.

### Request

Start by creating a route in your booking system in this format

`{baseUri}/orders/{uuid}`

Here's what a request will look like

<details>

<summary>Example request</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "Order",
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

??? Explain breakdown of fields of request ???

### Response

You should use the request to make a booking within your system. If the booking is successful you should respond with the order, an example response is shown below.&#x20;

<details>

<summary>Example response</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "Order",
  "@id": "https://example.com/api/orders/e11429ea-467f-4270-ab62-e47368996fe8",
  "orderNumber": "AB000001",
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
  "customer": {
    "@type": "Person",
    "email": "geoffcapes@example.com",
    "telephone": "020 811 8055",
    "givenName": "Geoff",
    "familyName": "Capes"
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
      "@id": "https://example.com/api/orders/e11429ea-467f-4270-ab62-e47368996fe8#/orderedItem/1234",
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
      },
      "accessPass": [
        {
          "@type": "Barcode",
          "text": "0123456789"
        }
      ]
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
  }
}
```

</details>

??? Explain fields inline here ???

You can now run the `PUT Book (B)` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a response that matches the example above.&#x20;

Build your own test in your booking system to ensure that when a successful request is made, a booking is made within your system.

{% hint style="info" %}
If you have used a lease make sure it is removed following a successful booking
{% endhint %}

### Error handling

If for some reason the booking cannot logically be completed you because there is a conflict with the orderItems and the availability in the system for example, or for some reason the conditions of the booking have changed between C2 and B a 409 error should be raised. This should be a rare, as most errors of this type are likely to be raised in C1 or C2.

<details>

<summary>Example 409 error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "UnableToProcessOrderItemError",
  "description": "An error occurred while processing the items within this booking."
}
```

</details>

??? postman link here for error ???

As in C1 and C2 you should ensure that booking endpoint B can respond to connectivity issues with a 500 error.

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

??? postman link here for error ???

### Up next

Now you have created main end points for the API it's time consider payment&#x20;

