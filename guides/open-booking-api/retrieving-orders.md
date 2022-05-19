# Retrieving orders

## Order feed

Broker and Brokers customers will want to be able to access a list of completed orders, so as to keep up-to-date and in sync with bookings on your system. This end point is required.

Broker will call `GET /orders-rpde`

Add endpoint

EXAMPLE RESPONSE

Make sure response includes correct fields and the fields are limited to only what is required.

Discuss accepted offer field

Postman link

## Order status

Similar to order feed - but this request is for a single opportunity. This end point is recommended but not required.

Broker will call `GET /orders/{uuid}`

Add endpoint

EXAMPLE RESPONSE in accordion

Make sure all fields are present

Postman link
