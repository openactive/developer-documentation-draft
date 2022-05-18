# Adding the rest of your data

You now have feed URLs, but they only return a few items

How do we include the rest of the data?

We could just remove the `LIMIT` but if we have more than a few hundred items that could inhibit performance, and put strain on your system

For similar reasons if a feed consumer has already checked your feed, we want them to be able to just get the updates rather than having to consume the whole feed

And if a consumer is only checking updates, we need to let them know an item has been removed

The solution OpenActive uses for this is Realtime Paged Data Exchange (RPDE).

## RPDE

Use reference implementation as an example

* Consumer visits base URL for the feed
* They see the least recently modified items and slurp them up
* They visit the URL in "next" to get the next page of items
* They repeat this until the "items" field is empty and the "next" URL is the same as the current page's URL
* After an interval they check the "next" URL for updates and repeat the above process

Advantages

* Paging limits individual query time
* Simple harvesting procedure
* Returning consumers only fetch updates
* When an item is updated or deleted it's modified timestamp will move it to the front of the feed, so returning consumers will see changes

## Important properties of RDPE

Rewrite from the spec:

* A continuous list of records that _must_ be sorted deterministically and chronologically (in the order they were updated). Either (i) ordered first by modified timestamp, and second by ID or (ii) ordered by an incrementing counter where records are assigned a new unique value on each update.
* Every record _must_ only be represented _once_ in this list at a given moment, distinguished by its unique ID, with its position in the list depending on when it was last updated. Records can freely move position in the list as they are updated.
* This deterministic ordering based on timestamp allows for pages of arbitrary size to be sent without concern for race conditions; if a record is updated during the transfer of a page it _must_ appear on a subsequent page (i.e. simply reappear further down the list).
* Pages are defined using a "next page URL", which _must_ contain enough information to identify a position in the list (e.g. by a "timestamp" and "ID" combination). It _must not_ reference a specific record, as that record can change position in the list.
* If a "next page URL" is not used to access the list, the first page _must_ be returned.
* If the consumer reaches the end of the list they consider themselves up-to-date at that moment, and can infrequently revisit the end of the list in order to retrieve further updates.
* If any record is added to the list or updated it _must_ remain in the list in perpetuity while it is in an `"updated"` state, or remain in the list for at least 7 days from the point in time at which it transitioned to a `"deleted"` state. This ensures that deletions are synchronised to data consumers (System 2).

**ITEMS MUST NEVER DISAPPEAR**

**IT IS NOT A STREAM OF UPDATES - ITEMS APPEAR ONLY ONCE, ONLY THEIR POSITION IN THE LIST CHANGES**

## Choosing an ordering strategy

There are 2 valid ordering strategies

Explain timestamp/id vs changeNumber

Prefer change number because it can avoid problems where updates are committed out of order

Sidebar: how can I implement change number? (Some DBs have rowversion, if using a cache table you could auto increment)

Say why you might use timestamp/id instead (it's sometimes easier)

## Making our feed an RDPE feed

First your feed endpoint needs to accept afterTimestamp/afterId or afterChangeNumber parameters

Then filter your query using these parameters

An extension to our example SQL query from earlier:

```
SELECT ... JOIN ... WHERE ... LIMIT 500
```

This might be slow if you need to join several tables. For now watch out for n+1 queries and/or reduce the `LIMIT` or make quick in some other way but don't worry if it is a little slow, we will come back to performance optimization and architecture later.

Change the "next" field you return with the feed URL for the next page. ie either with params `afterTimestamp={modified timestamp of last item returned}&afterId={id of last item returned}` or `afterChangeNumber={modified timestamp of last item returned}` .

You should have some output like this:

And when you visit the "next" URL you should see the next page of results.

Check that the last page of results works properly (explain what "properly" means)

Test your feed with the validator
