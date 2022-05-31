# Feed performance

Your feeds are working and have all the relevant information included. However it's likely running a complex query or queries with `JOIN`s on every feed page request is too slow.

{% hint style="info" %}
You should be aiming for a response time of under 1 second (ideally lower) at 500 items/page.
{% endhint %}

You might also be concerned that your feeds will become too long after some time.&#x20;

In this section we will look at strategies for handling these issues:

* Partial item caching with on demand JSON production
* Full JSON caching
* HTTP caching and CDNs
* Removing items from the feed after a retention period

## Caching

So far we have constructed the JSON items for each of our feed pages on every request "from scratch". This means that potentially expensive database queries and JSON serialization is run on every feed request, even if nothing has changed since the last time a consumer requested the feed.

Depending on your booking system's architecture this might be fast enough already. It might still be worth considering implementing at least partial caching, because a cached feed will stay fast as your database grows.

The downside to caching is increased code complexity and memory usage, but in the majority of Open Opportunity feed implementations the benefits will outweigh the drawbacks.

## A caching table

The first option for caching is to have a separate table that represents feed items where all information relevant to an item is cached.&#x20;

* Create a table called something like `feed_items`.
* The columns of the table can be the fields that are returned by the query you currently use to build your feed.
* You will also need an `item_type` column to distinguish between **SessionSeries**, **ScheduledSession**, **FacilityUse**, etc.
* To populate the table use the query or queries you currently use to build your feed. Instead of building the JSON items just copy the fields directly in to the table.
* Use one row per feed item in the new table.
* Keep in mind there **should** be duplicate data. For example multiple **SessionSeries** may have the same location information, that location information should be copied to every feed item's row.

Now you can replace the query you currently use to build your feed with something like:

```
SELECT *
FROM feed_items
WHERE item_type = @itemType
      AND (
            (modified = @afterTimestamp AND id > @afterId)
            OR (modified > @afterTimestamp)
      )
ORDER BY modified, id
LIMIT 500
```

You can probably reuse most of your existing serialization code to turn the results of the query in to a JSON feed page.

### Updating the cache

Querying your new [denormalized](https://en.wikipedia.org/wiki/Denormalization) feed items table should be much faster, but your feed now won't update when the relevant tables in your database update.

You might be thinking of using a recurring batch job to update the feed items table. However this isn't recommended because your feed is expected to stay up-to-date in close to real-time, and if you use a recurring job the items in your feed can be out-of-date by up to the amount of time between job runs.

There are a couple of strategies you can use to keep you cached table as close to up-to-date as possible:

* Use database triggers to run a query that updates the relevant feed item rows when an associated table is updated.
* The same as above but with application level triggers. These are often called "model callbacks" or "observers".

To make this work, you will need to keep a record of the associations for each row so the triggers know which feed item rows to update. You could use foreign keys on the feed items table, or it might make more sense to use a join table in your booking system.

If your database supports them you might also be able to build your feed items table using database views or materialized views but that is outside of the scope of this guide.

### Partial caching

If it will be difficult or complicated to trigger updates from particular associated tables, you can introduce caching gradually by having a feed items caching table that only caches some of the relevant information. You will still have to do some `JOIN` queries on every request, but this can be a reasonable compromise or stepping stone to improving performance.

## Full JSON caching&#x20;

If we are caching all the relevant information to construct the feed item JSON, why not just cache the final JSON output itself? Many databases have native JSON types and every database has an arbitrary length string type that can be used to store JSON.

This strategy is similar to the one above but instead of having separate columns for each field you just need the following columns:

* The item `id`
* A `modified` datetime or timestamp
* A `json_item` field that stores the JSON item for the feed
* Any relevant associations for triggers

Then the query for your feed will look something like this:

```
SELECT json_item
FROM feed_items
WHERE feed_items.item_type = @itemType
      AND (
            (feed_items.modified = @afterTimestamp AND feed_items.id > @afterId)
            OR (feed_items.modified > @afterTimestamp)
      )
ORDER BY feed_items.modified, feed_items.id
LIMIT 500
```

And put the result of the query directly in the `items` field of your feed page response.

## HTTP caching + CDN

We can cache whole page request

How to set relevant HTTP headers

LastModified, ETag and why to do the best one (find out what's best)

Clients that respect cache headers will get super quick responses.

On it's own not that useful, because client will rarely visit same page twice

Solution: Use a CDN. CDN will pass on request, any new client CDN will know it's cached and just pass on it's cached version.

Explain how to use CDNs.

## Removing items from feed after retention period

Ideally items once in feed remain forever

Might make your feed too big, or use up too storage in your DB

In RDPE you are allowed to remove items, so long as you give consumers enough time to catch.

This is set to 7 days

Example of what you can do to reduce feed size

Example of cron script to clean up "deleted" older than 7 days
