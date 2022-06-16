---
description: Barcodes, PIN codes, and Online Video URLs
---

# Access Control & Online Attendance

For Opportunities that require entry into a restricted building or area to participate, it is _required_ that `OrderItem` include unique access control data within `accessCode` and `accessPass`. An `accessCode` _should_ be provided for manual use in the event that an `accessPass` fails.

Each `accessCode` and `accessPass` _must_ represent unique methods of entry, and _must not_ be duplicated in different formats. For example, multiple formats (e.g. PNG, JPEG) of the same `accessPass` _must not_ be supplied.

For remotely accessible Opportunities, an `accessChannel` _must_ be provided.

A Broker _must_ make all supplied `accessPass`es, `accessCode`s and `accessChannel`s available to the Customer, with the exception of `Barcode`s that do not have fallback image `url`. `Barcode`s without a `url` _may_ be ignored if the Broker is unable to process them.

### **Text-based access control**

PIN codes, Order identifiers, or simply the e-mail address of the Customer are all permissible for the purposes of access control.

In keeping with the Schema.org definition of `PropertyValue`, the `name` property _should_ be used for the name of the property, and the `description` property _should_ be used for any human-readable access control code(s).

Here is an example of accessCode used for a PIN Code:

```
"accessCode": [
  {
    "@type": "PropertyValue",
    "name": "PIN Code",
    "description": "1234"
  }
],
```

Here is an example of accessCode used for a booking reference:

```
"accessCode": [
  {
    "@type": "PropertyValue",
    "name": "Booking Reference",
    "description": "123456789"
  }
],
```

### **Image-based access control**

Images to be displayed to provide access, for example access tickets rendered by the Booking System, _may_ be included as an `ImageObject` within the `accessPass` array.

`ImageObject` _must not_ be used for fallback images of a `Barcode`; the `url` property of the `Barcode` _must_ be used for this purpose.

Here is an example of ImageObject in accessPass:

```
"accessPass": [
  {
    "@type": "ImageObject",
    "url": "https://access.example.com/476ac24c694da79c5e33731ebbb5f1"
  }
],
```

**8.7.3 Extension point for barcode-based access control**

Access barcodes to be rendered by the Broker _may_ be included as a `Barcode` within the `accessPass` array. Note that `Barcode` subclasses `ImageObject`, allowing it to contain a fallback rendered barcode image `url` in addition to the raw barcode details. `Barcode`s _should_ include a fallback image `url`.

Due to the variety of barcode formats available, the specification expects the `Barcode` to include additional properties using a custom namespace (as specified in the \[_Modelling-Opportunity-Data_] specification), enough to allow the barcode to be reproduced by the Broker. This will help inform future versions of the specification.

Additionally where use of an existing barcode is desirable (for example, the barcode on a Broker's own membership card) the Broker _may_ include a `Barcode` in the `accessPass` property against individual `OrderItem`s in the request to **B**. If the Booking System is able to accept such existing barcodes, it _must_ reflect back the specified `Barcode` in the `accessPass` array in the response along with any other valid access control options.

Here is an example of Barcode in accessPass:

```
"accessPass": [
  {
    "@type": "Barcode",
    "url": "https://fallback.image.example.com/476ac24c694da79c5e33731ebbb5f1",
    "text": "0123456789",
    "beta:codeType": "code128"
  }
],
```

**8.7.4 Remote access control**

A `VirtualLocation` provides remote participants with a means of attending an Opportunity. The `url` _should_ include encoded access credentials where possible. Manual access credentials `name`, `accessId` and `accessCode` _should_ also be supplied to ensure the Customer can access the Opportunity from any device. `description` _should_ include any other information necessary for the Customer to easily access the Opportunity.

For Opportunities that are accessible remotely, the `attendeeInstructions` of the Opportunity _should_ include instructions that explain the remote set up, including how the Customer should participate in the Opportunity.

If an `accessChannel` is not available to the Customer immediately upon booking via the **B** response, and only accessible via notification, a `customerNotice` _must_ be included in **B** that includes details of when the means of access will be shared with the Customer, for example "Details of a Zoom room will be sent 1 hour prior to the class starting".

The Broker _must_ send an automatic reminder to the Customer that includes the details of the `accessChannel` 30 minutes prior to the class starting.

Here is an example of VirtualLocation in accessChannel:

```
"accessChannel": {
  "@type": "VirtualLocation",
  "url": "https://zoom.us/j/1234567890?pwd=SlBzaVpJaGY0UGJVMX",
  "name": "Zoom Video Chat",
  "accessId" : "1234567890",
  "accessCode" : "211277",
  "description": "Please log into Zoom a few minutes before the event"
},
```

\
