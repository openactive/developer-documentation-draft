# Retrieving orders

## Orders feed

Broker and Brokers customers will want to be able to access a list of completed orders, so as to keep up-to-date and in sync with bookings on your system. The concept of Realtime Paged Data Exchange (RDPE) feeds is explained in more detail in the Open Opportunity Feeds guide (??? LINK).

### Request

Ensure you have the route  `{baseURi}/orders-rpde`

The broker will call this endpoint with an GET request.

### Response

The Orders feed must not included the full contents of the Opportunities within the orderedItem, instead it should only display the @type and @id.

The `acceptedOffer` property within the Orders feed must include a snapshot of the `Offer` at the point it was booked or last updated. The `acceptedOffer` is included in the feed, as it reflects the Customer's purchase. Here is an example of the response you should give:

<details>

<summary>Example response</summary>

```
{
  "next": "https://example.com/api/orders-rpde?afterTimestamp=1521565719&afterId=e11429ea-467f-4270-ab62-e47368996fe8",
  "items": [
    {
      "state": "updated",
      "kind": "Order",
      "@id": "e11429ea-467f-4270-ab62-e47368996fe8",
      "modified": 1521565719,
      "data": {
        "@context": "https://openactive.io/",
        "@type": "Order",
        "@id": "https://example.com/api/orders/e11429ea-467f-4270-ab62-e47368996fe8",
        "identifier": "e11429ea-467f-4270-ab62-e47368996fe8",
        "orderedItem": [
          {
            "@type": "OrderItem",
            "@id": "https://example.com/api/orders/e11429ea-467f-4270-ab62-e47368996fe8#/orderedItem/1234",
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
              "@id": "https://example.com/events/452/subEvents/132"
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
        ]
      }
    }
  ]
}
```

</details>

You can now run the `GET Orders Feed` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a response that matches the example above. You may wish to run the requests as follows, using the simple booking flow:&#x20;

* `GET Sample requests`
* `PUT Checkpoint 1 (C1)`
* `PUT Checkpoint 2 (C2)`
* `PUT Book (B)`
* `GET Orders Feed`

You can run the [OpenActive Validator](https://validator.openactive.io/) to check that your response is formatted correctly. You can remind yourself of how to use the OpenActive Validator [here](../../getting-started/tools-and-resources/#openactive-validator). &#x20;

### Error handling

??? Link to general open booking error handling ???

## Order status

This endpoint is similar to orders feed, but the request is for a single opportunity instead of a feed. This end point is recommended but not required.

### Request

Ensure you have the endpoint `{baseURi}/orders/{uuid}`

The broker will make a GET request to this endpoint.

### Response

You should respond to this request with the full contents of the Opportunity within the orderedItem. Here is an example of the response you should give:

<details>

<summary>Example response</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "Order",
  "@id": "https://example.com/api/orders/e11429ea-467f-4270-ab62-e47368996fe8",
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

You can now run the `GET Order Status` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a response that matches the example above. You may wish to run the requests as follows, using the simple booking flow:&#x20;

* `GET Sample requests`
* `PUT Checkpoint 1 (C1)`
* `PUT Checkpoint 2 (C2)`
* `GET Order Status`
  * You should see a response containing an OrderQuote
* `PUT Book (B)`
* `GET Order Status`
  * You should see a response containing an Order

You can run the [OpenActive Validator](https://validator.openactive.io/) to check that your response is formatted correctly. You can remind yourself of how to use the OpenActive Validator [here](../../getting-started/tools-and-resources/#openactive-validator). &#x20;

### Error handling

??? Link to generic error handling ???
