# Booking

A PUT request to the Order Creation endpoint of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) with an object of type `Order` will create the `Order` and complete the booking from the point of the view of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system).

Add an endpoint `{baseUri}/orders/{uuid}`

Here's what a request will look like:

REQUEST

As you can see it is very similar to C2

Explain any differences

Happy path

* As before check the request is valid
* Do the logic so this actually makes a booking in your system
* Release any leases

Error handling - user error

Error handling - server error

