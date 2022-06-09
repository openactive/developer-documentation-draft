# Open Booking API data model

You will have already considered how the OpenActive data model fits with your booking system when implementing your opportunity feeds. As we saw in the previous section on API endpoints, there are a few new objects for the API. As you did when implementing the feeds you may need to make adjustments to your database schema or code.

{% hint style="info" %}
OpenActive has open source data model libraries (LINK) for .NET, PHP, Ruby, and Node JS that can help when mapping your data to the OpenActive data model.
{% endhint %}

Let's take the objects in the OpenActive data model one by one and discuss how this might fit in your existing system.

![Schema to support Open Booking API](<../../.gitbook/assets/image (1) (1).png>)

###

#### Opportunity

This is the same object as is present in your opportunity feeds. It may be represented by a single object in your system or a join across multiple objects. You probably will not need to make too many changes here beyond those you made for your feeds.

#### Order

This represents a successfully created order, i.e. the result of endpoint B. Customer information should be associated or embedded in this object. You probably already have a table for this concept.&#x20;

It is important as it is used to generate the data returned by the Orders Feed endpoint. Note that orders (and order items) made through your Open Booking API should only be "soft" deleted, as they are expected to always appear in your orders feed.

#### OrderItem

An object that associates a bookable opportunity with a specific order. This might also include other information relevant to the booked opportunity.

#### Offer

An offer represents the price of an opportunity, or prices if there are multiple options based on e.g. the age of the attendee. If this concept does not exist in your system, you might be able to build it from other existing data.

#### Seller

A table that represents Sellers, organizations or individuals who organize the events or provide the facilities. Existing "organisation" tables may serve this purpose. This is only required if you have a multiple seller booking system.

#### Authentication

We will cover this in detail later in [Authentication](authentication.md).

## Adapting your booking system

If the data model described above is similar to your booking system's, then it is likely you will only have to add a few columns to your database tables to work with the Open Booking API.&#x20;

However if it is significantly different you will have to make more substantial changes. You might choose to adapt your system to the Open Booking data model, to add new tables for concepts that do not exist in your current system, or even build a separate service for the API.

Whichever approach you take we recommend first attempting to implement the C1 endpoint, and ideally C1 and B too, within your existing system to get an idea of where issues are going arise.&#x20;

## Up next

Next we finally get started with some real work, implementing the endpoint for Checkpoint 1.
