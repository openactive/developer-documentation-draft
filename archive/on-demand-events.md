# On-Demand Events

On-Demand Events are recordings of events that do not occur at a specific time, and are available to watch at any time - similar to content on Netflix or iPlayer.

These might be pre-recorded fitness classes, workouts, sessions which are provided via an online product, such as Les Mills On Demand and Racefully. They include on-demand video content and sessions which can be participated in virtually using an app or run-tracker (e.g. live virtual 5k).

The **`OnDemandEvent`** type is used to represent such events, and they must be published using a **separate RPDE feed**, as per [this example](https://validator.openactive.io/?url=https%3A%2F%2Fwww.openactive.io%2Fdata-models%2Fversions%2F2.x%2Fexamples%2Fondemandevent\_example\_1.json\&version=2.x).

{% hint style="warning" %}
Online classes and events are part of an [ongoing discussion](https://github.com/openactive/modelling-opportunity-data/issues/71) that has been [accelerated](https://w3c.openactive.io/meetings/2020-03-25-virtual-events) in response to the COVID-19 pandemic, and any properties suggested in the associated proposals are subject to change after the pandemic has ended. We welcome your contribution to the [discussion and various proposals](https://github.com/openactive/modelling-opportunity-data/labels/virtual%20events) with any thoughts and feedback from your implementation.
{% endhint %}

## Implementation guidance

In order to upgrade your booking or listing system to support virtual events, consider implementing the following properties, in addition to the standard required and recommended properties, within your **new RPDE feed specific to** **`OnDemandEvent`**. The OpenActive libraries, types reference documentation, and [validator](http://validator.openactive.io/) have been updated to support these properties.

{% hint style="info" %}
Please see the "**Updated Proposal**" within each of the referenced GitHub issues in the headings below for further specific guidance on each property. Please comment on these GitHub issue if you require any specific clarifications.
{% endhint %}

### `beta:participantSuppliedEquipment` ([#229](https://github.com/openactive/modelling-opportunity-data/issues/229))

**Definition**

A property that indicates whether the participant must or may supply equipment for use in the Event.

#### **Why implement this property?**

This property allows applications to present a filter for "no equipment required", for those users who do not have equipment at home. This helps first-time virtual participants find classes they can easily participate in.

#### **Values**

`beta:participantSuppliedEquipment` must have one of the following values:

* `https://openactive.io/Required` - Equipment is required
* `https://openactive.io/Optional` - Equipment is optional, and the participant can improvise
* `https://openactive.io/Unavailable` - No equipment required

The existing `attendeeInstruction` and `description` properties may be used for activity providers to provide further clarification about equipment requirements.

#### **Example**

```javascript
{
 "@type": "OnDemandEvent",
 ...
 "beta:participantSuppliedEquipment": true,
 "description": "... This class is better with steps at home, but you can improvise if you don't have any. ...",
 "attendeeInstruction": "... If you don't have a step at home, find two reasonably thick books. ...",
 ...
}
```

### `level` for "Beginner-friendly" ([#82](https://github.com/openactive/modelling-opportunity-data/issues/82))

**Definition**

To allow for "beginner-friendly" events to be easily discoverable.

#### **Why implement this property?**

This property can be implemented simply as a "beginner-friendly" tick box, if no notion of "level" currently exists within the booking or listing system. This allows applications to present a filter for "beginner friendly", for those users who are new to the activity.

#### **Values**

To specify "Beginner-friendly" the value of the `level` property must include the string `Beginner` in an array. Other values such as `Intermediate` and `Advanced` are also permissible, but are not standardised.

#### **Example**

```javascript
{
 "@type": "OnDemandEvent",
 ...
  "level": [
    "Beginner"
  ]
 ...
}
```

### `beta:donationPaymentUrl` ([#234](https://github.com/openactive/modelling-opportunity-data/issues/234))

**Definition**

The URL of the webpage where the activity provider accepts donations.

#### **Why implement this property?**

A number of activity providers are seeking donations for their free on-demand classes in the wake of the COVID-19 pandemic lockdowns. On-demand sessions that appear to be offered for "free" based on `isAccessibleForFree` and `offers` actually proactively ask for donations. This property allows applications to advertise donation requests prominently to participants.

#### **Values**

The property accepts a URL, the existence of which both indicates that an activity provider is requesting donations, and provides the URL that applications should use when displaying a "Donate" button.

#### **Example**

```javascript
{
 "@type": "OnDemandEvent",
 ...
  "beta:donationPaymentUrl": "https://www.paypal.com/donate/acme_fit"
 ...
}
```

### `workFeatured`([#228](https://github.com/openactive/modelling-opportunity-data/issues/228))

**Definition**

A video, audio or other media that represents the actual recording of the `OnDemandEvent`.

#### **Why implement this property?**

This property allows applications to link directly to the media associated with the `OnDemandEvent`, and allows applications to embed this media within their user experience - where such media is available free of charge - to provide users with a more seamless user journey.

#### **Values**

The property accepts a `VideoObject`, `AudioObject`, or a more general `MediaObject`, which includes a `url` for the page where the media is available, an `embedUrl` that can be used to embed the media in an application, and a `thumbnail` that can be used to represent the media within an application.

#### **Example**

```javascript
{
  "@type": "OnDemandEvent",
  ...
  "workFeatured": {
    "@type": "VideoObject",
    "url": "https://www.youtube.com/watch?v=3fbCs0GVjgQ",
    "embedUrl": "https://www.youtube.com/embed/3fbCs0GVjgQ",
    "thumbnail": [
      {
        "@type": "ImageObject",
        "url": "http://example.com/static/image/speedball_thumbnail.jpg"
      }
    ]
  },
  ...
}
```

## Conformance criteria

{% hint style="info" %}
For a full description of all properties available within `OnDemandEvent`, please see the reference documentation. Please feedback on the below or request any clarifications by commenting on [this GitHub issue](https://github.com/openactive/modelling-opportunity-data/issues/231).
{% endhint %}

Note that in order to make use of "beta" properties, `"@context"` must include the beta namespace, as follows:

```javascript
"@context": [
  "https://openactive.io/",
  "https://openactive.io/ns-beta"
],
```

The following properties are **REQUIRED** for `OnDemandEvent`:

* `activity`
* `name`
* `offers` (including a recommended `url` that links straight to the purchase page)
* `organizer` (including a **required** `@id`, and a **recommended** `sameAs` property for social media handles)
  * The `@id` is required to give each organizer a [globally unique identifier in the form of a URL](https://www.openactive.io/open-booking-api/EditorsDraft/#globally-unique-identifiers).
  * The `@id` does not need to resolve to a functional endpoint, but **must** use a domain name owned by your booking or listing system. This allows applications with specific content approval requirements to register trusted organizers based on their `@id`.
  * An example of such an `@id` is `https://id.bookingsystem.com/organizers/123`.
* `url` (to a page describing the session)

The following properties are **RECOMMENDED** for `OnDemandEvent`:

* `ageRange`
* `description`
* `duration`
* `genderRestriction`
* `image`
* `level` ([#82](https://github.com/openactive/modelling-opportunity-data/issues/82)) - using the string "`Beginner`" for beginner friendly classes
* `workFeatured`([#228](https://github.com/openactive/modelling-opportunity-data/issues/228)) - which could could be a `VideoObject`, `AudioObject`, or a more general `MediaObject`.
* `beta:donationPaymentUrl` ([#234](https://github.com/openactive/modelling-opportunity-data/issues/234))
* `beta:isFirstSessionAccessibleForFree` ([#232](https://github.com/openactive/modelling-opportunity-data/issues/232))
* `beta:participantSuppliedEquipment` ([#229](https://github.com/openactive/modelling-opportunity-data/issues/229))

## **Examples**

### **Complete examples**

The validator includes a complete example for:

* [OnDemandEvent](https://validator.openactive.io/?url=https%3A%2F%2Fwww.openactive.io%2Fdata-models%2Fversions%2F2.x%2Fexamples%2Fondemandevent\_example\_1.json\&version=2.x)

### Illustrative examples

The example below only include new properties specific to on-demand events, for those already familiar with the OpenActive specifications. Please see above for all properties that should be included.

```javascript
{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/ns-beta"
  ],
  "@type": "OnDemandEvent",
  ...
  "workFeatured": {
    "@type": "VideoObject",
    "url": "https://www.youtube.com/watch?v=3fbCs0GVjgQ",
    "embedUrl": "https://www.youtube.com/embed/3fbCs0GVjgQ",
    "thumbnail": [
      {
        "@type": "ImageObject",
        "url": "http://example.com/static/image/speedball_thumbnail.jpg"
      }
    ]
  },
  "beta:participantSuppliedEquipment": "https://openactive.io/Required",
  "beta:donationPaymentUrl": "https://www.paypal.com/donate/acme_fit"
}
```
