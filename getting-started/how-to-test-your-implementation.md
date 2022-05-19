---
description: Ensure you've taken these steps before you get started with the guides
---

# Tools and resources

## JSON viewer extensions

## OpenActive Postman workspace

This + validator are the primary tools we will use to test our implementation as we build it

Install postman instructions

Link to workspace

Postman tutorial, testing against the reference implementation

Workspace configuration

* Creating an environment
* Setting the baseUrl

## OpenActive validator

Make sure people know there is an RPDE tab and model tab

Link to website

We can use this to validate Open Opportunity and Open Booking responses as we implement them

Example how to use

## The reference implementation

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
