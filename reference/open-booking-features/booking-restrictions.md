---
description: Restrictions on the circumstances in which bookings can be made
---

# Booking Restrictions

Note that although the Customer may be ineligible to attend a bookable Opportunity via an associated bookable `Offer` based on the `genderRestriction` or `ageRestriction` of either the Opportunity or `Offer`, this specification encourages the Broker NOT to capture additional personal data about the Customer (e.g. gender and date of birth) and NOT to enforce validation of these restrictions using such data, but instead to prominently communicate clear messaging regarding such restrictions throughout the booking process to ensure that the Customer is aware of these and able to make an informed decision.

There may be additional restrictions placed on an Opportunity by the Seller. For example, it may be that one of the party must be over a certain age as a supervising adult if some of the party are minors. It is the responsibility of the Booking System and Seller to make such restrictions available as an array of `oa:additionalAdmissionRestriction` on the `SessionSeries`, `FacilityUse`, `Event`, `HeadlineEvent` or `CourseInstance`, and the Broker _must_ communicate these restrictions clearly during the booking process.

The Broker _may_ filter the `Offer`s it makes available to the Customer, for example based on `ageRestriction` of the `Offer`.
