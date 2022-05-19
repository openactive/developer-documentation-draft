# Your second feed

NOTE: Need to talk about materializing scheduledSession

The data in your scheduled session feed is a list of specific events that occur at a place and time. To make it easier navigate your booking system you can add a second feed that lists all the event series you have.

Created another URL called localhost:3000/feeds/session-series and have it return a similar structure to your previous feed, with one item and no data.

```
{
  "next": "localhost:3000/feeds/session-series",
  "items": [
    {
      "state": "updated",
      "kind": "SessionSeries",
      "id": 1,
      "modified": 637885152000000000,
      "data": {}
    }],
  "licence": "https://creativecommons.org/licenses/by/4.0/"
}  
```

Postman link for session series feed

Your now ready to add some data

```
{
  "next": "localhost:3000/feeds/session-series",
  "items": [
    {
      "state": "updated",
      "kind": "SessionSeries",
      "id": 1,
      "modified": 637885152000000000,
      "data": {
        "@context": [
          "https://openactive.io/",
          "https://openactive.io/ns-beta"
        ],
        "@type": "SessionSeries",
        "@id": "https://reference-implementation.openactive.io/api/identifiers/session-series/1",
        "name": "Wooden Jumping",
        "activity": [
          {
            "@type": "Concept",
            "@id": "https://openactive.io/activity-list#c07d63a0-8eb9-4602-8bcc-23be6deb8f83",
            "inScheme": "https://openactive.io/activity-list",
            "prefLabel": "Jet Skiing"
          }
        ],
        "eventAttendanceMode": "https://schema.org/MixedEventAttendanceMode",
        "location": {
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
        },
        "offers": [
          {
            "@type": "Offer",
            "@id": "https://reference-implementation.openactive.io/api/identifiers/session-series/1#/offers/0",
            "allowCustomerCancellationFullRefund": true,
            "latestCancellationBeforeStartDate": "P1D",
            "openBookingFlowRequirement": [
              "https://openactive.io/OpenBookingAttendeeDetails"
            ],
            "price": 0,
            "priceCurrency": "GBP"
          }
        ],
        "organizer": {
          "@type": "Person",
          "@id": "https://reference-implementation.openactive.io/api/identifiers/sellers/5",
          "name": "Jane Smith",
          "isOpenBookingAllowed": true,
          "taxMode": "https://openactive.io/TaxGross"
        },
        "url": "https://www.example.com/a-session-age",
      }
    ]},     
  "licence": "https://creativecommons.org/licenses/by/4.0/"
}  
 
      
```

@context&#x20;

@type&#x20;

@id&#x20;

name

activity

location

offers

organizer

url

recommended

ageRange

description

genderRestriction

image

leader

level

[https://developer.openactive.io/data-model/types/sessionseries](https://developer.openactive.io/data-model/types/sessionseries)

There are other types of feeds that you can create - they are outlined here - but for now we will move onto adding the rest of your data
