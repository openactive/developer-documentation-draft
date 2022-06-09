---
description: What data you could add, and how to add it.
---

# What data to use?

The items that make up your feed are made up of data. Below is an outline of the types of data that you may want to use in your feeds.

### Recurring classes and sessions

An event series made up of individual sessions. For example, "Thursday night boxing" is a series of events made up of instances of that event "Boxing on Thursday 19th May 2022".

### Facility and Slots

A facility that can be used in slots. For example, a facility with a particular use could be a "Squash court" that could be used up in a slot, e.g. "Squash Court 1:00 pm-2:00 pm Thursday 19th May".

### AdHoc Event

This would be a one off event such as a "Yoga launch party".

### Headline event

This would be a whole day or multi-day event such as a music festival, perhaps with smaller events within the whole such as different stages at a music festival.

### &#x20;Courses

This would represent a fixed-length course made up of a description of the whole course "1 month strength training course", and individual instances of the course "1-month strength training course, starting 2nd May 2022". &#x20;

{% hint style="info" %}
You will have noticed that in some of these examples, there are two types of data, the overall event description and a single instance of that event - this is implemented with a split feed approach (i.e. two feeds linked by @id)
{% endhint %}

### Mapping your data

An open opportunity feed contains data about `Event` objects which are structured in line with schema.org. Schema.org contains a library of a widely accepted schema for describing any `Thing`.&#x20;

You will have data in your booking system that does not line up exactly with one of the above categories. You should think about which category best aligns with your system.&#x20;

For now, we will look at the recurring classes and sessions implementation. The split feed approach involves implementing two feeds, the`SessionSeries` and `ScheduledSession` feed. We will start by implementing the `ScheduledSession` .

