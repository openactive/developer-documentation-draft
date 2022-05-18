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

## A partial caching table

Make a new table&#x20;

One row for each item in the feed

Cache the required fields of different tables&#x20;

When associated tables update, have them fire database or application triggers to update the cache table

Denormalize (that is replicate) data for each row. ie if multiple SessionSeries have the same location, copy those changes to every relevant SessionSeries item row

When a feed request is made, you only need to select on one table
