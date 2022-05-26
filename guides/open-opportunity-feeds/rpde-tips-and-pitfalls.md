# RPDE tips and pitfalls

If you've been following the guide while you implement you're RDPE feed you will have seen these pitfalls mentioned, but to reiterate, there are three pitfalls that you want to avoid when implementing your RDPE feed.

* Misreading or misunderstanding the ordering. The ordering is key for the RDPE to function in an accurate an performant way for data consumers. (??? LINK BACK)
* Items must not be removed from the feed. Data consumers need to know when an item has been deleted, so it's state should be changed to "deleted" but not actually removed from the feed (??? LINK BACK)
* Race conditions can cause a data consumer to temporarily fall out of sync, as you make concurrent updates to your feed you need to ensure that transactions are carried out so that your feed is never presented in the incorrect order. (??? LINK BACK)

#### 6.1 Common implementation issues <a href="#common-implementation-issues" id="common-implementation-issues"></a>

This specification is simple to implement, however such implementation requires precision to ensure that it is robust. The most common issues encountered during implementation are enumerated here:

* The [last page](https://openactive.io/realtime-paged-data-exchange/#last-page-definition) must have zero items, and a correct next URL that matches the current page's URL.
* The [query](https://openactive.io/realtime-paged-data-exchange/#sql-query-example-for-timestamp-id) must be correctly implemented with the parentheses in the correct positions.
* The [core concept](https://openactive.io/realtime-paged-data-exchange/#core-concept) must be understood, specifically the nature of the list of items where each item appears only once, and each item exists in perpetuity unless deleted as specified in [deleted items](https://openactive.io/realtime-paged-data-exchange/#deleted-items).
* The [`modified`](https://openactive.io/realtime-paged-data-exchange/#-item-) value (used for either the `afterTimestamp` or `afterChangeNumber` parameter) must always be updated when any data within the `data` property is updated. This can be achieved through e.g. [adding triggers](https://openactive.io/realtime-paged-data-exchange/#patterns-of-implementation) for related changes to update the modified value on the primary table.
* The [`modified`](https://openactive.io/realtime-paged-data-exchange/#-item-) value should match the format used in the `afterTimestamp` or `afterChangeNumber` parameters.
