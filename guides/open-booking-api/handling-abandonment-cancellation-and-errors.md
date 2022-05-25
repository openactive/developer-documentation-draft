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

Once an order has been completed the customer may wish to cancel it, you should create this endpoint to enable that.

### Request

Ensure this route is present in your booking system `/order/{uuid}`

This endpoint will receive a PATCH request, as shown in this example:&#x20;

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

You should cancel the bookings for the orderItems in the request, and you should return a empty response with a 204 status, even if the item has already been cancelled.

??? Postman link for valid response ???

### Error handling

If the request being made to this endpoint does not align with the rules in your booking system you should return a 400 error stating that the cancellation is not permitted, as shown in this example:&#x20;

<details>

<summary>Example error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "CancellationNotPermittedError",
  "description": "The horse has already been fed, and cannot be put back in the box."
}
```

</details>

??? Postman link for valid error response ???

The `orderItemStatus` can only be a customer cancellation, any other status should return a 400 error to say the property is not allowed for the PATCH request.

<details>

<summary>Example error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "PatchNotAllowedOnProperty",
  "description": "Only 'https://openactive.io/CustomerCancelled' is permitted for this property."
}
```

</details>

??? Postman link for valid error response ???

## Deleting an order

If something goes wrong with the entire booking flow, either because of a fatal error or perhaps during testing this end point can be used.

### Request

Ensure this route is present in your booking system `/order/{uuid}`

An empty DELETE request will be made by the broker to this endpoint, at which point the order should be hard deleted within your system, with only a stub of the order remaining containing the UUID. Alternatively, you may want to soft delete the order and purge all personal data from it.

If the order is present in the orders feed (??? LINK), a topic we will come on to shortly, it must be marked as deleted.&#x20;

This request should only be used by the broker in cases of a fatal error or when testing, it is not meant for cancelling an order.

### Response

The response should be empty with a 204 status when a order is succesfully deleted in your system following the request.

### Error Handling

If the order is not recognised a 404 error should be returned, as shown in this example:&#x20;

<details>

<summary>Example error</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "NotFoundError",
  "description": "This Order does not exist."
}
```

</details>

{% hint style="info" %}
C1, C2, P, B endpoints should return `OrderAlreadyExistsError` in response to a request that includeds the UUID of a soft deleted, or hard deleted and stubbed UUID order.
{% endhint %}

## Up next

??? Auth linking sentence ???

