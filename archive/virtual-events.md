# Virtual Events

Virtual Events are opportunities that are accessible remotely in real-time.

The **`eventAttendanceMode`** property is used to indicate that an opportunity is available to attend online. It can be applied to the existing opportunity types for `SessionSeries`, `HeadlineEvent`, `Event`, `CourseInstance` as part of **existing RPDE feeds for those types**.

Although in most cases a Virtual Event will be a "Livestream" event - where an instructor live streams a video of themselves or their class - other types of virtual events exist. For example, using augmented reality or virtual reality. Such alternative forms of media will be accounted for in the future with further properties in addition to those listed below.

For the sake of speed and external consistency during the COVID-19 Pandemic, in the short term, the word “Livestream” or "Live" is recommended within user interfaces to represent all Virtual Events. However, implementers should be aware that properties introduced in the future may necessitate the use of more specific language to correctly represent events.

{% hint style="warning" %}
Online classes and events are part of an [ongoing discussion](https://github.com/openactive/modelling-opportunity-data/issues/71) that has been [accelerated](https://w3c.openactive.io/meetings/2020-03-25-virtual-events) in response to the COVID-19 pandemic, and any properties suggested in the associated proposals are subject to change after the pandemic has ended. We welcome your contribution to the [discussion and various proposals](https://github.com/openactive/modelling-opportunity-data/labels/virtual%20events) with any thoughts and feedback from your implementation.
{% endhint %}

## Implementation guidance

In order to upgrade your booking or listing system to support virtual events, consider implementing the following properties. The OpenActive libraries, types reference documentation, and [validator](http://validator.openactive.io/) have been updated to support these properties.

{% hint style="info" %}
Please see the "**Updated Proposal**" within each of the referenced GitHub issues in the headings below for further specific guidance on each property. Please comment on these GitHub issue if you require any specific clarifications.
{% endhint %}

### `eventAttendanceMode` ([#225](https://github.com/openactive/modelling-opportunity-data/issues/225))

#### **Definition**

Whether an event occurs online, offline, or a mix of both.

#### **Why implement this property?**

This property is the **required** for a minimal implementation of virtual events.

#### **Values**

`eventAttendanceMode` must have one of the following values:

* `https://schema.org/MixedEventAttendanceMode`
* `https://schema.org/OfflineEventAttendanceMode`
* `https://schema.org/OnlineEventAttendanceMode`

If `eventAttendanceMode` not provided it is must be assumed to be `https://schema.org/OfflineEventAttendanceMode` for backwards compatibility.

If `eventAttendanceMode` is set to `https://schema.org/OnlineEventAttendanceMode`, `location` **MUST NOT** be provided for backwards compatibility, in order to ensure that existing data users do not misrepresent virtual classes as physical ones. See the property `beta:affiliatedLocation` below for an alternative.

#### Example

```javascript
{
  "@type": "SessionSeries",
  ...
  "eventAttendanceMode": "https://schema.org/OnlineEventAttendanceMode"
  ...
}
```

### `beta:affiliatedLocation` ([#227](https://github.com/openactive/modelling-opportunity-data/issues/227))

#### **Definition**

The physical location affiliated with the virtual event, for example the original location of the event before it was moved online.

#### **Why implement this property?**

This property allows data users to display only virtual events that would usually be run from a local location, for those wishing to promote "local faces in local places" as part of the COVID-19 efforts to provide familiarity to those under lockdown.

#### **Values**

The property is identical to the existing `location` property, requiring a `Place` with either an `address` or a `geo`, or both.

`beta:affiliatedLocation` may only be present when `eventAttendanceMode` is set to `https://schema.org/OnlineEventAttendanceMode`.

#### Example

```javascript
{
 "@type": "SessionSeries",
 ...
  "beta:affiliatedLocation": {
    "@type": "Place",
    "name": "Middlesbrough Sports Village",
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "Alan Peacock Way",
      "addressLocality": "Village East",
      "addressRegion": "Middlesbrough",
      "postalCode": "TS4 3AE",
      "addressCountry": "GB"
    },
    "geo": {
      "@type": "GeoCoordinates",
      "latitude": 54.543964,
      "longitude": -1.20978500000001
    }
  },
  ...
}
```

### `beta:isInteractivityPreferred` ([#230](https://github.com/openactive/modelling-opportunity-data/issues/230))

**Definition**

A property that indicates whether the virtual event is interactive (e.g. Zoom with participant microphones and cameras on), or is just a one-way broadcast (e.g. Facebook Live, Instagram Live, Zoom with participant microphones and cameras off).

#### **Why implement this property?**

This property allows applications to present a filter for "interactive classes", for those users who prefer instructors who engage with their audience. This is a key differentiator for smaller classes that will likely benefit most from being published as open data, as activity providers who use Instagram or Facebook Live already have a large audience.

#### **Values**

`beta:isInteractivityPreferred` is a boolean, and so may have the values of `true`, `false` or be left unspecified. If unspecified, it is assumed that the value is unknown.

The existing `attendeeInstruction` and `description` properties may be used for activity providers to provide further clarification about interactivity preferences.

Additionally, this may optionally be combined with the boolean `beta:isVirtuallyCoached` ([#71](https://github.com/openactive/modelling-opportunity-data/issues/71)) for the cases where the virtual class instruction is pre-recorded - e.g. for the common use case where Zoom is used for a group of participants to share in a pre-recorded class. If `beta:isVirtuallyCoached` is unspecified, it is assumed to be `false`.

`beta:isInteractivityPreferred` **must not** be present when `eventAttendanceMode` is set to `https://schema.org/OfflineEventAttendanceMode` or is unspecified.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
 ...
 "beta:isInteractivityPreferred": true,
 "description": "... We get together with others in the village weekly to follow a Yoga video together on Zoom. ...",
 "attendeeInstruction": "... We encourage you to switch your camera on to feel part of the community. ...",
 "beta:isVirtuallyCoached": true
 ...
}
```

### `beta:participantSuppliedEquipment` ([#229](https://github.com/openactive/modelling-opportunity-data/issues/229))

**Definition**

A property that indicates whether the participant must or may supply equipment for use in the Event.

#### **Why implement this property?**

This property allows applications to present a filter for "no equipment required", for those users who do not have equipment at home for an online event, or to bring with them to an offline event. This helps first-time participants find classes they can easily attend.

#### **Values**

`beta:participantSuppliedEquipment` must have one of the following values:

* `https://openactive.io/Required` - Equipment is required
* `https://openactive.io/Optional` - Equipment is optional, and the participant can improvise
* `https://openactive.io/Unavailable` - No equipment required

The property is applicable to online, offline and mixed events.

The existing `attendeeInstruction` and `description` properties may be used for activity providers to provide further clarification about equipment requirements.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
 ...
 "beta:participantSuppliedEquipment": true,
 "description": "... This class is better with steps at home, but you can improvise if you don't have any. ...",
 "attendeeInstruction": "... If you don't have a step at home, find two reasonably thick books. ...",
 ...
}
```

### `maximumVirtualAttendeeCapacity` ([#226](https://github.com/openactive/modelling-opportunity-data/issues/226))

**Definition**

Indicates the maximum number of connections to a shared virtual space.

#### **Why implement this property?**

This property allows applications to present a filter for "class size", for those users who are interested in attending more intimate classes.

#### **Values**

`maximumVirtualAttendeeCapacity` must be an integer.

The property can be set at either the `SessionSeries`, `ScheduledSession`or `Event` level.

`maximumVirtualAttendeeCapacity` **must not** be present when `eventAttendanceMode` is set to `https://schema.org/OfflineEventAttendanceMode` or is unspecified.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
 ...
 "maximumVirtualAttendeeCapacity": 20,
 ...
}
```

### `beta:virtualLocation` ([#224](https://github.com/openactive/modelling-opportunity-data/issues/224))

**Definition**

Describes a means of electronic access to a shared virtual space.

#### **Why implement this property?**

This property allows participants to jump straight into the virtual class from any application, and for applications to show "sessions happening right now" that they can access for free, for example.

#### **Values**

The value of `beta:virtualLocation`, must be a `VirtualLocation` object, which must include at least the `url`, `name`, or `description` property. The `url` property is recommended for simple implementations.

The `url` must provide direct access to participate in a free virtual event, which could be used for e.g. a "View Livestream" button.

Note where the virtual location is private (e.g. behind a paywall), the `VirtualLocation` `url` should **not** be included.

From the existing [Modelling Opportunity Data specification](https://www.openactive.io/modelling-opportunity-data/), the `Offer` `url` can be used for the link to purchase access or registration, and the `Event` `url` can signpost to a general page about the session.

`beta:virtualLocation` **must not** be present when `eventAttendanceMode` is set to `https://schema.org/OfflineEventAttendanceMode` or is unspecified.

The property can be set at the `SessionSeries`, `ScheduledSession` or `Event` level.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
 ...
 "beta:virtualLocation": {
   "@type": "VirtualLocation",
   "name": "Zoom Video Chat",
   "url": "https://zoom.us/j/1234567890/signup",
   "description": "Please log into Zoom a few minutes before the event, and mute your mic while you wait for the session to start"
 }
 ...
}
```

### `level` for "Beginner-friendly" ([#82](https://github.com/openactive/modelling-opportunity-data/issues/82))

**Definition**

To allow for "beginner-friendly" events to be easily discoverable.

#### **Why implement this property?**

This property can be implemented simply as a "beginner-friendly" tick box, if no notion of "level" currently exists within the booking or listing system. This allows applications to present a filter for "beginner friendly", for those users who are new to the activity.

#### **Values**

To specify "Beginner-friendly" the value of the `level` property, must include the string `Beginner` in an array.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
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

A number of activity providers are seeking donations for their free live stream classes in the wake of the COVID-19 pandemic lockdowns. Sessions that appear to be offered for "free" based on `isAccessibleForFree` and `offers` actually proactively ask for donations. This property allows applications to advertise donation requests prominently to participants.

#### **Values**

The property accepts a URL, the existence of which both indicates that an activity provider is requesting donations, and provides the URL that applications should use when displaying a "Donate" button. It is applicable to online, offline and mixed events.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
 ...
  "beta:donationPaymentUrl": "https://www.paypal.com/donate/acme_fit"
 ...
} 
```

