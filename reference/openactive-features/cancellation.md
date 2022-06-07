---
description: Support for cancellation, both triggered by the Customer and by the Seller
---

# Cancellation

Either the whole `Order` or individual `OrderItem`s within the `Order` may be cancelled after [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b), however `OrderItem`s cannot be added or removed inside an existing `Order` past this point, within this version of the specification.

The cancellation may be requested by either the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) or the [Seller](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-seller), and results in an updated `Order` item on the [Orders feed](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-orders-feed). Once a cancellation status is placed on an `OrderItem` within an `Order` in the [Orders feed](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-orders-feed) it is assumed by the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) to have been processed, and it _must not_ be reversed. A new `Order` will need to created to reinstate the booking.

## Refund after the opportunity has occured

## Customer requested cancellation

## Seller requested cancellation

