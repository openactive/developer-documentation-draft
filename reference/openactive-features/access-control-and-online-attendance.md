---
description: Barcodes, PIN codes, and Online Video URLs
---

# Access Control & Online Attendance

For [Opportunities](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-opportunity) that require entry into a restricted building or area to participate, it is _required_ that `OrderItem` include unique access control data within `accessCode` and `accessPass`. An `accessCode` _should_ be provided for manual use in the event that an `accessPass` fails.

Each `accessCode` and `accessPass` _must_ represent unique methods of entry, and _must not_ be duplicated in different formats. For example, multiple formats (e.g. PNG, JPEG) of the same `accessPass` _must not_ be supplied.

For remotely accessible [Opportunities](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-opportunity), an `accessChannel` _must_ be provided.

A [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) _must_ make all supplied `accessPass`es, `accessCode`s and `accessChannel`s available to the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer), with the exception of `Barcode`s that do not have fallback image `url`. `Barcode`s without a `url` _may_ be ignored if the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) is unable to process them.

## Text-based access control

## Image-based access control

## **Barcode-based access control**

## **Remote access control**
