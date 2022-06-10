---
description: Ensure you've taken these steps before you get started with the guides
---

# Tools and resources

## Reference implementation

This is an important resource for your implementation. It is an example of how, once implemented correctly, an Open Opportunity Feed will appear to users. We will mention it throughout the guides and use it in various examples. If you'd like to get familiar with it now, you can [view it here](https://reference-implementation.openactive.io/OpenActive).

## JSON viewer extensions

You will be sharing your booking system data via what is called an Open Opportunity Feed, which is a feed of JSON objects. It can be very helpful to use a browser extension that makes the JSON easy to read such as [JSON viewer](https://github.com/tulios/json-viewer). The JSON viewer will help you also when examining the reference implementation.&#x20;

## OpenActive validator

As you work through the implementation, you will want to check that the Open Opportunity feeds and Open Booking API responses are returning correctly. The [OpenActive validator](https://validator.openactive.io/rpde) has been created to make checking your responses easier, and don't worry if you don't understand all the terminology at this point, it will be introduced both guides. The validator runs in two modes, explained below:

### RPDE validation

The [RPDE ](https://validator.openactive.io/rpde)validator is for testing an Open Opportunity Feed URL, particularly that the pages are formatted and operating correctly.

For example you can copy and paste the reference implementation URL below into the validation input bar, click validate, and you will see the validator tests run and results appear.

[`https://reference-implementation.openactive.io/feeds/scheduled-sessions`](https://reference-implementation.openactive.io/feeds/scheduled-sessions)``

### Model validation

The [Model](https://validator.openactive.io/) validator is for testing JSON produced by your Open Booking API and Open Opportunity feed implementation.

For example, you can take the code block below and copy it into the text editor on the left-hand side of the validator.&#x20;

You can select from the "MODE" drop-down menu OrderQuote Creation C1 Request and then click validate. If you add and remove sections of the request or format the request incorrectly the validation will display an error message that will suggest corrections to your request.&#x20;

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

The validator also contains samples of JSON under the "SAMPLES" drop-down, which may be useful to refer to when working on your implementation.&#x20;



## OpenActive test suite

The OpenActive test suite enables you to test both your Open Opportunity Feeds and your Open Booking API implementation. You will be able to configure the test suite to test features that are relevant to your booking system, but for now, you need to download it and make sure it works by running it against the [reference implementation](./#reference-implementation).&#x20;

### Steps to follow

* Ensure you have node installed already&#x20;
* Clone the repository [https://github.com/openactive/openactive-test-suite](https://github.com/openactive/openactive-test-suite)
* Change into the directory of the openactive-test-suite that you just cloned
* Run `npm install`

You are now ready to test the installation by running:

```
npm start -- core
```

By default the test suite will run against the Open Booking API reference implementation. The test suite will harvest feeds then run all the core tests. It should take a few minutes to run then report that all core tests have passed.

Now you need to set up your config so the test suite will run against your own .&#x20;

* Copy the default config `config/default.json` to your config `config/dev.json`
* Open the `config/dev.json` file you just created
* In the config, under broker, make sure `datasetSiteUrl` points to a local URL where you will put your dataset site e.g. `http://localhost:4000/openactive`

Now run the following command:

```
NODE_ENV=dev npm start -- --runInBand test/features/core/dataset-site/
```

You should get an error, shown below, as you have not setup your dataset site yet:

```
Broker Microservice running on port 4567
Check http://localhost:4567/status for current harvesting status
Downloading Dataset Site JSON-LD from "https://localhost:4000/openactive"...
Error while extracting JSON-LD from datasetSiteUrl "https://localhost:4000/openactive"
```

The test suite is up and running. We will set up your dataset site with tests passing in our first guide.
