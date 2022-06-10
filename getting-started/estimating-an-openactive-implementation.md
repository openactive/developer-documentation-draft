# Estimating an OpenActive Implementation

## Considerations

The development time of an OpenActive implementation will vary considerably depending on the following factors:

* The application and database architecture of your existing software
  * In particular whether your platform supports a single or multiple sellers
* How many Open Booking and Open Opportunity features you need to implement
* The language and/or framework your software is written in and whether open source OpenActive libraries are available for them

{% hint style="info" %}
C#/.NET has the most comprehensive OpenActive libraries. There are also supporting libraries for Ruby, PHP, and JavaScript/TypeScript.
{% endhint %}

## OpenActive implementation breakdown

An OpenActive implementation for your booking system can be broken down in to two major projects: [Open Opportunity Feeds](../required-features-guides/opportunity-feeds/) and the [Open Booking API](../required-features-guides/simple-booking/). Below we have broken down these projects in to their major tasks and give ranges of how long these tasks could take.

### Open Opportunity Feeds

1. Identify how your data maps on to the OpenActive opportunity/event schema
2. For each required feed:
   1. Marshaling your data in to opportunity feed items
   2. Building a real-time paged data exchange feed
   3. Implementing soft deletes
   4. Feed validation and testing
3. Adjusting your database or application architecture for feed performance
4. Identifying and adding recommended or optional information to your feeds
5. Building a dataset site
6. Publishing your feeds

### Open Booking API&#x20;

1. Implementing the Simple Booking Flow
2. Implementing the Approval Booking Flow
3. Configuring and running the test suite to validate your API
4. Handling abandonment, cancellation, and errors
5. Authentication
6. Identifying and implementing OpenActive features for your API
7. Administration tools for your sellers
