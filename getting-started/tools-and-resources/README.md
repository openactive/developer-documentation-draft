---
description: Ensure you've taken these steps before you get started with the guides
---

# Tools and resources

## Reference implementation

This is an important resource for your implementation. It is an example of how once implemented correctly an Open Opportunity Feed will appear to users. We will be mentioning it throughout the guides and using it in various examples. If you'd like to get familiar with it now you can [view it here](https://reference-implementation.openactive.io/OpenActive).

## JSON viewer extensions

You will be sharing your booking system data via what is called an Open Opportunity Feed. This is a feed of JSON objects - and it can be very helpful to use a browser extension that makes the JSON easy to read such as [JSON viewer](https://github.com/tulios/json-viewer). This will help you also when examining the reference implementation.&#x20;

## OpenActive validator

As you work through the implementation you will want to check that the Open Opportunity feeds and Open Booking API responses are returning correctly. For this you can use the [OpenActive validator](https://validator.openactive.io/rpde). Don't worry if you don't understand all the terminology at this point, it will be introduced both guides.

### RPDE validation

The [RPDE ](https://validator.openactive.io/rpde)validator is for testing a Open Opportunity Feed URL, particularly that the pages are formatted and operating correctly.

For example you can copy and paste the reference implementation URL below into the validation input bar, click validate and you will see the validator tests run, and results appear.

[`https://reference-implementation.openactive.io/feeds/scheduled-sessions`](https://reference-implementation.openactive.io/feeds/scheduled-sessions)``

### Model validation

The [Model](https://validator.openactive.io/) validator is for testing JSON produced by your Open Booking API and Open Opportunity feed implementation.

For example you can take the code block below and copy it into the text editor on the left hand side of the validator.&#x20;

You can select from the "MODE" drop down menu OrderQuote Creation C1 Request and then click validate. If you add and remove sections of the request or format the request incorrectly the validation with an error message that will suggest corrections to your request.&#x20;

<details>

<summary>Example OrderQuote C1 request</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "OrderQuote",
  "brokerRole": "https://openactive.io/AgentBroker",
  "broker": {
    "@type": "Organization",
    "name": "MyFitnessApp",
    "email": "contact@myfitnessapp.example.com",
    "url": "https://myfitnessapp.example.com",
    "description": "A fitness app for all the community",
    "logo": {
      "@type": "ImageObject",
      "url": "http://data.myfitnessapp.org.uk/images/logo.png"
    },
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "Alan Peacock Way",
      "addressLocality": "Village East",
      "addressRegion": "Middlesbrough",
      "postalCode": "TS4 3AE",
      "addressCountry": "GB"
    }
  },
  "seller": {
    "@type": "Organization",
    "@id": "https://example.com/api/organisations/123"
  },
  "orderedItem": [
    {
      "@type": "OrderItem",
      "position": 0,
      "acceptedOffer": {
        "@type": "Offer",
        "@id": "https://example.com/events/452#/offers/878"
      },
      "orderedItem": {
        "@type": "ScheduledSession",
        "@id": "https://example.com/events/452/subEvents/132"
      }
    }
  ]
}
```

</details>

The validator also contains samples of JSON under the "SAMPLES" dropdown, which may be useful to refer to when working on you implementation.&#x20;



## OpenActive test suite

Use this tool to validate your implementation once you think you have it working

Can be configured to test the features your booking system implements

For now just download and make sure it works by running it against the reference implementation

### Instructions

Visit [https://github.com/openactive/openactive-test-suite](https://github.com/openactive/openactive-test-suite)

Ensure you have node installed&#x20;

Clone the repository (maybe into your app)

Change into directory that you cloned into openactive-test-suite

Run npm install

to check the installation has worked run`npm start --core` this should run your test suite against our test implementation and it should take around 60 seconds - if this breaks get in touch.

Next up Config your setup, copy default as follows (linux command line)

`cp config/default.json config/dev.json`

Open the config file created

In the JSON under broker, make sure datasetSiteUrl points to a dataSiteUrl that you are going to create locally in dev e.g `http://localhost:4000/openactive`

export NODE\_ENV=dev to bash profile

export PORT=4567 or another available port number of your choice&#x20;

Now to run your first test

`NODE_ENV=dev npm start -- --runInBand test/features/core/dataset-site/`

You should get an error as you have not setup your dataset site yet&#x20;

```
Broker Microservice running on port 4567
Check http://localhost:4567/status for current harvesting status
Downloading Dataset Site JSON-LD from "https://localhost:4000/openactive"...
Error while extracting JSON-LD from datasetSiteUrl "https://localhost:4000/openactive"
```

We will move on to setting up your dataset site in our first guide.
