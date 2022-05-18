# Introduction

Your booking system likely contains information about different types of activities or facilities that can be booked, when they can be booked and how much a booking would cost.&#x20;

An Open Opportunity Feed is a way to distribute that information in a organised, logical and standardised way.&#x20;

This feed can be viewed by anyone - and used to display availability in your system and facilitate bookings (we will come on to that later).&#x20;

There are different types of feeds that you can create and you should consider which bests suits your booking system as you go through this guide.

A feed is made up of pages of JSON objects that are accessed via a HTTP request made to your application.

In this guide we will walk you through making an opportunity feed - this is what one looks like.&#x20;

<details>

<summary>Opportunity Feed Example</summary>

```
{ 
  "next": "https://reference-implementation.openactive.io/feeds/session-series?afterTimestamp=637884288000000000&afterId=500",
  "items": [
    {
      "state": "updated",
      "kind": "SessionSeries",
      "id": 1,
      "modified": 637884288000000000,
      "data": {
        "@context": [
          "https://openactive.io/",
          "https://openactive.io/ns-beta"
        ],
        "@type": "SessionSeries",
        "@id": "https://reference-implementation.openactive.io/api/identifiers/session-series/1",
        "name": "Metal Yoga",
        "activity": [
          {
            "@type": "Concept",
            "@id": "https://openactive.io/activity-list#c07d63a0-8eb9-4602-8bcc-23be6deb8f83",
            "inScheme": "https://openactive.io/activity-list",
            "prefLabel": "Jet Skiing"
          }
        ],
        "eventAttendanceMode": "https://schema.org/OnlineEventAttendanceMode",
        "offers": [
          {
            "@type": "Offer",
            "@id": "https://reference-implementation.openactive.io/api/identifiers/session-series/1#/offers/0",
            "allowCustomerCancellationFullRefund": false,
            "latestCancellationBeforeStartDate": "P1D",
            "openBookingPrepayment": "https://openactive.io/Required",
            "price": 18.31,
            "priceCurrency": "GBP"
          }
        ],
        "organizer": {
          "@type": "Organization",
          "@id": "https://reference-implementation.openactive.io/api/identifiers/sellers/4",
          "name": "Coyote Classes Ltd",
          "isOpenBookingAllowed": true,
          "taxMode": "https://openactive.io/TaxNet",
          "termsOfService": [
            {
              "@type": "PrivacyPolicy",
              "name": "Privacy Policy",
              "requiresExplicitConsent": false,
              "url": "https://example.com/privacy.html"
            }
          ]
        },
        "url": "https://www.example.com/a-session-age",
        "beta:affiliatedLocation": {
          "@type": "Place",
          "name": "Fake Pond",
          "address": {
            "@type": "PostalAddress",
            "addressCountry": "GB",
            "addressLocality": "Another town",
            "addressRegion": "Oxfordshire",
            "postalCode": "OX1 1AA",
            "streetAddress": "1 Fake Park"
          },
          "geo": {
            "@type": "GeoCoordinates",
            "latitude": 0,
            "longitude": 0
          }
        }
      }
    }],
    "license": "https://creativecommons.org/licenses/by/4.0/"
  }
```

</details>

### Guide Approach

This guide will take you through the implementation of a feed for your booking system. Each stage will take the following approach:

* Begin with a simple or previously understood concept&#x20;
* Add details and some complexity
* Validate and test the implementation
* Move on to the next concept

There will be information along the way that helps you decide how best to implement a feed for you particular booking system. Let's get started.
