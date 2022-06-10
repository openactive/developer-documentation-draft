# Implementing the test interface

To run the test suite in controlled mode you must implement one endpoint for creating and one for deleting opportunities. Additionally for some tests you must add another endpoint that triggers certain "actions" in your booking system on the test data.

The test interface paths are relative to the same `baseUri` as your Open Booking API.

{% hint style="warning" %}
The test interface endpoints are not authenticated or secure.

Make sure your test interface endpoints are **not** active in production or any other environment exposed to the internet.&#x20;
{% endhint %}

### Test datasets

A `testDatasetIdentifier` is set in the configuration of the test suite, and is reused across multiple test runs of the same instance. This allows any existing test data to be cleaned up, while still allowing multiple test suite instances to execute against a shared booking system environment simultaneously.

### Create opportunities

`POST /test-interface/datasets/:testDatasetIdentifier/opportunities`

This endpoint creates an opportunity in the Booking System, that matches the specified criteria. The endpoint is called (potentially multiple times) before each individual test starts executing.

The endpoint must accept a [bookable opportunity type](https://www.openactive.io/open-booking-api/EditorsDraft/#definition-of-a-bookable-opportunity-and-offer-pair), which includes a specific `test:TestOpportunityCriteriaEnumeration` to which the newly created opportunity must conform, and the appropriate `@type` of `superEvent` or `facilityUse` to disambiguate the type of opportunity to be created. It must also include an `organizer` or `provider` `@id` to specify the Seller within which the opportunity should be created.

The Booking System must create an opportunity of the type specified in `@type` (taking into account `@type` of `superEvent` or `facilityUse`) matching the criteria specified by `test:TestOpportunityCriteriaEnumeration`, within the specified notional “Test Dataset” defined by the `testDatasetIdentifier` within the path.

The example request below would create a new `ScheduledSession`, within the test dataset “`uat-ci`”, that meets the criteria specified in `https://openactive.io/test-interface#TestOpportunityBookable`, for the `organizer` with `@id` `https://id.booking-system.example.com/organizer/3`.

<details>

<summary>Example create request</summary>

```
POST /test-interface/datasets/uat-ci/opportunities HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1

{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/test-interface"
  ],
  "@type": "ScheduledSession",
  "superEvent": {
    "@type": "SessionSeries",
    "organizer": {
      "@type": "Organization",
      "@id": "https://id.booking-system.example.com/organizer/3"
    }
  },
  "test:testOpportunityCriteria": "https://openactive.io/test-interface#TestOpportunityBookable"
}
```

</details>

<details>

<summary>Example create response</summary>

```
HTTP/1.1 201 Created
Date: Mon, 8 Oct 2018 20:52:36 GMT
Content-Type: application/vnd.openactive.booking+json; version=1

{
  "@context": "https://openactive.io/",
  "@type": "ScheduledSession",
  "@id": "https://id.booking-system.example.com/scheduled-sessions/42"
}
```

</details>

### Delete dataset

`DELETE /test-interface/datasets/:testDatasetIdentifier`

This endpoint deletes all opportunities within a given `testDatasetIdentifier`, and also deletes any `Order`s and `OrderItem`s associated with them. The endpoint is called just once before each test run.

It must clean up test data from previous test runs for the given `testDatasetIdentifier`.

The example request below would delete all opportunities within the test dataset “`uat-ci`”.

<details>

<summary>Example delete request</summary>

```
DELETE /test-interface/datasets/uat-ci HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1
```

</details>

<details>

<summary>Example delete response</summary>

```
HTTP/1.1 204 No Content
Date: Mon, 8 Oct 2018 20:52:36 GMT
Content-Type: application/vnd.openactive.booking+json; version=1
```

</details>

### Actions

`POST /test-interface/actions`

The `/test-interface/actions` endpoint is called to simulate a Booking System instigated action for the specified opportunity.

{% hint style="info" %}
You can find all actions currently used by test interface in the [Open Booking API Test Interface specification](https://openactive.io/test-interface/#properties). You will probably not need to implement every possible action, only those that are required for the tests you need to run for your booking system.
{% endhint %}

The endpoint is called when the test suite is run in both “Controlled” and “Random” test modes, only for those tests that depend on test action functionality being available in the Booking System.

If this endpoint is not implemented, the features whose tests depend on this endpoint must be configured to “disabled-tests” mode, to allow the test suite to run successfully.

The Booking System must respond with a `204` status code and an empty body to indicate success. If the action `@type` requested by the test suite is not recognised by your booking system, you should return an empty 400 error to make sure the test fails early.

The example request below would execute the simulation specified by `test:SellerRequestedCancellationSimulateAction` on the `ScheduledSession` with `@id` of `https://id.booking-system.example.com/session-series/42`.

<details>

<summary>Example action request</summary>

```
POST /test-interface/actions HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1

{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/test-interface"
  ],
  "@type": "test:SellerRequestedCancellationSimulateAction",
  "object": {
    "@type": "Order",
    "@id": "https://id.booking-system.example.com/orders/92e55c9f-ba86-471c-9cb4-5030188423b1"
  }
}
```

</details>

<details>

<summary>Example action response</summary>

```
HTTP/1.1 204 No Content
Date: Mon, 8 Oct 2018 20:52:36 GMT
Content-Type: application/vnd.openactive.booking+json; version=1
```

</details>
