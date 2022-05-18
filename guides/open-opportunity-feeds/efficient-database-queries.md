# Feed performance

My feed is slow!

You should be aiming for <1s (ideally lower) at 500 items/page

It's likely running a complex query with joins on every feed page request is slower than this

Strategies for coping with this:

* Partial item caching + on demand JSON production
* Full JSON caching
* HTTP caching + CDN
* Removing items from the feed after a retention period

## Caching

So far we have constructed the JSON response for each of our feed pages on every request. This means:

* Even if our feed hasn't changed, if a new feed consumer makes a request we have to run the queries again.
* Every consumer has to wait for potentially expensive queries to run, introducing latency.

If you can get these queries under 1 second then great!

It might still be worth considering implementing at least partial caching, because a cached feed can stay fast as your database grows.

Downside is more storage used, and updates are more complex, however storage >>>> cheaper than compute

## A partial caching table

Make a new table&#x20;

One row for each item in the feed

Cache the required fields of all the relevant tables for the item

EXAMPLE HERE

When associated tables update, have them fire database or application triggers to update the cache table

Denormalize (that is replicate) data for each row. ie if multiple SessionSeries have the same location, copy the location fields to every relevant SessionSeries item row

When a feed request is made, you only need to select on one table

Create the JSON objects on request

## Full JSON caching&#x20;

If we are caching all the relevant information, why not go one step further and just cache the JSON for every item

Table with columns id, modified, ..., data: pre build JSON

You just query by changeNumber, stitch together the items array

Very little to do on request

## How should I implement updates to the cache table?

* Application triggers - very flexible, but easy to do it wrong
* Database triggers - less familiar for most people, but database enforces them so harder to introduce bugs
* Materialized view - advanced, not possible in every DB, but you can just use the query you already have and rely on the database to materialize it for you.

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
