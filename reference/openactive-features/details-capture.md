---
description: Capturing details about the customer or attendee
---

# Details Capture

### **Customer Details capture**

The specification includes provision to capture of the following details related to the Customer (the actor who is booking), with only the e-mail address being required.

* E-mail address (`email`)
* Forename (`givenName`)
* Surname (`familyName`)
* Telephone (`telephone`)

Note that these are _not_ the details of attendees, and that the Customer is not guaranteed to participate in the activity. This facilitates child booking as the Customer can easily book on behalf of a child, without supplying the child's own personal details.

### **Attendee Details capture**

If attendee details in scope of the `Person` type are captured, they _must_ be captured via the `attendee` property of the `OrderItem` (and not using the `orderItemIntakeForm`). This allows attendee details to be stored and reused by the Broker.

If the same `attendee` is used across multiple `OrderItem`s, a duplicate `Person` _must_ be provided for each of these `OrderItem`s. The Broker may choose to design their user interface to minimise repeat data entry (e.g. by allowing the Customer to enter attendee details first, then later assigning them to `OrderItem`s), however this is outside the scope of the specification.

Note that conformance with this specification requires that the Booking System _must not_ _require_ attendee data to be submitted in all cases, and that configuration of which attendee details are required (if any) _must_ be available to the Seller.

If an `Offer` requires attendee details capture, the Booking System _must_ include the `openBookingFlowRequirement` of `https://openactive.io/OpenBookingAttendeeDetails` in the `Offer` within its open data. This allows Brokers that do not support attendee details capture to easily filter out such `Offer`s from their experience.

Here is an example of attendeeDetailsRequired provided by Booking System at C1:

```
{
  "@type": "OrderItem",
  ...
  "attendeeDetailsRequired": [
    "https://schema.org/givenName",
    "https://schema.org/familyName"
  ],
}
```

Here is an example of attendee details provided with each OrderItem at C2:

```
{
  "@type": "OrderItem",
  ...
  "attendee": {
    "@type": "Person",
    "email": "geoffcapes@example.com",
    "telephone": "020 811 8055",
    "givenName": "Geoff",
    "familyName": "Capes"
  }
}
```

If a required property within `Person` is not supplied by the Broker the Booking System _must_ include an `IncompleteAttendeeDetailsError` against the offending `OrderItem` at **C2** which includes an `instance` that references the `@id` of the missing or invalid `Property`.

Here is an example of an error for missing attendee property data:

```
{
  "@type": "OrderItem",
  ...
  "error": [
    {
      "@type": "IncompleteAttendeeDetailsError",
      "instance": "https://schema.org/givenName",
      "description": "Missing attendee first name"
    }
  ]
}
```

The Broker _may_ remember the attendee data previously submitted by the Customer, if consent is provided.

### **Additional Details capture**

This version of the specification does not provide comprehensive support for construction of a complex additional details capture form. However, it does facilitate the use of simple text fields, dropdowns and checkboxes to capture additional details. These can be used for both the Simple Booking Flow and the Booking Flow with Approval.

If an `Offer` requires additional details capture, the Booking System _must_ include the `openBookingFlowRequirement` of `https://openactive.io/OpenBookingIntakeForm` in the `Offer` within its open data. This allows Brokers that do not support such additional details capture to easily filter out such `Offer`s from their experience.

Four types of form element are available, all of which sub-class `PropertyValueSpecification`:

* `ShortAnswerFormFieldSpecification`
* `ParagraphFormFieldSpecification`
* `DropdownFormFieldSpecification`
* `BooleanFormFieldSpecification`

### **Text fields**

`ShortAnswerFormFieldSpecification` and `ParagraphFormFieldSpecification` are identical except that they represent a single-line text box and a multi-line text box, respectively.

Here is an example of orderItemIntakeForm with ShortAnswerFormSpecification provided by Booking System at C1:

```
{
  "@type": "OrderItem",
  ...
  "orderItemIntakeForm": [
    {
      "@type": "ShortAnswerFormSpecification",
      "@id": "https://example.com/experience",
      "name": "Level of experience",
      "description": "Have you played before? Are you a complete beginner or seasoned pro?",
      "valueRequired": true
    }
  ]
}
```

