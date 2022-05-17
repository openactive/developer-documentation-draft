---
description: Ensure you've taken these steps before you get started with the guides
---

# Getting started

Your going to want to start by setting up the test suite to test your implementation

Visit [https://github.com/openactive/openactive-test-suite](https://github.com/openactive/openactive-test-suite)

Ensure you have node installed&#x20;

Clone the repository (maybe into your app)

Change into directory that you cloned into openactive-test-suite

Run npm install

to check the installation has worked run`npm start --core` this should run your test suite against our test implementation and it should take around 60 seconds - if this breaks get in touch.

Next up Config your setup, copy default as follows (linux command line)

`cp config/default.json config/dev.json`

Open the config file created

In the JSON under broker, make sure datasetSiteUrl points to a dataSiteUrl that you are going to create locally in dev e.g `https://localhost:4000/openactive`

export NODE\_ENV=dev to bash profile

export PORT=4567 or another available port number of your choice&#x20;

Now to run your first test

`npm start -- --runInBand test/openactive-test-suite/packages/openactive-integration-tests/test/features/core/dataset-site/implemented/dataset-site-jsonld-valid-test.js`

You should get an error as you have not setup your dataset site yet&#x20;

```
Broker Microservice running on port 4567
Check http://localhost:4567/status for current harvesting status
Downloading Dataset Site JSON-LD from "https://localhost:4000/openactive"...
Error while extracting JSON-LD from datasetSiteUrl "https://localhost:4000/openactive"
Error: connect ECONNREFUSED 127.0.0.1:4000
    at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1187:16)
```

Link to postman test goes here

We will move on to setting up your dataset site in our first guide.
