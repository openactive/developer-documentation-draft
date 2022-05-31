---
description: How to create your dataset site
---

# Publishing your feeds

You should be ready to publish now.

But how will people know where to find your data.

## Dataset Site

Create a home page for dataset site to get this test to pass

Create a route for your page in your app e.g. `get 'openactive', to: 'openactive#show'`

Ensure this route you create matches your config created in your setup under dataSiteUrl

Create a controller for your route that enables you to render a view

Take this template[ ](https://github.com/openactive/dataset-site-template/blob/master/datasetsite.mustache)[https://github.com/openactive/dataset-site-template/blob/master/datasetsite.mustache](https://github.com/openactive/dataset-site-template/blob/master/datasetsite.mustache)

Place the template in a html view that can render mustache or convert the mustache template to your templating language

Now take this JSON template and place it in your app [https://github.com/openactive/dataset-site-template/blob/master/example.json](https://github.com/openactive/dataset-site-template/blob/master/example.json)

Update fields in JSON to match your booking system (see reference here for details)

You then need to pass variables into the mustache template that are relate to your booking system.&#x20;

Pass in the JSON file itself as a variable in the mustache template

Replace authenticationAuthority and endpointUrl to your localhost don't worry about the exact path - we will edit this later.

Re run your test and it should now pass

`NODE_ENV=dev npm start -- --runInBand test/features/core/dataset-site/`

## Listing with OpenActive

[https://developer.openactive.io/publishing-data/dataset-sites#step-6-adding-your-dataset-site-or-data-catalog-to-the-openactive-data-catalog-collection](https://developer.openactive.io/publishing-data/dataset-sites#step-6-adding-your-dataset-site-or-data-catalog-to-the-openactive-data-catalog-collection)



