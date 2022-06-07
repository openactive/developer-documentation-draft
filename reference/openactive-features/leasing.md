---
description: Reserving items during the booking journey
---

# Leasing

Customers may simultaneously attempt to book the same [Opportunity](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-opportunity) (e.g. space in an event or `Slot` of a facility), and hence contend with one another for a limited number of [Opportunities](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-opportunity). In order to ensure that items cannot be "stolen" from a [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer)'s "shopping basket" by another [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer), some [Booking Systems](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) use the concept of a Lease, a temporary reservation that provides a window during which a [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) can complete their customer journey and improves the user experience overall.

The design of this API supports the use of time-bound [Leases](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease). Using [Leases](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) is _recommended_, however they are not required, in order to ensure a low barrier of entry for implementors of the specification.

Individually at [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) and [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2), the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) can opt to create (or extend) a [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) for the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker). Whether they opt to do so may depend on the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system)'s capability to support [Leases](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease), or on the [Seller](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-seller)'s configuration of the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system).

At [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) an Anonymous Lease _may_ be created i.e. a [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) for a [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) that has not yet provided any personal details.

At [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2) a Named Lease _may_ be created i.e. a [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) for a [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) who has identified themselves via personal details and other additional details. Any existing [Anonymous Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-anonymous-lease) becomes a [Named Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-named-lease) when the customer's personal details are provided (even if the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) does not store them at this stage).

If a [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) is created at [**C1**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c1) and/or [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2) it is used during [**B**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-b). However, if no [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) is created, or if it had expires prior to the transaction being completed, the booking completes anyway provided sufficient inventory is available.

It is _recommended_ that if the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) supports leasing, it also guarantees the price of each leased item for the duration of the [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease), so that the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) is guaranteed to pay the price they have seen during [**C2**](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-c2).

The specification is designed to allow [Brokers](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) to be agnostic to [Leases](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease): should a [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) be upgraded to support these, it can do so without affecting the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker)'s implementation.

By design, then, the use of [Leases](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) is largely transparent to the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker). However, the [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) _must_ provide information on the [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) to help improve the user experience of the customer journey as follows: when a [Lease](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-lease) is created, a `Lease` object _must_ be returned in the `OrderQuote`.

[EXAMPLE 2](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#example-2-example-of-lease): Example of lease

```
"lease": {
  "@type": "Lease",
  "leaseExpires": "2018-10-01T11:00:00Z"
}
```

The `leaseExpires` _may_ be used to inform the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) of the time they have remaining.
