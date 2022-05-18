# RPDE tips and pitfalls

* Misreading/misunderstanding the ordering
* Items must not be removed from the feed
* Race conditions on updates affecting timestamps
  * First commit the transaction, then update the timestamps or change numbers after the transaction has been committed as an atomic operation, outside of a transaction, using `GETDATE()` or similar
  * Ensure the RPDE endpoint filters out all items with a "modified" date after 2 seconds in the past, to delay items appearing in the feed
  * If using the [Modified Timestamp and ID](https://www.w3.org/2017/08/realtime-paged-data-exchange/#modified-timestamp-and-id) ordering strategy, use a timestamp column with a high degree of accuracy (e.g. `datetime2` in SQL Server).



#### 6.1 Common implementation issues <a href="#common-implementation-issues" id="common-implementation-issues"></a>

This specification is simple to implement, however such implementation requires precision to ensure that it is robust. The most common issues encountered during implementation are enumerated here:

* The [last page](https://openactive.io/realtime-paged-data-exchange/#last-page-definition) must have zero items, and a correct next URL that matches the current page's URL.
* The [query](https://openactive.io/realtime-paged-data-exchange/#sql-query-example-for-timestamp-id) must be correctly implemented with the parentheses in the correct positions.
* The [core concept](https://openactive.io/realtime-paged-data-exchange/#core-concept) must be understood, specifically the nature of the list of items where each item appears only once, and each item exists in perpetuity unless deleted as specified in [deleted items](https://openactive.io/realtime-paged-data-exchange/#deleted-items).
* The [`modified`](https://openactive.io/realtime-paged-data-exchange/#-item-) value (used for either the `afterTimestamp` or `afterChangeNumber` parameter) must always be updated when any data within the `data` property is updated. This can be achieved through e.g. [adding triggers](https://openactive.io/realtime-paged-data-exchange/#patterns-of-implementation) for related changes to update the modified value on the primary table.
* The [`modified`](https://openactive.io/realtime-paged-data-exchange/#-item-) value should match the format used in the `afterTimestamp` or `afterChangeNumber` parameters.
