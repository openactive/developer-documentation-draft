---
description: Accepting payment and/or handling free bookings
---

# Payment & Free Bookings

Due to the variety of business models available in the OpenActive ecosystem, conformance to this specification requires that the Payment Provider be distinct from the Booking System for which this API is defined. The Booking System _may_ separately expose its own native payment functionality as if it were a Payment Provider, however its use _must_ be _optional_.

Although the details of Payments and Refunds are not in the scope of this documentation, a single `Payment` object is included in the `Order` in the `payment` property to ensure payments are fully traceable in case of audit.

## Virtual payments

## Basic Payment

## Dynamic Payment

## Payment reconciliation identifiers



