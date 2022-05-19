---
description: What data you could add, and how to add it.
---

# What data to use?

The items that make up your feed are made up of data. There are five categories of data that you will be using in your feeds.

### Recurring sessions

A event series made up of individual sessions. For example "Thursday night boxing" is a series of events, made up of instances of that event "Boxing on Thursday 19th May 2022".

### Facility and Slots

A facility that can be used in slots. For example a facility with a particular use could be a "Squash court" that could be used up in a slot e.g. "Squash Court 1:00pm-2:00pm Thursday 19th May".

### AdHoc Event

A course that is made up of events. For example a course could be "Strength and Conditioning" and an event could be "Strength training part 1".

### Headline event and Event

Explain

### Event series and Event

Explain

### Mapping your data

An open opportunity feed contains data about `Event` objects which are structured in line with schema.org. Schema.org contains a library of widely accepted schema for describing any `Thing`.&#x20;

You will have data in your booking system that does not line up exactly with one of the above categories, you should think about which category best aligns with your system.&#x20;

For now we will implement the Series and Sessions category - starting with a `ScheduledSession` feed.&#x20;