Here is an example of orderItemIntakeFormResponse for ShortAnswerFormSpecification provided by Broker at C2:

```
{
  "@type": "OrderItem",
  ...
  "orderItemIntakeFormResponse": [
    {
      "@type": "PropertyValue",
      "propertyID": "https://example.com/experience",
      "value": "I've played twice before, but I'm a quick learner so I hope to keep up!"
    }
  ]
}
```

### **Dropdown fields**

`DropdownFormFieldSpecification` provides a list of options in a dropdown format.

`valueOption`s are provided as an array of simple strings containing display values, and are supplied back to the Booking System using these display values in the `orderItemIntakeFormResponse`.

Here is an example of orderItemIntakeForm with DropdownFormFieldSpecification provided by Booking System at C1:

```
{
  "@type": "OrderItem",
  ...
  "orderItemIntakeForm": [
    {
      "@type": "DropdownFormFieldSpecification",
      "@id": "https://example.com/age",
      "name": "Age",
      "description": "Your age is useful for us to place you in the correct group on the day",
      "valueOption": ["0-18", "18-30", "30+"],
      "valueRequired": true
    }
  ]
}
```

Here is an example of orderItemIntakeFormResponse for DropdownFormFieldSpecification provided by Broker at C2:

```
{
  "@type": "OrderItem",
  ...
  "orderItemIntakeFormResponse": [
    {
      "@type": "PropertyValue",
      "propertyID": "https://example.com/age",
      "value": "0-18"
    }
  ]
}
```

**Checkbox fields**

`BooleanFormFieldSpecification` accepts either a `true` or `false` response, and _must not_ be specified as `valueRequired`.

Here is an example of orderItemIntakeForm with BooleanFormFieldSpecification provided by Booking System at C1:

```
{
  "@type": "OrderItem",
  ...
  "orderItemIntakeForm": [
    {
      "@type": "BooleanFormFieldSpecification",
      "@id": "https://example.com/photoconsent",
      "name": "Photo Consent",
      "description": "Are you happy for us to include photos of you in our marketing materials?"
    }
  ]
}
```

The `value` in the `PropertyValue` response _must_ be either `true` or `false`.

Here is an example of orderItemIntakeFormResponse for BooleanFormFieldSpecification provided by Broker at C2:

```
{
  "@type": "OrderItem",
  ...
  "orderItemIntakeFormResponse": [
    {
      "@type": "PropertyValue",
      "propertyID": "https://example.com/photoconsent",
      "value": true
    }
  ]
}
```

### **Form validation and auto-fill**

If a field with `valueRequired` is not supplied by the Broker, the Booking System _must_ include an `IncompleteIntakeFormError` against the offending `OrderItem` at **C2** which includes an `instance` that references the `@id` of the specific `PropertyValueSpecification`.

Here is an example of an error for missing additional details field data

```
{
  "@type": "OrderItem",
  ...
  "error": [
    {
      "@type": "IncompleteIntakeFormError",
      "instance": "https://example.com/experience",
      "description": "Incomplete additional details supplied"
    }
  ]
}
```

If the contents of a field supplied by the Broker are present but invalid, the Booking System _must_ include an `InvalidIntakeFormError` against the offending `OrderItem` at **C2** which includes both an `instance` that references the `@id` of the specific `PropertyValueSpecification`, and a human-readable `description` of the validation constraint to allow the Customer to correct it.

In this version of the specification, any field value validation _must_ be implemented by the Booking System via `InvalidIntakeFormError`, for example if integer values are required.

Here is an example of an error for invalid additional details field data:

```
{
  "@type": "OrderItem",
  ...
  "error": [
    {
      "@type": "InvalidIntakeFormError",
      "instance": "https://example.com/shoesize",
      "description": "The provided shoe size is not a number"
    }
  ]
}
```

The Broker _may_ remember the values previously submitted by the Customer based on the `@id` of the `PropertyValueSpecification`, if consent for such storage is provided.

It is expected that future versions of the specification will support more complex field types.
