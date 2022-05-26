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

#### OrderQuote creation (C1)

`PUT /order-quote-templates/:uuid`

This is the first endpoint you will add to your booking system. The broker calls it with JSON containing an `OrderQuote` object with the requested items from your feed for the order. If successful you will return a similar `OrderQuote` with additional information about the items ordered.

#### OrderQuote creation (C2)

`PUT /order-quotes/:uuid`

This endpoint is similar to C1 but the broker adds information about the customer to the `OrderQuote`. If the requested items can be purchased by the customer you will again respond with an `OrderQuote` with additional information the broker needs before authorizing payment with the customer.

#### Order creation (B)

`PUT /order-quotes/:uuid`

This is where you will create the booking in your system. The broker will call this endpoint with a JSON `Order` object based on the `OrderQuote` from C1/C2, to which will you responsd with the successful `Order`, or an error if the booking was not successful.

## Handling abandonment, cancellation and errors

The simple booking flow explained above outlines the 'happy' path for a customer. Sometimes a customer will abandon an order, experience connectivity issues to your booking system, or wish to cancel their order. The Open Booking API has endpoints to handle these scenarios.

#### Order quote deletion

`DELETE /order-quotes/:uuid`

A customer may abandon or cancel their order before it is complete, in which case the broker will call this endpoint. The broker makes an empty request made to this endpoint, you delete the `OrderQuote` from your booking system and return an empty success response.

#### Order cancellation

`PATCH /orders/:uuid`

A customer may want to cancel and order after placing it. The broker will make a request to this endpoint with the `Order` and `OrderItem`s to be cancelled. You cancel the order and return an empty success response.

#### Order deletion

`DELETE /orders/:uuid`

Sometimes there there is an error during the booking flow. Deleting an `Order` allows for the whole booking flow to be reversed.

The broker makes an empty request to this endpoint and you mark the corresponding `Order` as deleted.

## Approval booking flow

You may want to be able to approve bookings before they are completed, that is before they reach  stage B (Book) of the simple booking flow. This might be because you have a multiple seller system and some or all of your sellers want to approve bookings before they are finalised.

You can handle this scenario with one additional endpoints and some adjustments to the C2 and orders feed (discussed below) endpoints. This is called the Approval Booking flow and fits in the simple booking flow as shown in the following diagram.

![](<../../.gitbook/assets/image (2).png>)

### Endpoints

#### Changes to OrderQuote Creation (C2)

This endpoint is the same as in the simple booking flow, but for items that require approval you will add an error informing the broker that approval is required. &#x20;

#### Order proposal creation (P)

`PUT /order-proposals/:uuid`

A customer submits their details to confirm a booking, the broker sends an `OrderProposal` to this endpoint with the same information as the Book (B) endpoint. You mark the items in the order for seller approval in your booking system and return the `OrderProposal`with an "awaiting seller confirmation" status.

#### Order proposal update

`PATCH /order-proposals/:uuid`

This optional endpoint is used by the broker to update the proposal with either a message from the customer, or a change to the proposal status. You will update the proposal with the update and return an empty response.

## Retrieving orders

These endpoints allow the broker to see orders made in your booking system.

### Orders feed

`GET /orders-rdpe`

This is an RDPE feed similar to your opportunity that will return an up to date list of all `Order` and `OrderProposal` for the authenticated broker (authentication is covered later in this guide).

You will update this feed if there are any changes to an `Order` or `OrderProposal` and the broker will check it regularly so that they can keep the customer up to date. The feed also includes a record of any orders deleted by the broker through the order deletion endpoint.

### Order status

`GET /orders/{uuid}`

Similar to the order feed endpoint except this request return the data for a single order.&#x20;

## Up next

Now we know all the endpoints in an Open Booking API, it's tempting to get started implementing them. However, it will be useful to first consider how the OpenActive data model (`Order` , `OrderQuote`, etc.) maps to the data in your existing booking system and any changes you might need to make.