### `beta:formalCriteriaMet` ([#236](https://github.com/openactive/modelling-opportunity-data/issues/236))

**Definition**

An array of URLs, each of which describe the formal criteria that are met by the organizer.

#### **Why implement this property?**

It is the activity provider’s responsibility to ensure they have adequate insurance in place for virtual classes (and correct music licences, where relevant). Booking systems that wish to allow their customers to be part of Sport England's [Join the Movement](https://www.sportengland.org/stayinworkout) campaign must provide assurance that activity providers have met these criteria, by either including the `beta:formalCriteriaMet` property per-organizer, or by demonstrating system-level safeguards are in place.

#### **Values**

The `beta:formalCriteriaMet` property accepts an array of URLs, and in most cases will contain just one URL. Each URL must reference a webpage that includes the criteria that have been presented to the activity provider, and that have been actively accepted by them. The webpage itself does not need to be presented to the activity provider, as long as the criteria it contains is accepted by the activity provider in some form.

An example of such a URL is: [`https://emduk.org/advice-on-how-instructors-can-continue-to-deliver-their-classes-online/`](https://emduk.org/advice-on-how-instructors-can-continue-to-deliver-their-classes-online/)

Applications that consume the open data can use the content of the webpage at each URL to decide if the criteria specified are sufficient for their needs, and filter for opportunities where the `beta:formalCriteriaMet` property includes such URLs.

The property must be set on the `Organization` or `Person` within the `organizer` property.

#### **Example**

```javascript
{
 "@type": "SessionSeries",
 ...
 "organizer": {
  "@type": "Organization",
  ...
  "beta:formalCriteriaMet": [
    "https://emduk.org/advice-on-how-instructors-can-continue-to-deliver-their-classes-online/"
  ]
 }
} 
```

## Conformance criteria

{% hint style="info" %}
Please feedback on the below or request any clarifications by commenting on [this GitHub issue](https://github.com/openactive/modelling-opportunity-data/issues/231).
{% endhint %}

Note that in order to make use of "beta" properties, `"@context"` must include the beta namespace, as follows:

```javascript
"@context": [
  "https://openactive.io/",
  "https://openactive.io/ns-beta"
]
```

### SessionSeries, HeadlineEvent, CourseInstance and Event

The following properties are **REQUIRED** (inheriting from existing [Modelling Opportunity Data specification](https://www.openactive.io/modelling-opportunity-data/)):

* `name`
* `activity`
* `organizer` - including a **required** `@id`, a **recommended** `sameAs` property for social media handles, and a **recommended** `beta:formalCriteriaMet` ([#236](https://github.com/openactive/modelling-opportunity-data/issues/236)).
  * The `@id` is required to give each organizer a globally unique identifier in the form of a URL.
  * The `@id` does not need to resolve to a functional endpoint, but **must** use a domain name owned by your booking or listing system. This allows applications with specific content approval requirements to register trusted organizers based on their `@id`.
  * An example of such an `@id` is `https://id.bookingsystem.com/organizers/123`.
* `eventAttendanceMode` ([#225](https://github.com/openactive/modelling-opportunity-data/issues/225))
* `offers` (including a recommended `url` that links straight to the purchase page)
* `url` (to a page describing the session)

The following properties **MUST NOT** be included when `eventAttendanceMode` is set to `https://schema.org/OnlineEventAttendanceMode`:

* `location` ([#227](https://github.com/openactive/modelling-opportunity-data/issues/227))

The following properties are **RECOMMENDED**:

* `description`
* `image`
* `level` ([#82](https://github.com/openactive/modelling-opportunity-data/issues/82)) - using the string "`Beginner`" for beginner friendly classes
* `ageRange`
* `genderRestriction`
* `beta:isFirstSessionAccessibleForFree` ([#232](https://github.com/openactive/modelling-opportunity-data/issues/232))
* `beta:isInteractivityPreferred` ([#230](https://github.com/openactive/modelling-opportunity-data/issues/230))
* `beta:participantSuppliedEquipment` ([#229](https://github.com/openactive/modelling-opportunity-data/issues/229))
* `beta:affiliatedLocation` ([#227](https://github.com/openactive/modelling-opportunity-data/issues/227))
* `maximumVirtualAttendeeCapacity` ([#226](https://github.com/openactive/modelling-opportunity-data/issues/226))
* `beta:donationPaymentUrl` ([#234](https://github.com/openactive/modelling-opportunity-data/issues/234))

### ScheduledSession and Event

The following properties are **REQUIRED** (inheriting from existing [Modelling Opportunity Data specification](https://www.openactive.io/modelling-opportunity-data/)):

* `startDate`

The following properties are **RECOMMENDED**:

* `duration`
* `endDate`
* `beta:virtualLocation` ([#224](https://github.com/openactive/modelling-opportunity-data/issues/224)) (including a `url` to the live class itself, e.g. a Facebook Live or Zoom URL)

## **Examples**

### **Complete examples**

The validator includes complete examples for:

* [Virtual SessionSeries](https://validator.openactive.io/?url=https%3A%2F%2Fwww.openactive.io%2Fdata-models%2Fversions%2F2.x%2Fexamples%2Fsessionseries-split-virtual\_example\_1.json\&version=2.x)
* [Virtual ScheduledSession](https://validator.openactive.io/?url=https%3A%2F%2Fwww.openactive.io%2Fdata-models%2Fversions%2F2.x%2Fexamples%2Fscheduledsession-split-virtual\_example\_1.json\&version=2.x)

### Illustrative examples

The examples below only include new properties specific to virtual events, for those already familiar with the OpenActive specifications. Please see above for all properties that should be included.

```javascript
{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/ns-beta"
  ],
  "@type": "SessionSeries",
  ...
  "organizer": {
    "@type": "Organization",
    "@id": "https://id.bookingsystem.com/organizers/123",
    ...
    "beta:formalCriteriaMet": [
      "https://emduk.org/advice-on-how-instructors-can-continue-to-deliver-their-classes-online/"
    ]
  },
  "eventAttendanceMode": "https://schema.org/OnlineEventAttendanceMode",
  "beta:affiliatedLocation": {
    "@type": "Place",
    "name": "Middlesbrough Sports Village",
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "Alan Peacock Way",
      "addressLocality": "Village East",
      "addressRegion": "Middlesbrough",
      "postalCode": "TS4 3AE",
      "addressCountry": "GB"
    },
    "geo": {
      "@type": "GeoCoordinates",
      "latitude": 54.543964,
      "longitude": -1.20978500000001
    }
  },
  "beta:isInteractivityPreferred": true,
  "beta:participantSuppliedEquipment": "https://openactive.io/Required",
  "beta:donationPaymentUrl": "https://www.paypal.com/donate/acme_fit"
 }
}
```

```javascript
{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/ns-beta"
  ],
  "@type": "ScheduledSession",
  ...
  "maximumVirtualAttendeeCapacity": 20,
  "beta:virtualLocation": {
    "@type": "VirtualLocation",
    "name": "Zoom Video Chat",
    "url": "https://zoom.us/j/1234567890/signup",
    "description": "Please log into Zoom a few minutes before the event, and mute your mic while you wait for the session to start"
  }
}
```
