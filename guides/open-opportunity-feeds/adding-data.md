# Adding Data

NOTE: This isn't necessarily how it will work, but stick with it for now

[https://developer.openactive.io/data-model/types/scheduledsession](https://developer.openactive.io/data-model/types/scheduledsession)

We are going to fill in the data for your scheduled session feed - let's rename the feed from event to scheduled session

```
{
  "next": "localhost:3000/feeds/scheduled-session",
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

Required properties

@type must be present and set to "ScheduledSession"

@id is a unique URI based identifier for the record and used for compatability with JSON-LD, this does not need to be a actual end point but is helpful namespacing&#x20;

startDate is the start date and time of this event

@context - discuss JSON-LD Linked Data this enables others to know the definitions of the terms in this data [https://www.youtube.com/watch?v=vioCbTo3C-4](https://www.youtube.com/watch?v=vioCbTo3C-4)&#x20;

Recommended properties

endDate

eventStatus

leader

maximumAttendeeCapacity

offers

remainingAttendeeCapactiy

url

SuperEvent????

Example mapping exercise

* You should have tables that you can join to be able to fill these fields for examples you may want to join an activity table and slots table.
* You would need to do a query like this:

```
SELECT ... JOIN ... LIMIT 10
```

It might not be easy or even possible to do it in one query, don't worry about being performant or elegant, just get the right data out.

Mention that there are libraries for Ruby, .NET, PHP, JS to help.

Note - ensure Empty objects/arrays/strings and nulls should be removed from items

Hit postman link to see if built correctly

Now we will move on to repeat this exercise for the superEvent SessionSeries
