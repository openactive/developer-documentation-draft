---
description: Two optional endpoints that enable sellers to approve or reject bookings.
---

# Approval booking flow

{% hint style="info" %}
The Approval Booking Flow endpoints are not required and many booking system use the Simple Booking Flow. Decide which flow makes sense for your booking system and skip this section if you do not need the approval endpoints.
{% endhint %}

We've previously discussed the [approval booking flow](api-endpoints.md#approval-booking-flow) and how it enables Sellers using your booking system to approve or reject bookings, now it's time to actually create the endpoints required.

You will need to ensure that both your C1 and C2 endpoints are changed so that the `orderRequiresApporval` is `true` if you are going to use the approval booking flow in your implementation. You will also need to create two further endpoints which are outlined below.

### Postman

Within Postman simply use the "Approval Flow" requests instead of the "Simple Flow" requests.

![](<../../.gitbook/assets/Screenshot from 2022-06-09 13-53-33.png>)

## &#x20;Order Proposal (P)

An `orderProposal` is sent as a PUT request by the Broker to your booking system to request approval of an order. The broker will notify the customer that their order is pending approval.

### Request

You should create an end point `PUT {baseURi}/order-proposals/{uuid}`

This endpoint will receive a `PUT` request, like the example below, the details submitted are identical to those required for endpoint B with the addition of an optional `lease`.

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
  "seller": "https://example.com/api/organisations/123",
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
      "acceptedOffer": "https://example.com/events/452#/offers/878",
      "orderedItem": "https://example.com/events/452/subEvents/132"
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

This request is identical to the one made to the Book (B) endpoint, except that the `@type` is change to `OrderProposal`.

### Response

The response should contain the `OrderProposal` with an `orderProposalStatus` of `https://openactive.io/AwaitingSellerConfirmation`.

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

You can now run the `PUT Proposal (P)` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a response that matches the example above. You may wish to run the requests in the approval flow order, as follows:&#x20;

* Select the `GET Sample X (Approval)` request (where `X` is your desired opportunity type)
* `PUT Checkpoint 1 (C1)`
* `PUT Checkpoint 2 (C2)`
* `PUT Proposal (P)`

You can run the [OpenActive Validator](https://validator.openactive.io/) to check that your response is formatted correctly. You can remind yourself of how to use the OpenActive Validator [here](../../getting-started/tools-and-resources/#openactive-validator). &#x20;

### Error handling&#x20;

If there are any errors with the orderProposal or orderItems you must respond with an error. It is unlikely that this errors will be raised at this stage, as similar errors should have been raised in C1 and C2 prior to the approval process.

As with the Simple Booking Flow, errors must conform to the Open Booking API specification section [10.2 Error Model](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#error-model).

Here is an example of an error response:&#x20;

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

## Order proposal update (U)

If an orderProposal is accepted by the seller the order will be successfully created in your booking system and added to the order feed (another endpoint you will be creating shortly, and not to be confused with the opportunity feed). If however the Seller chooses to reject the orderProposal, i.e. they do not approve it, they will use this order proposal update endpoint to reject the proposal.&#x20;

### Request

You should create the endpoint `PATCH {baseUri}/order-proposals/{uuid}`&#x20;

This end point should receive a `PATCH` request, here's an example of the request that will be made:

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

### Response

Since the only request made to this endpoint is a rejection of an orderProposal the response can be empty and carry a 204 status to indicate the rejection was successfully received.&#x20;

You can now run the `PATCH Proposal update` [Postman request](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), and if you've set this up correctly your should see a 204 response. You may wish to run the requests in the approval flow order, as follows:&#x20;

* Select the `GET Sample X (Approval)` request (where `X` is your desired opportunity type)
* `PUT Checkpoint 1 (C1)`
* `PUT Checkpoint 2 (C2)`
* `PUT Proposal (P)`
* `PATCH Proposal update`

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

## Up next

Now you've worked through the approval booking flow, it's time to use the test suite to check your implementation.&#x20;
