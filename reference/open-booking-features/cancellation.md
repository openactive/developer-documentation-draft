---
description: Support for cancellation, both triggered by the Customer and by the Seller
---

# Cancellation

Either the whole `Order` or individual `OrderItem`s within the `Order` may be cancelled after **B**, however `OrderItem`s cannot be added or removed inside an existing `Order` past this point, within this version of the specification.

The cancellation may be requested by either the Customer or the Seller, and results in an updated `Order` item on the Orders feed. Once a cancellation status is placed on an `OrderItem` within an `Order` in the Orders feed it is assumed by the Booking System to have been processed, and it _must not_ be reversed. A new `Order` will need to created to reinstate the booking.

### **Refund after the opportunity has occurred**

Although a cancellation may be requested before the Opportunity has occurred in order to trigger a refund, cancellation and refund after an Opportunity has occurred, or when an `OrderItem` has `orderItemStatus` set to `https://openactive.io/CustomerAttended` (e.g. when an attendee arrives early), is currently outside the scope of this specification.

Hence within this version of the specification any `OrderItem` `@id` submitted to the Order Cancellation endpoint _must_ reference an Opportunity with a `startDate` in the future.

Full and partial refunds after an Opportunity has occurred _must_ be handled out-of-band directly between the Broker and the Seller.

### **Customer requested cancellation**

![](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/customercancelled.png)

To cancel an existing `OrderItem`, the Broker _must_:

1. Use the latest state of the `Order` stored during the booking flow and updated via the Orders feed to determine which `OrderItem`s may be cancelled and refunded. If `orderItemStatus` is not yet set to `https://openactive.io/CustomerAttended`, and `allowCustomerCancellationFullRefund` is `true`, and either `latestCancellationBeforeStartDate` is unspecified and `startDate` is in the future, or `latestCancellationBeforeStartDate` subtracted from the Opportunity `startDate` is in the future, a PATCH against the `Order` including the `OrderItem` with `"orderItemStatus": "https://openactive.io/CustomerCancelled"` _should_ succeed.
2. Offer the Customer an interface to "Request Cancellation". Cancellation requested by the Customer cannot be guaranteed due to potentially opaque cancellation rules within the Booking System for exceptional cases, therefore it is important that expectations are set accordingly.
3. Inform the Customer of the refund being requested before they commit to cancellation, using the existing data available in the stored `Order` to communicate the `OrderItem`s that are included in the full refund request.
4. Submit a PATCH to the Order Cancellation endpoint for the `Order` including only the `@id` for each of the `OrderItem`s to be cancelled, with an `"orderItemStatus": "https://openactive.io/CustomerCancelled"` set on each. On success, a status code 204 response _must_ be returned, without any body. On failure due to opaque cancellation rules, a status code 400 response _must_ be returned, containing a `CancellationNotPermittedError` error including a `description` of the reason, which _must_ be communicated to the Customer. The PATCH request is atomic, and therefore the cancellation succeeds or fails entirely.
5. Process any refunds based on the resulting updates to the Orders feed, based on the updated `totalPaymentDue`. Successfully cancelled `OrderItem`s will have `orderItemStatus` set to `https://openactive.io/CustomerCancelled`. Note refunds _must not_ be processed except in response to updates in the Orders feed.

To cancel an entire `Order`, the PATCH request _must_ include `"orderItemStatus": "https://openactive.io/CustomerCancelled"` for each `OrderItem` within it.

Note that there is no provision for a Customer to cancel an `Order` after the cancellation window specified by `latestCancellationBeforeStartDate`. They may wish to do this out of politeness even though no refund is possible, with the benefit that their place may be taken by another Customer, which can be included in future versions of this specification.

**8.3.3 Seller requested cancellation**

![](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/sellercancelled.png)

`OrderItem`s cancelled by the Seller will have `orderItemStatus` set to `https://openactive.io/SellerCancelled` in the Orders feed, and will optionally include a `cancellationMessage`. Refunds _must_ be processed in response to the resulting updates in the Orders feed, and the Customer _must_ be notified with the `cancellationMessage` if provided.
