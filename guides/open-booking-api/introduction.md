# Introduction

## Before getting stared

You will need to have set up an Open Opportunity feed before you start creating your open booking API. Your feeds should carry all the information that is required to make a booking in your system. Once your comfortable that you feed has the information you need - you can get started.

## What is the Open Booking API for?

The Open Booking API is a collection of endpoints that you can build which enable other applications to make bookings in your system. Here's a high level overview of the actors and relationships between them.

![](../../.gitbook/assets/image.png)

#### Customer

The user who places a booking using an application provided by a [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker). The [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) is not necessarily the attendee, e.g. a parent may book on behalf of their child.

#### Broker

The organisation, independent of the [Seller](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-seller), that provides an application allowing [Customers](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer) to make bookings. For simplicity the API client is referred to as the [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) throughout this specification, however other API clients are permissible, and "[Booking Partner](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-partner)" is the collective term for different types of clients of this API.

#### Booking System (you)

The organisation providing an application that maintains bookable inventory on behalf of the [Seller](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-seller). The platform or service that provides a server-side implementation of this API.

#### Seller

The organisation providing access to events or facilities via a [Booking System](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-booking-system) e.g. a leisure provider running yoga classes.

#### Payment Provider

The notional service providing payment processing between the [Customer](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-customer), [Broker](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-broker) and [Seller](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-seller). Although this specification refers to the [Payment Provider](https://openactive.io/open-booking-api/EditorsDraft/1.0CR3/#dfn-payment-provider) as a single notional service, it may in reality be composed of multiple connected services providing the same functionality to allow for the widest variety of business models.

## What you are going to make

You are going to make API endpoints that enable a customer to go through a journey which is outlined in three steps:

1. Select&#x20;
   * Choose an item to purchase
2. Identify
   * Submit personal details
3. Book and Pay
   * Submit payment details

These steps indicate the flow of information required to make a booking but they do not need to be presented customer (e.g. if personal details or payment details are already known).

The endpoints will deal with all situations in the journey, abandonment, cancellation, errors as well as exposing orders that have been completed.

## Approach

This guide will take you through the implementation of the Open Booking API for your booking system. Each stage will take the following approach:

* Begin with a simple or previously understood concept&#x20;
* Add details and some complexity
* Validate and test the implementation
* Move on to the next concept

There will be information along the way that helps you decide how best to implement for your particular booking system. Let's get started with a more detailed look at the end points we will be creating
