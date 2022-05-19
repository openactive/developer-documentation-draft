# Handling abandonment, cancellation, and errors

## Abandonment

Could be that broker customer abandons

Broker will call `DELETE /order-quotes/{uuid}`

Add endpoint

Check input (error returns)

Drop any leases

After delete C1/C2/B against UUID should return error

## Order cancellation

Could be that broker customer abandons

Broker will call `PATCH /order/{uuid}`

Add endpoint

Check input (error returns)

Cancel order

Return 204

NOTE: Idempotent, always return same

After delete C1/C2/B against UUID should return error

## Deleting and order

Could be that something goes wrong with order

Broker will call `PATCH /order/{uuid}`

Add endpoint

Check input (error returns)

Leave a stub `deleted` record remaining for that UUID

Return 204

