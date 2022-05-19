# Checkpoint 2

Checkpoint 2 (C2) is used to confirm the exact details of an opportunity + customer

Here is what the request will look like:

REQUEST

It is the same as C1 except there is a customer field

Add an endpoint `{baseUri}/order-quotes/{uuid}`

SIDEBAR: It's likely you can share logic between C1 and C2

As before check validity of request

Here's what your response will look like

RESPONSE

Explain additional fields

NOTE: User data must not be persisted beyond lease

Lease field - Should this be persisted?&#x20;

* This can be stateless but if your system operates a lease system and it makes sense for you to put the lease here, you will need to set a lease on the opportunity and return

Error handling - user error

Error handling - server error

