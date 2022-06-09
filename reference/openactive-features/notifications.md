---
description: Notifications of changes to a confirmed booking
---

# Notifications

### **Customer notice notifications**

The Seller may provide a notice to the Customer ahead of an Opportunity, for example to inform Customers that they are still short of a player in case any friends are interested in attending.

An `orderedItem` within the `OrderItem` of the Orders feed _may_ be updated to include a `customerNotice`. The Customer _must_ be notified with the contents of the `customerNotice` for the `OrderItem` if it differs from the value of `customerNotice` for that specific `orderedItem` that the Broker had previously stored (or if a new `Order` in the Orders feed includes a `customerNotice`).

Note that notifications that inform the Customer of substantive changes to the Opportunity _must_ be triggered by updating the Opportunity Data, which will trigger a Change of logistics notification, instead of using the `customerNotice` mechanism.

### **Change of logistics notifications**

The `orderedItem` within the `OrderItem` includes an `@id` reference to an Opportunity, which _must_ be able to be cross-referenced with other sources of Opportunity Data, for example an \[_RPDE_] open feed.

This specification requires the Orders feed to not include the full contents of the Opportunities within the `orderedItem`, and instead only includes the Opportunity properties `@type` and `@id`. Additionally the `orderedItem` property of the `OrderItem` is only required within the Orders feed if Replacement is supported.

Hence the Broker _must_ store the Opportunity references `@type` and `@id` at **B**, _must_ monitor the Opportunity Data open feeds for updates to the referenced information, and _must_ provide a notification to the Customer for substantive changes to the same Opportunity; specifically: any changes to the Opportunity `name`, `startDate`, `endDate`, `duration` or `meetingPoint` properties, and any changes to the `name`, `address` or `geo` properties within `location`.

### **Opportunity attendance updates**

When a Customer has been confirmed as attending an Opportunity that was booked via an `Order`, the Booking System _should_ update the `Order` within the Orders feed by setting `orderItemStatus` for the relevant `OrderItem` to `https://openactive.io/CustomerAttended`.

### **Other notifications**

The following properties within `Order`s in the Orders feed _may_ also be updated by the Booking System, and _must_ trigger the Broker to notify the Customer:

* `accessCode`, `accessPass` and/or `accessChannel` - A notification that the access details for the Opportunity have changed

The following properties within `Order`s in the Orders feed _may_ also be updated by the Booking System, and _must_ be used subsequently by the Broker for their internal operations:

* `additionalProperty` - The details to use for payment reconciliation have changed
