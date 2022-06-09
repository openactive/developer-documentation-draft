---
description: Create your first endpoint for the Open Booking API.
---

# Checkpoint 1 (C1)

Now you have an understanding of how the booking flow works, and have understood how your own database maps to the objects required we can get started.

### Endpoint URL

Firstly you should decide on the `baseUri` to form a namespace for your implementation of the API (e.g. `/openactive/api`)

{% hint style="info" %}
Set your`DatasetSiteUrl`in your dataset site template to this `baseUri`. It will be the homepage for your Open Booking API, making it easy to discover.
{% endhint %}

Next, create a route in the following format that can receive a PUT request&#x20;

`PUT {baseUri}/order-quote-templates/{uuid}`

A Broker will call this endpoint after a Customer selects an Opportunity from your feed.

{% hint style="info" %}
Reminder - an opportunity is an instance of an event e.g. "Yoga Tuesday 14th 7pm"&#x20;
{% endhint %}

### Testing with Postman

{% hint style="info" %}
You'll need to have installed Postman and set up the workspace, instructions for this can be [found here](../../getting-started/tools-and-resources/testing-with-postman.md).
{% endhint %}

We are now going to test with Postman, you'll find links throughout the guide but for this first one we will take you through step-by-step, ensure you've run the through the [Postman setup first](../../getting-started/tools-and-resources/testing-with-postman.md).

* Select the "Open Booking API" collection
* Ensure the Broker Microservice is running in the background.
* Select the `GET Sample X (Simple)` request (where `X` is your desired opportunity type)
* Click 'Send' and see a response body containing a randomly selected opportunity, which is used to parameterise the other requests in Postman.
* Select the `PUT Checkpoint 1 (C1)` request
* Click 'Send' and if your C1 request is set up correctly you'll see a 200 status response, with a body containing a correctly formatted OrderQuote. For now you will see an error, since you have not set up your endpoint yet.

### Request

The C1 endpoint acts as a "dry-run" of the order to confirm the details of the opportunity, your system will return a response that looks broadly similar to a finished booking.

Here's what a request will look like:

<details>

<summary>Example C1 request</summary>

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
  "seller": "https://example.com/api/organisations/123",
  "orderedItem": [
    {
      "@type": "OrderItem",
      "position": 0,
      "acceptedOffer": "https://example.com/events/452#/offers/878",
      "orderedItem": "https://example.com/events/452/subEvents/132"
    }
  ]
}
```

</details>

Here's a breakdown of the key fields in this request:

* `@context` is a JSON-LD concept, it is a URL that points to the definitions of properties for the API, particularly a definition of the different types.&#x20;
* `@type` is the object that is being used as part of this request, in the case `OrderQuote`
* `brokerRole` & `broker` are properties that identify who the broker is, and contain information that will eventually be used in authentication.
* `seller` is the organization or person providing access to events or facilities.
* `orderItem` is an array of items that make up the `OrderQuote`
  * Each `orderItem` has a `@id` which is unique URL (that does not need to be an actual working link but a unique identifier) and a `@type` which corresponds to the type of `orderItem`.

{% hint style="info" %}
These guides will mention the key properties in requests and responses, for the details of every property used, head to the reference section (reference link).
{% endhint %}

### Response

Now let's build up your response.

Here is what your response will eventually look like:

<details>

<summary>Example C1 response</summary>

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
              "@type": "Concept",
              "@id": "https://openactive.io/activity-list#5e78bcbe-36db-425a-9064-bf96d09cc351",
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

* `bookingService` is a property that contains details about your booking system, like it's `name` and `url`
* `orderRequiresApporval` is a boolean property that indicates if the `OrderItem`'s require approval.&#x20;
* `totalPaymentDue` is a property that outlines the specification of the price, for example the cost and currency.
* `totalPaymentTax` is a property that outlines the specification for the tax, for example the amount of tax due, the name of the tax.&#x20;

{% hint style="info" %}
You can choose to use the `lease` field in your response to hold the booking for the customer, this can be extended or created as part of checkpoint 2 (C2).
{% endhint %}

You can now run the `PUT Checkpoint 1 (C1)` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a response that matches the example above.&#x20;

You can run the [OpenActive Validator](https://validator.openactive.io/) to check that your response is formatted correctly. You can remind yourself of how to use the OpenActive Validator [here](../../getting-started/tools-and-resources/#openactive-validator). &#x20;

### Error handling

#### OrderItem errors

If the items being ordered can no longer be ordered you should return an `error` with the relevant `OrderItem` along with the HTTP status code "409 Conflict". For example if all the available slots for an event have been booked:

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

Only the `@type` field is required in the error. However for a better debugging experience for both users of your API and yourself you should also include a `description` field that describes why the error occurred in this particular case. There are other optional fields you may include in your errors if they are helpful:

* `name` A short, human-readable summary of the problem type. It should not change from occurrence to occurrence of the problem.
* `instance` A URI reference that identifies the specific occurrence of the problem.
* `statusCode` An integer representing the HTTP status code.
* `requestId` An ID for the request to help technical support debug problems in production.

There are a number of error `@type`s in the Open Booking API. For any `OrderItem` related errors use an error `@type` from the [10.2.2.3 Order Creation - `OrderItem` Errors](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#order-creation-orderitem-errors) section of the Open Booking specification.

#### Other errors

For errors in any other part of the request you should return just the error, not the full `OrderQuote` object. For example if the broker details supplied are not valid:

<details>

<summary>Example 400 error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "IncompleteBrokerDetailsError",
  "description": "The broker property supplied is not a valid Organisation object"
}
```

</details>

There are many possible errors, all valid error `@type`s can be found in the [10.2 Error Model](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#error-model) section of the specification. When deciding what to return in your errors:

* Always prefer a more specific error `@type` to a less specific one.
* The HTTP status code returned must be the status code required in the specification for the error `@type`.
* `description`s can be different for errors with the same `@type`.
* It is better to give too much information in the `description` than too little.

#### Example errors in the Validator

Example error responses can be found in the [Validator](https://validator.openactive.io/) under the "Samples" menu as shown below.

![](<../../.gitbook/assets/Screenshot 2022-06-06 at 17.06.57.png>)

