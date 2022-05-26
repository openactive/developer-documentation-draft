# Authentication

Your booking system has an opportunity feed, is able to receive orders, and can handle errors, but one crucial part is missing: authentication. You need to be able to tell who is calling your API and whether they have permission to access the data they are requesting.&#x20;

So far we have stubbed out authentication so that we can test our booking flows, now we will look at how to implement it for your API.

## Choosing an authentication method

You probably already have authentication for your booking system but implementing it for an API is different than for users using your system directly. The most common options for securing an API are:

* API secret keys in the query parameters or HTTP headers
* OAuth

The Open Booking API standard does not require a particular authentication method. However we recommend using OAuth as it is widely used, well understood, and open source implementations are available in most languages.

??? Authenticating booking partner vs broker distinction

## OAuth and OpenID connect

??? Maybe just copy this from the .NET tutorial

Describe OAuth

Describe OpenID

Link to popular libraries in different languages

Scopes: [https://tutorials.openactive.io/open-booking-sdk/quick-start-guide/storebookingengine/day-8-authentication#endpoint-scopes](https://tutorials.openactive.io/open-booking-sdk/quick-start-guide/storebookingengine/day-8-authentication#endpoint-scopes)

Client credentials flow

Authorization code flow for multiple seller systems
