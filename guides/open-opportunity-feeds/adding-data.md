---
description: Creating your first feed containing data
---

# Adding Data

We are going to fill in the data within an item in a `ScheduledSession` feed. Start by changing your events endpoint you've created `{baseUri}/events` to `{baseUri}/scheduled-session`

{% hint style="info" %}
This guide is taking you step-by-step through building a endpoint - it's not how you'll build every feed, but helps you grasp the concepts within the feeds.
{% endhint %}

### Example feed

Next you will be adding populating the `"data"` field so that it returns as shown in this example:&#x20;

```
{
  "next": "{baseUri}/scheduled-session",
  "items": [
    {
      "state": "updated",
      "kind": "ScheduledSession",
      "id": 1,
      "modified": 637885152000000000,
      "data": {
        "@context": "https://openactive.io/",
        "@type": "ScheduledSession",
        "@id": "https://reference-implementation.openactive.io/api/identifiers/scheduled-sessions/1/events/1",
        "startDate": "2022-05-27T17:48:21+00:00",
        "endDate": "2022-05-27T20:56:21+00:00",
        "superEvent": "https://reference-implementation.openactive.io/api/identifiers/session-series/1",
        "duration": "PT3H8M",
        "maximumAttendeeCapacity": 1,
        "remainingAttendeeCapacity": 1
      }
    }],
  "licence": "https://creativecommons.org/licenses/by/4.0/"
}  
```

Let's go through each of the fields in the data object of this item so you can get an understanding of the data being shared in this feed.

### Key properties

`@context` provides a globally unique identifier that provides definitions for the remainder of the fields in this context. For example using `"https://openactive.io/"` defines the property `"startDate"` of the type `"Event"` . You should use this URL for your data objects as it will provide a JSON-LD representation of the object. You can learn more about this here. (LINK ???)&#x20;

`@type` must be present and set to `"ScheduledSession"`

`@id` is a unique URI based identifier for the record and used for compatability with JSON-LD, this does not need to be a actual end point but is helpful name-spacing. The `@id` field is used to link between split feeds which we will come to later on.&#x20;

`startDate` is the start date and time of this event

### Recommended properties

`endDate` is the end date and time of this event

`eventStatus` it the status of an event. It can be used to indicate rescheduled or cancelled events.

`leader` refers to a person who will be leading an event e.g. a coach. This is a more specific role than an organiser or a contributor. The person will need to have given their consent for their personal information to be present in the Open Data.

`maximumAttendeeCapacity`is the maximum capacity of the event.

`offers` is an array that includes the price of attending the event.

`remainingAttendeeCapactiy` is the number of places that are still available for the event.

`url` is the web page that describes the event.

`superEvent` is the property that identifies the feed item that is linked to this item.

`duration` is the property that show the duration of the event  in ISO8601 format.

{% hint style="info" %}
Ensure empty objects, arrays, strings or nulls are removed from items
{% endhint %}

### Example query for building items

You will likely have to join several tables to build the items for your feed. For example if you had the following models in your booking system:

* **Activity** which represents a fitness class, e.g. "Yoga". This would approximately map to **SessionSeries**.
* **Session** which represents an individual session of an activity. This would approximately map to a **ScheduledSession**.

Then the query you would use to build the **ScheduledSession** feed might look something like this:

```
SELECT
  s.id,
  s.starts_at,
  s.duration,
  s.capacity,
  s.remaining_capacity,
  s.updated_at AS modified,
  a.id AS activity_id,
  a.name AS activity_name
FROM sessions AS s
JOIN activites AS a 
  ON a.id = s.activity_id
LIMIT 10
```

From which you could construct the **ScheduledSession** items in your feed.&#x20;

Keep in mind that the `@id` and `superEvent` fields must be URIs. You should come up with a sensible scheme for them now as changing once you publish your feeds will be difficult. They don't have to point to the existing relevant URLs in your booking system, but it can make debugging easier if they are.

It might not be easy or even possible to do it in one query. Don't worry about being performant or elegant, we will cover performance in the [Feed performance](efficient-database-queries.md) section later on.

### Libraries

You may find it helpful to investigate some of the OpenActive libraries at this point, they have been made to make it easy to create open opportunity data feeds.

| Language              | Open Opportunity Data Feeds                                                  |   |
| --------------------- | ---------------------------------------------------------------------------- | - |
| .NET                  | [OpenActive.NET](https://www.nuget.org/packages/OpenActive.NET/)             |   |
| PHP                   | [openactive/models](https://packagist.org/packages/openactive/models)        |   |
| Ruby                  | [openactive](https://rubygems.org/gems/openactive)                           |   |
| JavaScript/TypeScript | @openactive/[models-ts](https://www.npmjs.com/package/@openactive/models-ts) |   |
|                       |                                                                              |   |

### Validate

Check the response that you've created is functioning correctly and contains the correct fields using the [OpenActive Validator](https://validator.openactive.io/). You can remind yourself of how to use the OpenActive Validator [here](../../getting-started/tools-and-resources/#openactive-validator).  &#x20;

### Up next

Now you've setup your ScheduledSession with one item with data, it's time to look at adding more items to your feed.
