# Adding the rest of your data

You have a feed with one item that contains data - how do we add further items with data?

We could just remove the `LIMIT` but if we have more than a few hundred items that could inhibit performance, and put strain on your system.

For similar reasons if a feed consumer has already checked your feed, we want them to be able to just get the updates rather than having to consume the whole feed

And if a consumer is only checking updates, we need to let them know an item has been removed.

The solution OpenActive uses for this is Realtime Paged Data Exchange (RPDE).

## Realtime Paged Data Exchange

This concept is key to your implementation of Open Opportunity Feeds, take you time to understand how it works. To get you started - here is a video giving you an overview of RDPE.

{% embed url="https://youtu.be/yHZS24xzY-8" %}

### Reference Implementation&#x20;

So you can get a better understanding of how this process works you can refer to this [reference implementation](https://reference-implementation.openactive.io/OpenActive), it gives an example of feeds that have been correctly implemented.

Here's the stages a consumer of your feed would go through;&#x20;

1. Consumer visits base URL for the feed [https://reference-implementation.openactive.io/feeds/scheduled-sessions](https://reference-implementation.openactive.io/feeds/scheduled-sessions)
2. The consumer will visit the least recently modified items and ingest them into there system, in the first instance this will be the base URL since no data has been ingested yet.
3. They visit the URL in "next" to get the next page of items
4. They repeat this until the "items" field is empty and the "next" URL is the same as the current page's URL [https://reference-implementation.openactive.io/feeds/scheduled-sessions?afterTimestamp=637890336000000000\&afterId=20000](https://reference-implementation.openactive.io/feeds/scheduled-sessions?afterTimestamp=637890336000000000\&afterId=20000)
5. After an interval they check the "next" URL for updates and repeat the above process

### Advantages

This system has some clear advantages, that are:

* Paging limits individual query time
* Simple harvesting procedure
* Returning consumers only fetch updates
* When an item is updated or deleted it's modified timestamp will move it to the front of the feed, so returning consumers will see changes

## Important properties of RDPE

Below is a list of the important properties of RDPE feeds that when understood will help complete your implementation: &#x20;

* The feed must be a continuous list of records that are sorted deterministically (i.e the sort will return the same result each time it is applied) and chronologically (i.e. in the order they were updated). The ordering could be applied in two ways:
  1. First by modified timestamp, and second by ID&#x20;
  2. By an incrementing counter where records are assigned a new unique value on each update.
* Every record is only present once in the entire list at any given moment. The record can be distinguished by its unique ID, and its position in the list depends on when it was last updated. This means records can freely move position in the list as they are updated.
* This deterministic and chronological ordering based on timestamp allows for pages of arbitrary size to be sent.&#x20;
* If a record is updated during the examination of pages by a consumer, that object must appear on a subsequent page (i.e. simply reappear further down the list).
* Pages are defined using a "next page URL", which _must_ contain enough information to identify a position in the list (e.g. by a "timestamp" and "ID" combination). This "next page URL" must not reference a specific record, as that record can change position in the list.
* If a "next page URL" is not used to access the list, the first page must be returned.
* When the consumer reaches the end of the list they can consider themselves up-to-date at that moment. They can infrequently revisit the end of the list in order to retrieve further updates.
* If any record is added to the list or updated it _must_ remain in the list while it is in an `"updated"` state, or remain in the list for at least 7 days from the point in time at which it transitioned to a `"deleted"` state. This ensures that deletions are synchronised to data consumers.

{% hint style="info" %}
Items **must never** disappear from the list
{% endhint %}

{% hint style="info" %}
An RDPE feed **is not** a stream of updates - items only appear once, and when update there position in the list changes.
{% endhint %}

## Choosing an ordering strategy

There are 2 valid ordering strategies as mentioned earlier in the list of important properties of RDPE feeds.

You can either sort the items in your list by there timestamp and then by id, or you can sort by there change number. These will both ensure that updated items in the list will move to the bottom of the list.&#x20;

{% hint style="info" %}
Change number is provided by some databases as an accurate internal timestamp that is automatically converted to a integer that is incremented. Some databases have a "row version" which you could apply your own increments to.&#x20;
{% endhint %}

It's important to be aware of the concept of "race conditions" when updating your feed. When updating your feed you may start multiple database transactions at the same time. This could lead to the list being incorrectly sorted for a short time, as a data consumer may be accessing the list whilst these updates are occurring.&#x20;

In order to avoid this you should:

* First commit the transaction that updates the item itself, then update the timestamps or change numbers after the transaction has been committed, outside of a transaction, using `GETDATE()` or similar.
* Ensure the RPDE endpoint filters out all items with a "modified" date after 2 seconds in the past, to delay items appearing in the feed.
* If using the the timestamp and id ordering strategy, use a timestamp column with a high degree of accuracy (e.g. `datetime2` in SQL Server

??? Seems like both change number and timestamp/id can have race condition issues - is there any benefit of one over the other ???

## Making our feed an RDPE feed

First your feed endpoint needs to accept these parameters depending on the sorting strategy you've chosen:

* `afterTimestamp` and `afterId`&#x20;
* `afterChangeNumber`&#x20;

You should use these parameters to filter your query so that your feed end point returns the data in the correct order.

For example, here is an extension to our [example SQL query](adding-data.md#example-query-for-building-items) from earlier:

```
SELECT
  s.id AS id,
  s.updated_at AS modified,
  s.starts_at,
  s.duration,
  s.capacity,
  s.remaining_capacity,
  a.id AS activity_id,
  a.name AS activity_name
FROM sessions AS s
JOIN activites AS a 
  ON a.id = s.activity_id
WHERE (modified = @afterTimestamp AND s.id > @afterId)
      OR (modified > @afterTimestamp)
ORDER BY modified, id
LIMIT 500
```

The `WHERE` clause should only be included if the `afterTimestamp` and `afterId` parameters are provided. Adjust the `WHERE` and `ORDER` clauses appropriately if you are using `afterChangeNumber` .

This might be slow if you need to join several tables. For now watch out for n+1 queries and/or reduce the `LIMIT` , but don't worry if it is a little slow, we will come back to [performance optimization and architecture later](efficient-database-queries.md).

{% hint style="info" %}
Ordering and filtering is the most common cause of bugs in Open Opportunity feeds, pay special attention to the `WHERE` and `ORDER` clauses.

```
WHERE (modified = @afterTimestamp AND s.id > @afterId)
      OR (modified > @afterTimestamp)
ORDER BY modified, id
```
{% endhint %}

You should also update the "next" field you return. Each page is defined by the last items ordering fields from the previous page. So this would make the "next" page field URL either of these two:

* `{baseUri}/scheduled-sessions?afterTimestamp={modified timestamp of last item returned}&afterId={id of last item returned}`&#x20;
* &#x20;`{baseUri}/sceduled-sessions?afterChangeNumber={modified timestamp of last item returned}`

This should leave you with an output that matches this example here:

<details>

<summary>Example feed page</summary>

```
{
  "next": "{baseUri}/scheduled-sessions?afterTimestamp=637890336000000000&afterId=500",
  "items": [
    {
      "state": "updated",
      "kind": "ScheduledSession",
      "id": 1,
      "modified": 637890336000000000,
      "data": {
        "@context": "https://openactive.io/",
        "@type": "ScheduledSession",
        "@id": "https://reference-implementation.openactive.io/api/identifiers/scheduled-sessions/1/events/1",
        "startDate": "2022-06-02T13:56:24+00:00",
        "endDate": "2022-06-02T19:48:24+00:00",
        "superEvent": "https://reference-implementation.openactive.io/api/identifiers/session-series/1",
        "duration": "PT5H52M",
        "maximumAttendeeCapacity": 27,
        "remainingAttendeeCapacity": 27
      }
    }, 
    {...}
  ],
  "licence": "https://creativecommons.org/licenses/by/4.0/" 
}
    
```

</details>

If you have implemented this correctly you should be able to visit the "next" URL and see the next page of your feed.

You should check that the last page in your feed is functioning correctly, it should have the following two properties so that the data consumer knows they have reached the end of the feed.

* The items property is an empty array.
* The next property matches the URL of the current page.
