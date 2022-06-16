---
description: Reserving items during the booking journey
---

# Leasing

Customers may simultaneously attempt to book the same Opportunity (e.g. space in an event or `Slot` of a facility), and hence contend with one another for a limited number of Opportunities. In order to ensure that items cannot be "stolen" from a Customer's "shopping basket" by another Customer, some Booking Systems use the concept of a Lease, a temporary reservation that provides a window during which a Customer can complete their customer journey and improves the user experience overall.

The design of this API supports the use of time-bound Leases. Using Leases is _recommended_, however they are not required, in order to ensure a low barrier of entry for implementors of the specification.

Individually at **C1** and **C2**, the Booking System can opt to create (or extend) a Lease for the Broker. Whether they opt to do so may depend on the Booking System's capability to support Leases, or on the Seller's configuration of the Booking System.

At **C1** an Anonymous Lease _may_ be created i.e. a Lease for a Customer that has not yet provided any personal details.

At **C2** a Named Lease _may_ be created i.e. a Lease for a Customer who has identified themselves via personal details and other additional details. Any existing Anonymous Lease becomes a Named Lease when the customer's personal details are provided (even if the Booking System does not store them at this stage).

If a Lease is created at **C1** and/or **C2** it is used during **B**. However, if no Lease is created, or if it had expires prior to the transaction being completed, the booking completes anyway provided sufficient inventory is available.

It is _recommended_ that if the Booking System supports leasing, it also guarantees the price of each leased item for the duration of the Lease, so that the Customer is guaranteed to pay the price they have seen during **C2**.

The specification is designed to allow Brokers to be agnostic to Leases: should a Booking System be upgraded to support these, it can do so without affecting the Broker's implementation.

By design, then, the use of Leases is largely transparent to the Broker. However, the Booking System _must_ provide information on the Lease to help improve the user experience of the customer journey as follows: when a Lease is created, a `Lease` object _must_ be returned in the `OrderQuote`.

Here is an example of a lease:

```
"lease": {
  "@type": "Lease",
  "leaseExpires": "2018-10-01T11:00:00Z"
}
```

The `leaseExpires` _may_ be used to inform the Customer of the time they have remaining.
