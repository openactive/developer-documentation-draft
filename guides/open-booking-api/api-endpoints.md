# API endpoints

## Simple booking flow

Here is the core flow that shows the end points you'll be creating.&#x20;

![](<../../.gitbook/assets/image (1).png>)

* The [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) generates an [Order UUID](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-order-uuid), which is used to uniquely identify the `OrderQuote` / `Order` throughout the process.
* After "**1. Select**" the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) calls the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) to confirm an indicative price and the availability of the selected items using an `OrderQuote`, and retrieve a total order price. Any details that the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) needs to supply to complete the booking are also specified. This is checkpoint [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) on the diagram above.
* After "**2. Identify**" the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) calls the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) to reconfirm the price and availability of the selected items using an `OrderQuote`, using all personal details required, and retrieve a total order price. Explicit consent for any terms and conditions is also captured at this stage if required. This is checkpoint [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2) on the diagram above.
* After "**3. Book and Pay**", the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) first _pre-authorizes_ the payment for the total order price (if required by the [Payment Provider](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-payment-provider)), then confirms the booking with the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) using an `Order` ([**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b) on the diagram above), and finally _captures_ the payment. The [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) then generates invoices, and sends the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) notifications.
* The [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) adds the `Order` to an [Orders feed](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-orders-feed) specific to the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker), which the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) reads to update its internal state, including those updates resulting from cancellations.

### Order quote creation (C1)

The [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) is an endpoint that accepts an `OrderQuote` without a `customer` object to check availability and confirm that the specific combination of `OrderItem`s requested can be purchased.

A PUT request to this endpoint of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) with an object of type `OrderQuote` will return an `OrderQuote` which represents a "dry run" of the booking, with the state of the `Order` nearly identical to the result of [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b) (except it excludes `customer`, `attendee`, `orderItemIntakeFormResponse` and the `payment` `identifier`), without any side effects (with the exception of optional leasing).

Note that in a simple implementation that does not manage [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) state, attendee details, or additional details, [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) and [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2) can be handled by the same underlying code that simply validates the `email` of the `customer` only if a `customer` value is supplied. Even for complex implementations, given the similarities between [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) and [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2), much of the business logic can be shared between both.

### Order quote creation (C2)

The [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2) is PUT endpoint that accepts an `OrderQuote` with a `customer` object to check availability and confirm that the specific combination of `OrderItem`s requested can be purchased.

A PUT request to this endpoint of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) with an object of type `OrderQuote` will return an `OrderQuote` which represents a "dry run" of the booking, with the state of the `Order` nearly identical to the result of [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b) (except it excludes `payment` `identifier`), without any side effects (with the exception of optional leasing).

Note that the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) _must not_ store any personal information of the `customer` provided for the `OrderQuote` past the expiry of any associated [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) created.

### Order creation (B)

A PUT request to the Order Creation endpoint of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) with an object of type `Order` will create the `Order` and complete the booking from the point of the view of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system).

## Handling abandonment, cancellation and errors

explain what this is used for and why is happens

### Order quote deletion

Deleting an `OrderQuote` releases any [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) associated with it, which the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) _should_ do as a courtesy if the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) has abandoned their journey. An OrderQuote Deletion request _must_ simply return a 204 success status in all cases (including cases where the [Booking Systems](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) has not implemented leasing), as the `OrderQuote` itself is ephemeral.

### Order cancellation

Cancellation of an `Order` may be requested by the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer). `OrderItem`s omitted from the PATCH request _must_ be ignored.

### Order deletion

Deleting an `Order` allows for the whole Booking Flow to be reversed for fatal error and testing scenarios.

The corresponding `Order` _must_ be marked as `deleted` in the [Orders feed](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-orders-feed), only if it is already present in the feed (which is unlikely during the [Simple Booking Flow](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#simple-booking-flow)).

When an `Order` is deleted the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) _should_ hard delete it as though it had not been created in the first place (with only a stub `deleted` record remaining, for the [Orders feed](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-orders-feed), if necessary), but _may_ soft delete it providing all related personal data is purged. The audit for the deletion is guaranteed to be stored in the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) in all cases.

The [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) _must_ return an `OrderAlreadyExistsError` in response to [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1), [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2), [**P**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-p) or [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b) if the request includes a soft deleted or stubbed [Order UUID](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-order-uuid).

The [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) _must not_ use Order Deletion for cancellation, but instead only use it in the unusual case of a fatal error or testing. An appropriate approach for cancellation is defined [below](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#order-cancellation).

## Approval booking flow

This may fit your system if the seller want's to approve bookings

![](<../../.gitbook/assets/image (2).png>)

### Order proposal creation (P)

[**P**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-p) is a PUT endpoint that creates and returns the submitted `OrderProposal` with an `orderProposalStatus` of `https://openactive.io/AwaitingSellerConfirmation`. The details submitted are identical to those required for [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b) with the addition of an optional `lease`.

### Order proposal update

The endpoint accepts a simple PATCH containing either or both of `orderProposalStatus` and `orderCustomerNote`.

## Up next

Now you've got an understanding of what you are making we can start with creating your first endpoint - OrderQuote creation C1
