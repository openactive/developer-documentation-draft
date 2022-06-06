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

## HTTP caching and Content Delivery Networks (CDN)

In most cases your feed will change intermittently and between changes multiple brokers will check and consume your feed. We have discussed caching individual items to improve performance but your system still builds the final response on every request, even if nothing has changed.&#x20;

We can improve performance further by adding HTTP caching to your feed pages. This way clients will use their cached version of your feed for a period of time before making another request. More importantly it allows you to use a CDN which will cache your feed pages for all clients on servers all around the world, reducing load and further improving performance.

{% hint style="info" %}
In order for the CDN to be effective your application **should not** implement the [optional `limit` parameter specified in the RPDE specification](https://www.w3.org/2017/08/realtime-paged-data-exchange/#modified-timestamp-and-id).
{% endhint %}

### Headers

Your booking system should set the following `Cache-Control` headers for your feeds:

* For the last page in your feed, which contains zero items, set the time to live (TTL) to 8 seconds: `Cache-Control: public, max-age=8`
* For all other pages set the TTL to 1 hour: `Cache-Control: public, max-age=3600`

### CDN configuration using Cloudflare

{% hint style="info" %}
We are using Cloudflare as an example on how to set up a CDN for your feeds but the steps should be similar with other CDNs.
{% endhint %}

In order for [CloudFlare](https://www.cloudflare.com/) to respect your cache control headers, there are five simple steps to follow:

#### 1) Set up CloudFlare as your DNS provider and proxy

After you've [set up CloudFlare](https://support.cloudflare.com/hc/en-us/categories/200275218-Getting-Started) as your DNS provider, check requests are being routed through CloudFlare by enabling the orange cloud button.

#### 2) Set up a page rule with a wildcard that covers your feeds

Use the wildcards to ensure the rule covers all your feeds, for example:

```
*opendata.example.com/api/feeds/*
```

The page rule should have the following configuration:

* **Cache Level:** Everything
* **Origin Cache Control**: On
* **SSL:** Flexible (if you do not have SSL configured on your own server)

#### 3) Set Browser Cache Expiration to Respect Existing Headers

On the Caching configuration page, ensure that following is set:

* **Browser Cache Expiration:** Respect Existing Headers

#### 4) Ensure that your feed does not inadvertently set any cookies

Ensure that your web application or web server infrastructure does not set any cookies on the feed pages (for example load balancer [affinity cookies](https://azure.microsoft.com/en-gb/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)), as these will prevent CloudFlare from caching pages.

#### 5) Test your configuration

Inspect the headers returned by your page to see if CloudFlare is successfully caching your feed.

A successfully cached page will return the following header:

* **cf-cache-status: HIT**

#### Further information

The following articles will help you dive deeper in case you have any issues:

* [Understanding and Configuring Cloudflare Page Rules (Page Rules Tutorial)](https://support.cloudflare.com/hc/en-us/articles/218411427-Understanding-and-Configuring-Cloudflare-Page-Rules-Page-Rules-Tutorial-)
* [How Do I Tell Cloudflare What to Cache?](https://support.cloudflare.com/hc/en-us/articles/202775670-How-Do-I-Tell-Cloudflare-What-to-Cache-)
* [Origin Cache-Control](https://support.cloudflare.com/hc/en-us/articles/115003206852-Origin-Cache-Control)
* [What do the various Cloudflare cache responses (HIT, Expired, etc.) mean?](https://support.cloudflare.com/hc/en-us/articles/200168266-What-do-the-various-Cloudflare-cache-responses-HIT-Expired-etc-mean-)
* [View HTTP headers in Chrome Dev Tools](https://developers.google.com/web/tools/chrome-devtools/network-performance/reference#headers)

## Removing items from feed after retention period

In order to minimise the total number of items within feeds, it is recommended to apply a retention period. In particular `Event,` `ScheduledSession` or `Slot` data items are likely to benefit from removing items after transitioning them to the "deleted" state.

The [Realtime Paged Data Exchange specification](https://www.w3.org/2017/08/realtime-paged-data-exchange/#deleted-items) specifies that:

> If any record is added to the list or updated it must remain in the list in perpetuity while it is in an "updated" state, or remain in the list for at least 7 days from the point in time at which it transitioned to a "deleted" state

{% hint style="info" %}
The [high-volume proposal](https://github.com/openactive/realtime-paged-data-exchange/issues/93) for the RPDE specification is currently widely adopted, and hence it is recommended that `Slot` feeds that have a particularly high volume of small payload items wait 2 days before removing `"deleted"` items from the feed, in place of the specified 7 days.
{% endhint %}

### Option 1: Retention period to minimise storage requirements

If the objective of implementing a retention period is primarily to reduce the number of records stored, records representing events that occur in the past should be pruned by:

* First setting their [`state` to the `"deleted"` state and updating the `modified` value](https://www.w3.org/2017/08/realtime-paged-data-exchange/#deleted-items).
* Then after 7 days removing them from the feed.

This may be implemented via a regular CRON job, for example.

{% hint style="danger" %}
Simply filtering out or removing opportunities from the feed that are in the past is not sufficient, because any opportunity that is edited to have a `startDate` in the past would then disappear from the feed without first transitioning into the [`"deleted"` state](https://www.w3.org/2017/08/realtime-paged-data-exchange/#deleted-items) with an updated `modified` value. Hence the previous version of such a record would live on forever in the downstream applications.
{% endhint %}

### Option 2: Retention period to reduce feed size

If the objective of implementing a retention period is primarily to reduce the size of the feed, an effective retention period can be implemented by having the first page of the feed start from the first relevant record (instead of from the beginning of time). This approach is useful for simple cases where a CRON job is not desirable.

The approach can be implemented as follows: if the `@afterTimestamp` and `@afterId` parameters are **not** supplied to the RPDE endpoint (i.e. for the first page), use the query below to try to get the `@firstTimestamp` and `@firstId` and use these as default values:

```sql
-- Execute ONLY if @afterTimestamp and @afterId NOT provided
  SELECT @firstTimestamp = modified, @firstId = id
    FROM ...
   WHERE startDate >= @now AND state <> "deleted"
ORDER BY modified, id
   LIMIT 1
```

For the first page only (where `@afterTimestamp` and `@afterId` parameters are **not** supplied), these default values are included within the `WHERE` clause of the RPDE query as below, with a slight change to the operands such that **`id >= @firstId`** to ensure the default value itself is included in the feed.

For the first page, if no default values are returned, the RPDE query must exclude the `WHERE` clause as per the [specification](https://www.openactive.io/realtime-paged-data-exchange/#sql-query-example-for-timestamp-id), and return results from the beginning of time.

Hence the query either uses:

* For the first page: the default values supplied from the query above or otherwise returns results from the beginning of time
* For all other pages: the values supplied by the parameters

```sql
-- Include this WHERE clause only if @afterTimestamp and @afterId
-- are NOT provided (first page), and if default values are available
   WHERE (modified = @firstTimestamp AND id >= @firstId)
      OR (modified > @firstTimestamp)
-- Include this WHERE clause only if @afterTimestamp and @afterId
-- are provided (not first page)
   WHERE (modified = @afterTimestamp AND id > @afterId)
      OR (modified > @afterTimestamp)
-- If @afterTimestamp and @afterId not provided, and default values
-- are not available, do not include WHERE clause
ORDER BY modified, id
```

To further reduce the number of records in the feed, any record for an opportunity in the past can be rendered as `"deleted"` in the feed without any change to the `updated` value (note the record **must not** be removed from the feed). This means that records representing past opportunities are effectively frozen after they have occurred, and by default will live on forever in downstream applications. If the record is edited, the `modified` value must still be updated, at which point the record will be removed from downstream applications, to ensure historical accuracy.

{% hint style="info" %}
Alternative approaches for implementing this option are available in [this proposal](https://github.com/openactive/realtime-paged-data-exchange/issues/96). Feedback and thoughts very welcome.
{% endhint %}
