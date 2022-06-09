# RPDE tips and pitfalls

If you've been following the guide while you implement you're RDPE feed you will have seen these mentioned, but to reiterate, these are three key pitfalls that you want to avoid when implementing your RDPE feed.

* Misreading or misunderstanding the ordering. The ordering is key for the RDPE to function in an accurate and performant way for data consumers.
* Items must not be removed from the feed. Data consumers need to know when an item has been deleted, so it's state should be changed to "deleted" but not actually removed from the feed.
* Race conditions can cause a data consumer to temporarily fall out of sync. As you make concurrent updates to your feed, you need to ensure that transactions are carried out so that your feed is never presented in the incorrect order.

### Common issues <a href="#common-implementation-issues" id="common-implementation-issues"></a>

The RDPE feed is relatively simple to implement. However, the implementation requires precision to ensure that it is robust. The most common issues encountered during implementation are:

* The last page in your feed must have zero items, and a correct next URL that matches the current page's URL.
* Your database query for retrieving records for the feed must be correctly implemented with the parentheses in the correct positions.
* The core concept of the RDPE feed must be understood, specifically the nature of the list of items where each item appears only once, and each item exists in perpetuity unless deleted as specified in deleted items.
* The modified value (used for either the `afterTimestamp` or `afterChangeNumber` parameter) must always be updated when any data within the `data` property is updated. This can be achieved by adding triggers for related changes to update the modified value on the primary table.
* The modified value should match the format used in the `afterTimestamp` or `afterChangeNumber` parameters.
