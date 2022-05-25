---
description: An overview of the endpoints that make up the Open Booking API
---

# API endpoints

There are 10 API endpoints in the Open Booking API, 7 are required but you might need to implement all 10 depending on how your booking system works. This section will give an overview of all of them.

First we will look at the basic journey a customer will take when making a booking through a broker and the endpoints you will need to implement. This basic journey is called the **Simple Booking Flow**.&#x20;

We will then take a look at the endpoints for handling abandonment, cancellation, and errors, and an extension to the Simple Booking flow you may need to implement called the **Approval Booking Flow**.&#x20;

## Simple booking flow

The Open Booking API is a set of endpoints that allow a broker to create a booking in your system for a customer. The simple booking flow (represented in the diagram below) shows a customers journey and how that interacts with the endpoints you'll be creating (C1, C2 and B).&#x20;

![](<../../.gitbook/assets/image (1) (1).png>)

1. The broker generates a UUID which is used to uniquely identify the order the customer wishes to make.
2. After "**1. Select**" the broker calls the booking system through it's API to confirm the price (the price shown in the booking system opportunity feeds) and the availability of the selected items. Any details that the customer needs to supply to complete the booking are also specified. This corresponds to the API endpoint checkpoint 1 (C1).
3. After "**2. Identify**" the broker calls the booking system to reconfirm the price and availability of the selected items using all personal details required by the booking system in C1. Explicit consent for any terms and conditions is also captured at this stage if required. This corresponds to the API endpoint checkpoint 2 (C2).
4. After "**3. Book and Pay**", the broker first _pre-authorizes_ the payment for the total order price (if required by the payment provider), then confirms the booking with the booking system. This confirmation is called the Book (B) stage. Once the order is made the payment is captured, the broker then generates invoices, and sends the customer notifications.
5. The booking system adds the order to an orders feed specific to the broker, which the broker fetches periodically for updates, including those updates resulting from cancellations.&#x20;

### Endpoints

#### OrderQuote Creation C1

`PUT /order-quote-templates/:uuid`

This is the first endpoint you will add to your booking system. The broker calls it with JSON containing an `OrderQuote` object with the requested items from your feed for the order. If successful you will return a similar `OrderQuote` with additional information about the items ordered.

#### OrderQuote Creation C2

`PUT /order-quotes/:uuid`

This endpoint is similar to C1 but the broker adds information about the customer to the `OrderQuote`. If the requested items can be purchased by the customer you will again respond with an `OrderQuote` with additional information the broker needs before authorizing payment with the customer.

#### Order Creation B

`PUT /order-quotes/:uuid`

This is where you will create the booking in your system. The broker will call this endpoint with a JSON `Order` object based on the `OrderQuote` from C1/C2, to which will you responsd with the successful `Order`, or an error if the booking was not successful.

## Handling abandonment, cancellation and errors

The simple booking flow explained above outlines the 'happy' path for a customer. Sometimes a customer will abandon an order, experience connectivity issues to your booking system, or wish to cancel their order. The Open Booking API has endpoints to handle these scenarios, outlined below.

### Order quote deletion

`DELETE /order-quotes/:uuid`

A customer may abandon or cancel their order before it is complete, in which case the broker will call this endpoint. The broker makes an empty request made to this endpoint, you delete the `OrderQuote` from your booking system and return an empty 204 no content response.

### Order cancellation

`PATCH /orders/:uuid`

A customer may want to cancel and order after placing it. A PATCH request can be made to this endpoint with the `Order` and `OrderItem`s to be cancelled. On success, a status code 204 response **must** be returned, without any body.&#x20;

### Order deletion

`DELETE /orders/:uuid`

Sometimes there is a fatal error during the booking flow. Deleting an `Order` allows for the whole booking flow to be reversed.

A DELETE request is made and the corresponding `Order` **must** be marked as `deleted` in the orders feed.

## Approval booking flow

Depending on your booking system and the sellers that use it, you may want to enable them to approve orders before the booking is confirmed and paid for by the customer. This diagram below outlines this flow.&#x20;

![](<../../.gitbook/assets/image (2).png>)

### Order proposal creation (P)

`PUT /order-proposals/:uuid`

A Customer submits there details to confirm there booking, this process sends an `OrderProposal` in a PUT request that returns the submitted `OrderProposal` with an `orderProposalStatus` of `https://openactive.io/AwaitingSellerConfirmation`.&#x20;

### Order proposal approved (A)

`GET /orders-rdpe`

This endpoint is the same as the Orders Feed endpoint discussed below (link). If an order is approved it will be located in the orders feed by the broker with an approved `orderProposalStatus`.&#x20;

### Order proposal update

`PATCH /order-proposals/:uuid`

This endpoint accepts a simple PATCH containing either or both of `orderProposalStatus` and `orderCustomerNote`. This endpoint is only used to return the `orderProposalStatus` of `rejected` , i.e. when the seller rejects a customers booking.

## Retrieving orders

These endpoints allow the broker to see orders made in your booking system.

### Orders feed

`GET /orders-rdpe`

A GET request made to this endpoint enables the Broker to access a list of completed orders, so as to keep up-to-date and in sync with bookings on your system. This end point is required.

### Order status

`GET /orders/{uuid}`

Similar to the order feed endpoint except this GET request made by the broker is for a singular order.&#x20;

## Up next

Now you've got an understanding of what you are making, you can now check how you booking system database fits with the objects used in the endpoints.
