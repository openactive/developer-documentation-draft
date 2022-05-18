# RPDE tips and pitfalls

* Misreading/misunderstanding the ordering
* Items must not be removed from the feed
* Race conditions on updates affecting timestamps
  * First commit the transaction, then update the timestamps or change numbers after the transaction has been committed as an atomic operation, outside of a transaction, using `GETDATE()` or similar
  * Ensure the RPDE endpoint filters out all items with a "modified" date after 2 seconds in the past, to delay items appearing in the feed
  * If using the [Modified Timestamp and ID](https://www.w3.org/2017/08/realtime-paged-data-exchange/#modified-timestamp-and-id) ordering strategy, use a timestamp column with a high degree of accuracy (e.g. `datetime2` in SQL Server).
