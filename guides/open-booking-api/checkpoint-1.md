# Checkpoint 1

Now we have an understanding of how the booking flow works make our first endpoint

Choose a base URL e.g. `/openactive/api`

Instructions to add this to `endpointUrl` in your dataset site, this way brokers can discover your booking API

Create an endpoint for C1 `PUT {baseUri}/order-quote-templates/{uuid}`

A broker will call this endpoint after a user selects an opportunity from your feed

Sidebar: reminder of what an "opportunity" is

Change `endpointUrl` in postman and call, the 200 test should pass, but the rest will fail

C1 acts as a "dry-run" of the order to confirm the details of the opportunity, your system will return a response that looks broadly similar to a finished booking

Here's what a request will look like:

REQUEST

Explain fields

* @context/@type
* brokerRole/broker - identifies the broker, eventually want to check against auth
* seller
* orderItem
  * Idenitifies the item/offer from your feed
  * explain fields

Write the logic to check the request is well-formed - table of what should be checked

For now just return a blank 409 for poorly formed request - we will do error handling later - run postman for error

Now let's build up your response

Here is what your response will eventually look like:

RESPONSE

Expand fields

Lease field - Should this be persisted?&#x20;

* This can be stateless but if your system operates a lease system and it makes sense for you to put the lease here, you will need to set a lease on the opportunity and return

Error handling - user error

Error handling - server error
