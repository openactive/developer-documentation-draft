---
description: >-
  A brief explanation of what this guide is for, and how it will help you with
  your Open Booking API implementation.
---

# Introduction

{% hint style="warning" %}
This guide assumes you have set up an [Open Opportunity Feed](../../required-features-guides/opportunity-feeds/). Make sure you have at least one complete and validated feed before continuing.
{% endhint %}

This guide will take you step by step through adding the Open Booking API to your booking system. Booking systems are written in different programming languages and frameworks, so you will need to apply the steps to your own system, but you should be able to add a working and complete Open Booking API to your booking system using this guide and the reference documentation that will be linked along the way.

{% hint style="info" %}
If you are using .NET you can use the OpenActive library (LINK)
{% endhint %}

But before getting started with implementing the API in your system, let's take a look at what the Open Booking API is and how it is used.

## What is the Open Booking API?

The Open Booking API is a standardised collection of HTTP endpoints that enable other applications to make bookings in your system. Here's a high level overview of the actors and relationships between them.

![](<../../.gitbook/assets/image (1).png>)

#### Customer

The user who places a booking using an application provided by a Broker. The Customer is not necessarily the attendee, e.g. a parent may book on behalf of their child.

#### Broker

The organisation, independent of the Seller, that provides an application allowing Customers to make bookings.&#x20;

#### Booking System (you)

The organisation providing an application that maintains bookable inventory on behalf of the Seller. The platform or service that provides a server-side implementation of the Open Booking API.

#### Seller

The organisation providing access to events or facilities via a Booking System e.g. a leisure provider running yoga classes.

#### Payment Provider

The service providing payment processing between the Customer, Broker and Seller. Payment is mostly outside the scope of the Open Booking API, and implementation can vary. We will go in to more detail on this later.

## What you are going to make

You are going to create endpoints that make up the Open Booking API. They will enable a customer to go through a journey that follows these three steps:

1. Select - Choose an item to purchase
2. Identify - Submit personal details
3. Book and Pay - Submit payment details

Your API implementation will also handle other common situations: abandonment; cancellation; errors; as well as fetching data and updates on orders during and after the booking process.

## Multiple and single seller systems

A significant difference between booking systems is whether they support single or multiple sellers.

* **Single Seller**: The booking system either supports a single Seller by design (e.g. an agency build for a specific organisation), or the booking system is designed to have a database provisioned for each customer (e.g. larger leisure management solutions deployed on-premise or cloud hosted).
* **Multiple Sellers**: The booking system is multi-tenancy within the same database, so for example, multiple Sellers are able to log in and manage sessions and facilities within the same underlying database (e.g. a booking system targeting at a large number of small/medium size activity providers).

Whether your system has a single or multiple sellers, you can add the Open Booking API to your system, but there are some differences in implementation which will be highlighted along the way.

## Approach

This guide will hopefully give you all the information you need to implement the Open Booking API  in your booking system. We take a step by step approach and encourage you to read the sections in order. However, we have tried where possible to also make each section useful to someone looking for an answer to a specific problem.

This is a general guide for developers using any language or framework, but where language specific tools exist, we will point them out.

If you are still deciding whether you want to add the Open Booking API to your booking system, it should still be useful to read the guide to get an idea of what is involved. We also have some tips on [estimating an OpenActive implementation project](../../getting-started/estimating-an-openactive-implementation.md).

In the next section, we will take a look at how a customer makes a booking using the Open Booking API and the specific endpoints you will need to implement in your booking system.

