---
description: >-
  Before you start making endpoints, it's sensible to consider the objects being
  used in the requests and responses and how they map to your database.
---

# Database structure



![Schema to support Open Booking API](<../../.gitbook/assets/image (1).png>)

#### Order

A table representing the atomic successfully created `Order` which is the result of Booking endpoint B. This table likely also includes the `Customer`. This table is important as it is used to generate the data returned by the Orders Feed endpoint.

#### OrderItem

A table representing an individual booking of an `Opportunity` within an `Order`. This table likely also includes the guest checkout `attendee` details if these are supported. Existing "booking" or "attendee" tables may serve this purpose. Each `OrderItem` represents a booked space of a bookable Opportunity and Offer pair.

#### Opportunity

One or many tables that represent the different types of opportunities. ??? Link or explain opportunities here ???

#### Offer

A table or other data structure that represents the available Offers within each Opportunity

#### Seller

A table that represents Sellers, organizations or individuals who organize the events or provide the facilities. Existing "organisation" tables may serve this purpose. This is only required if the booking system is multi-tenancy within the same database (i.e. it supports multiple Sellers).

#### AuthToken

This is likely to be managed by the authentication library, e.g. as JWT.

#### Booking Partner

This is likely to be managed by the authentication library, e.g. as a table of OAuth Clients.
