---
description: >-
  How to handle customers abandoning the booking before completing it, or
  cancelling it after finishing an order.
---

# Handling abandonment, cancellation, and errors

## Abandonment

A customer may start the flow of creating a booking but may not actually finalize it. In this instance the broker will use this endpoint to delete the orderQuote and release any lease being held.

### Request and Response

Create a route with the following structure `{baseUri}/order-quotes/{uuid}`

The broker will make an empty `DELETE`request to this endpoint which you should respond to with an empty request carrying a 204 status if successful. You should remove any leases made associated with the booking.

??? postman to check endpoint valid ???

### Error handling

Ensure that an error is returned if a request is made using this endpoint to a UUID that does not exist.

??? postman to check error returns ???

## Order cancellation

Once an order has been completed the customer may with to cancel it, you should create this endpoint to enable that.

### Request

Ensure this route is present in your booking system `/order/{uuid}`

This endpoint will recieve a PATCH request, as shown in this example:&#x20;

<details>

<summary>Request example</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "Order",
  "orderedItem": [
    {
      "@type": "OrderItem",
      "@id": "https://example.com/api/orders/e11429ea-467f-4270-ab62-e47368996fe8#/orderedItem/1234",
      "orderItemStatus": "https://openactive.io/CustomerCancelled"
    }
  ]
}
```

</details>

??? Postman link for valid endpoint ???

??? Explain fields ???

Any orderItem omitted from the cancellation request must be ignored.

### Response

You should cancel the bookings for the orderItems in the request, and you should return a 204, even if the item has already been cancelled (the&#x20;

Return 204

NOTE: Idempotent, always return same

After delete C1/C2/B against UUID should return error

## Deleting an order

Could be that something goes wrong with order

Broker will call `PATCH /order/{uuid}`

Check input (error returns)

Leave a stub `deleted` record remaining for that UUID

Return 204

