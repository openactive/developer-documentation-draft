---
description: Accepting payment and/or handling free bookings
---

# Payment & Free Bookings

Due to the variety of business models available in the OpenActive ecosystem, conformance to this specification requires that the Payment Provider be distinct from the Booking System for which this API is defined. The Booking System _may_ separately expose its own native payment functionality as if it were a Payment Provider, however its use _must_ be _optional_.

Although the details of Payments and Refunds are not in the scope of this documentation, a single `Payment` object is included in the `Order` in the `payment` property to ensure payments are fully traceable in case of audit.

## Virtual payments

Note that this `Payment` is permitted to represent a virtual Payment consolidating a number of actual **Payments** to allow for a wide variety of payment methods. In the case where the Seller is paid monthly, the Payment may refer to a future invoice against which the Payment will eventually be paid.

## Basic Payment

A unique reference to this Payment **must** be included in the `Payment` `identifier` of the `Order` sent to the Booking System. This `Payment` `identifier` _must_ be uniquely resolvable for audit purposes.

The `accountId` of the `Payment` _should_ be provided to reference the specific account within the Payment Provider that is used for reconciliation purposes.

The `paymentProviderId` of the `Payment` _should_ be provided to reference the specific Payment Provider that is used.

The `name` of the `Payment` _should_ be used to provide information about the source of the payment that can be stored in the Booking System, to help the Seller in discussions with the Customer (e.g. "AcmeBroker Points" or "AcmeBroker via Credit Card").

Here is an example of a basic payment:

```
"payment": {
  "@type": "Payment",
  "identifier": "12345678ABCD",
  "name": "Big Media Services",
  "accountId": "SN1593",
  "paymentProviderId": "STRIPE"
},
```

### **Payment reconciliation detail validation**

Payment reconciliation detail validation is required if any viable payment reconciliation route exists between the Broker and the Seller that necessitates more than just the `identifier` property of the `Payment` being included in **B**. For simplicity of implementation, such validation is required even if the `totalPaymentDue` is likely to be `0` or the `prepayment` value of the selected `Offer`s indicates no payment.

This ensures that a viable payment reconciliation route is available before the Customer starts to enter their data.

To achieve such validation, the `payment` property _must_ be included at **C1** and **C2** by the Broker in the `OrderQuote` request, including all relevant properties of `Payment` except the `identifier`.

If the `payment` details supplied (e.g. `accountId`) are not considered valid for reconciliation by the Booking System then a `InvalidPaymentDetailsError` _must_ be returned at **C1** and **C2**.

For the Booking System, the `payment` property at **C1** and **C2** is always considered optional, as a payment reconciliation route may not exist.

At **B** and **P** the `payment` property _must_ only be included if a payment is actually being processed. If payment details were not captured due to the `prepayment` value of the selected `Offer`, or if `totalPaymentDue` is `0` due to all `OrderItem`s being free of charge, the `payment` property _must not_ be included in the `Order` at **B**. In all other cases `payment` property _must_ be included in the `Order` at **B**.

For the simple case, if the Booking System does not implement properties other than the `identifier` property of the `Payment`, the `Payment` _must_ simply be reflected back in the response to **C1**, **C2**, **B** and **P** with only the `identifier` property included, and no implementation of the `InvalidPaymentDetailsError` response is required.

## Dynamic Payment

For fully dynamic pricing, where price is not known at the point of purchase, a `DynamicPayment` _must_ be used. Example use cases include volume-based pricing, where the price of items are calculated at the end of the month depending on the total volume booked during that period.

To use the `DynamicPayment` the following preconditions must be met:

* The Broker and the Seller _must_ have agreed an arrangement for dynamic pricing out-of-band, including which Opportunities and `Offer`s it applies to.
* Dynamic pricing cannot be mixed with standard pricing behaviour within the same `Order`, so all `OrderItem`s selected _must_ be applicable for dynamic pricing based on such agreements.
* The Opportunities that are selected for booking _must_ be bookable as in the standard flow.

To use `DynamicPayment` the booking flow _must_ be completed as normal with the following additional requirements:

* The `OrderQuote`, `OrderProposal` and `Order` in request, response, and in the subsequent Orders feed _must not_ include `totalPaymentDue` or `totalPaymentTax`, and `OrderItem`s _must not_ include `acceptedOffer` or `unitTaxSpecification`.
* The booking flow _must_ be completed _as if_ `prepayment` was set to `https://openactive.io/Unavailable` for the `acceptedOffer` for all `OrderItem`s.
* The `OrderQuote` / `OrderProposal` / `Order` submitted to **C1**, **C2** and **B** (and **P** if using approval) _must_ have a `payment` property that contains a `DynamicPayment`, which is consistent throughout the calls.
* If the `DynamicPayment` details supplied (e.g. `accountId`) are not considered either sufficient or valid for reconciliation by the Booking System then either `IncompletePaymentDetailsError` or `InvalidPaymentDetailsError` _must_ be returned by all calls, respectively.
* The Booking System _must not_ record any price against the `Order` or `OrderItem`s for reconciliation, as such reconciliation _must_ occur completely out-of-band.

The `accountId` of the `DynamicPayment` _must_ be used consistently in order to allow the Seller to reconcile Opportunities booked using a particular account.

The `name` of the `DynamicPayment` _should_ be used to provide information about the source of the payment that can be stored in the Booking System, to help the Seller in discussions with the Customer (e.g. "AcmeBroker Points" or "AcmeBroker Membership").

Note that the Booking System _may_ choose to validate the `DynamicPayment` details, as above, though this is not a requirement - it could simply accept any values provided.

Here is an example of a dynamic payment

```
"payment": {
  "@type": "DynamicPayment",
  "identifier": "12345678ABCD",
  "name": "MoveGB Membership",
  "accountId": "MOVE"
},
```



## Payment reconciliation identifiers

Although automated payment reconciliation is outside the current scope of this specification, a Booking System _may_ include identifiers suitable for payment reconciliation to be handled out-of-band with each `OrderItem` using the `additionalProperty` property.

In keeping with the Schema.org definition of `PropertyValue`, the `name` and `value` properties _must_ be provided within any given `PropertyValue`, and the Broker _should_ include these in any reconciliation process with the Seller.

Here is an example of `additionalProperty` for `OrderItem`

```
"additionalProperty": [
  {
    "@type": "PropertyValue",
    "name": "SiteID",
    "value": "ABC1234"
  },
  {
    "@type": "PropertyValue",
    "name": "DeptID",
    "value": "D89"
  }
],
```
