---
description: Permitting bookings where payment is accepted upon arrival
---

# Prepayment

If the _optional_ `prepayment` feature of this specification is supported by the Booking System, the following logic _must_ be applied by the Booking System to populate the `prepayment` value of `totalPaymentDue` in the `OrderQuote` response of **C1** or **C2:**

| acceptedOffers across OrderItem                                                                                                                                                | Booking System sets prepayment value of totalPaymentDue |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------- |
| _Any_ `prepayment` values are either `https://openactive.io/Required` or omitted where `price` values are also not `0`                                                         | `https://openactive.io/Required`                        |
| _At least one_ `prepayment` value is `https://openactive.io/Optional` and the rest are either `https://openactive.io/Unavailable` or omitted where `price` values are also `0` | `https://openactive.io/Optional`                        |
| _In all cases_ either `prepayment` value is `https://openactive.io/Unavailable` or `price` value is `0`                                                                        | `https://openactive.io/Unavailable`                     |

If the `prepayment` property is not supported by the Booking System, the `prepayment` property _must_ simply be omitted in all cases, however the Booking System _must_ still handle the payment error conditions.

The following logic _must_ be applied by the Broker to determine whether to initiate taking payment from the Customer, and applies whether or not the Booking System supports `prepayment`:

| totalPaymentDue                                                                     | Broker initiates payment                                                                                                                                                                                                 |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `prepayment` is `https://openactive.io/Required`, or omitted and `price` is not `0` | Always                                                                                                                                                                                                                   |
| `prepayment` is `https://openactive.io/Optional`                                    | The [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) _may_ be given the option, at the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker)'s discretion |
| `prepayment` is `https://openactive.io/Unavailable` or `price` is `0`               | Never                                                                                                                                                                                                                    |

If payment is not to be initiated based on the above:

* The Broker _must_ communicate to the Customer that they are simply reserving a space without payment.
* The Broker _must not_ accept payment details, attempt payment pre-authorisation, or attempt payment capture.
* The Broker _must not_ include the `payment` property in the request to **B** or **P**.
* The Booking System therefore _must not_ include the `payment` property in the response from **B** or **P**, and _must_ proceed to create a payment-free `Order` or `OrderProposal`.

## Free opportunities

Free Opportunities, defined as those with at least one applicable `Offer` with a `price` of `0`, _must_ follow the same workflow as paid Opportunities.

For `Offer`s with a `price` of `0`:

* The `prepayment` property of the `Offer` _must_ be either `https://openactive.io/Unavailable` or unspecified.
* The `Offer` _may_ not include `priceCurrency`.
* If a `priceCurrency` is not available then Brokers _should_ communicate "Free" or equivalent when referencing a zero price.

When an `Order` or `OrderProposal` involves exclusively Free Opportunities the `totalPaymentDue` will be `0` regardless of the `prepayment` property values. In this case, no interaction with a Payment Provider is required, the `payment` property _may_ be omitted at **C1** and **C2** (depending on Payment Reconciliation Detail Validation), and _must_ be omitted at **B** or **P**.

## Payment error conditions

At **B** or **P** the Booking System _must_ produce an error response in the following cases:

* If the `payment` property is expected in the request (as within `totalPaymentDue`: `prepayment` is `https://openactive.io/Required`, or if `prepayment` not supported, `price` is not `0`) but not provided, then a `MissingPaymentDetailsError` _must_ be returned.
* If the `payment` property is provided but not expected in the request (as within `totalPaymentDue`: `prepayment` is `https://openactive.io/Unavailable` or `price` is `0`), then an `UnnecessaryPaymentDetailsError` _must_ be returned.
* If the `payment` property does not include `identifier`, then an `IncompletePaymentDetailsError` _must_ be returned.
* If the `payment` details supplied (e.g. `accountId`) are not considered valid for reconciliation by the Booking System then an `InvalidPaymentDetailsError` _must_ be returned.
