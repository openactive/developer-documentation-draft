---
description: An overview of the endpoints that make up the Open Booking API
---

# API endpoints

## Simple booking flow

The Open Booking API is a set of endpoints that will enable a customer to create a booking in your system. The simple booking flow (represented in the diagram below) shows a customers journey and how that interacts with the endpoints you'll be creating (C1, C2 and B).&#x20;

![](<../../.gitbook/assets/image (1) (1).png>)

1. The Broker (??? LINKS TO ACTOR DEFINITIONS) generates an Order UUID (??? LINK TO REFERENCES) which is used to uniquely identify the `OrderQuote` / `Order` throughout the process.
2. After "**1. Select**" the Broker calls the Booking System to confirm an indicative (??? indicative === price gathered from the feed ???) price and the availability of the selected items using an `OrderQuote`, and retrieve a total order price. Any details that the Customer needs to supply to complete the booking are also specified. This is checkpoint 1 (C1).
3. After "**2. Identify**" the Broker calls the Booking System to reconfirm the price and availability of the selected items using an `OrderQuote`, using all personal details required, and retrieve a total order price. Explicit consent for any terms and conditions is also captured at this stage if required. This is the endpoint called checkpoint 2 (C2).
4. After "**3. Book and Pay**", the Broker first _pre-authorizes_ the payment for the total order price (if required by the Payment Provider), then confirms the booking with the Booking System using an `Order` . This confirmation is the endpoint called Book (B). Once the order is made the payment is captured, the Broker then generates invoices, and sends the Customer notifications.
5. The Booking system adds the `Order` to an Orders feed specific to the Broker, which the Broker reads to update its internal state, including those updates resulting from cancellations.

### Order quote creation without customer (C1)

C1 is an endpoint that accepts an `OrderQuote` **without** a `customer` to check availability and confirm that the specific combination of `OrderItem`s requested can be purchased.

A PUT request made to C1 with an object of type `OrderQuote` will return an `OrderQuote` which represents a "dry run" of the booking.

### Order quote creation with customer (C2)

C2 is an endpoint that accepts an `OrderQuote` **with** a `customer` object to check availability and confirm that the specific combination of `OrderItem`s requested can be purchased.

A PUT request to this endpoint with an object of type `OrderQuote` will return an `OrderQuote` which represents a "dry run" of the booking. C1 and C2 are very similar endpoints, except that the `customer` object is used in C2.

### Order creation (B)

B is an endpoint that accepts an `Order`  via a PUT request. If successful this request will create an `Order` in your Booking System and complete the booking process.&#x20;

## Handling abandonment, cancellation and errors

The simple booking flow explained above outlines the 'happy' path for a customer. Sometimes a customer will abandon an order, experience connectivity issues to your booking system, or wish to cancel their order. The Open Booking API has endpoints to handle these scenarios, outlined below.

### Order quote deletion

A customer may abandon or cancel their order before it is complete, in this case the broker will call this endpoint. A DELETE request made to this endpoint **must** simply return a 204 success status.

??? INSERT LINK TO DISCUSS LEASE ???

### Order cancellation

A customer may want to cancel and order after placing it. A PATCH request can be made to this endpoint with the `Order` and `OrderItem`s to be cancelled. On success, a status code 204 response **must** be returned, without any body.&#x20;

### Order deletion

Sometimes there is a fatal error during the booking flow. Deleting an `Order` allows for the whole booking flow to be reversed.

A DELETE request is made and the corresponding `Order` **must** be marked as `deleted` in the orders feed (LINK).

## Approval booking flow

Depending on your booking system and the sellers that use it, you may want to enable them to approve orders before the booking is confirmed,and paid for by the customer. This diagram below outlines this flow.&#x20;

![](<../../.gitbook/assets/image (2).png>)

### Order proposal creation (P)

A Customer submits there details to confirm there booking, this process sends an `OrderProposal` in a PUT request that returns the submitted `OrderProposal` with an `orderProposalStatus` of `https://openactive.io/AwaitingSellerConfirmation`.&#x20;

### Order proposal approved (A)

This endpoint is the same as the Orders Feed endpoint discussed below (link). If an order is approved it will be located in the orders feed by the broker with an approved `orderProposalStatus`.&#x20;

### Order proposal update

This endpoint accepts a simple PATCH containing either or both of `orderProposalStatus` and `orderCustomerNote`. This endpoint is only used to return the `orderProposalStatus` of `rejected` , i.e. when the seller rejects a customers booking.

## Retrieving orders

These endpoints allow the broker to see orders made in your booking system.

### Orders feed

A GET request made to this endpoint enables the Broker to access a list of completed orders, so as to keep up-to-date and in sync with bookings on your system. This end point is required.

### Order status

Similar to the order feed endpoint except this GET request made by the broker is for a singular order.&#x20;

## Up next

Now you've got an understanding of what you are making, you can now check how you booking system database fits with the objects used in the endpoints.
