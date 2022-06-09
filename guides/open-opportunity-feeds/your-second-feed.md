# Your second feed

The data in your scheduled session feed is a list of specific events that occur at a place and time. So that data consumers can easily navigate your booking system, we are going to add a second feed that lists all the event series you have.&#x20;

{% hint style="info" %}
Reminder: There are other [types of data](what-data-to-use.md) that may be more suited to your system.
{% endhint %}

As before, this feed will be made up of JSON objects and will have the same top-level structure. To get started, return the three top-level fields from your `{baseUri}/session-series`

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

In the same way as before, run a query to return the data that would be appropriate for the `SessionSeries`

<details>

<summary>Example feed</summary>

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

</details>

Outlined below are the key properties and recommended properties for this feed, these can be found also in the[ references](../../reference/data-model/).

### Key properties

`@context` provides a globally unique identifier that provides definitions for the remainder of the fields in this context. For example using `"https://openactive.io/"` defines the property `"name"` of the type `"Event"` . You should use this URL for your data objects as it will provide a JSON-LD representation of the object.

`@type` must be present and set to `"SessionSeries"`

`@id` is a unique URI based identifier for the record and used for compatibility with JSON-LD, this does not need to be an actual end point but is helpful name-spacing.

`name` is for the name of the event

`activity` specifies the physical activity or activities that will take place during an event, as an array of the event's properties.

`location` is the location at which the event will take place or in the case of events that may span multiple locations, the initial meeting or starting point. Locations must be specified as a Place complete with a fully described geographic location and/or address.

`offers` is an array of Offer objects that include the price of attending.

`organizer` is the person or organization ultimately responsible for an event. An organizer might be an Organization or a Person.

`url` is a web page (or section of a page) that describes the event.

### Recommended properties

`ageRange` gives an indication that an event is recommended as being suitable for or is targeted at a specific age range.

`description` gives a plain text description of the event, which must not include HTML or other markup.

`genderRestriction` indicates that an event is restricted to male, female or a mixed audience. This information must be displayed prominently to the user before booking. If a gender restriction isn't specified then applications should assume that an event is suitable for a mixed audience.

`image` is an image or photo that depicts the event, e.g. a photo taken at a previous event.

`leader` refers to a Person who will be leading an event, e.g. a coach. This is a more specific role than an organiser or a contributor. The Person will need to have given their consent for their personal information to be present in the Open Data.

`level` is a general-purpose property for specifying the suitability of an event for different participant “levels”. E.g. `Beginner`, `Intermediate`, `Advanced`. Or, in the case of martial arts, specific belt requirements.

### Validation

Once you are happy that you have created the required fields for your feed as well as any recommended or optional fields, you should run the test suite and use the validator as outlined in the previous [validating your feed](validating-your-feed.md) section.

