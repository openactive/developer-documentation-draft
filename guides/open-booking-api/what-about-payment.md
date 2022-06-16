---
description: >-
  Payment is a key part of making a booking, but the way you implement your
  payment is not part of this guide.
---

# What about payment?

Due to the variety of business models available in the OpenActive ecosystem, conformance to this guide requires that the Payment Provider be distinct from your Booking System. You **may** separately expose your own native payment functionality as if it were a payment provider, however, its use **must** be optional.

Although the details of payments and refunds are not in the scope of this guide, a single `Payment` object is included in the `Order` in the `payment` property to ensure payments are fully traceable in case of audit.

### Up next

In the same way you've considered your own database and payment setup and how that will interact with your open booking API implementation, it is also worth considering the Sellers using your system.&#x20;
