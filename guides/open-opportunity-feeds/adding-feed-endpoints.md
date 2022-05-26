---
description: Creating a generic URL which returns a JSON object
---

# Starting your feed

In order to create an opportunity feed you need to create a URL where your feed can be accessed via a HTTP request.

To start you may wish to use a baseUri called `feeds` to make it clear which group these endpoints are part. Create your first endpoint`{baseUri}/events` (you can change this name later on) that returns successfully.&#x20;

## Top level fields

Your feed will be made up of JSON objects. To get started will start by returning three top level fields from your `{baseUri}/events`

### Request and Response

Setup your endpoint so that it has three top level fields `next`, `items`, and `license`. So it returns this example for a GET request.

```
{
  "next": "{baseUri}/events",
  "items": [],
  "licence": "https://creativecommons.org/licenses/by/4.0/"
}  
```

The `next` URL is a precomputed URL that will be called by the Broker to get the next page of data in your feed. The `next` URL **must** be calculated from the last item used to generate the current page, and use the current page's own URL if no items exist - which is what we will do for now.

`Licence` is a link to the licence under which the data has been published.

`items` is where an array of items will be found, for now this is an empty array but we will explain what will go in this array next.&#x20;

??? You can check your implementation by running this postman link ???

## What are Items?

An item is the generic term for the object listed in your feed. These items are listed in the array of the items field, that is currently empty in your response.

### Request and Response

Change your endpoint `{baseUri}/events`so that it returns the fields shown in the example below:

```
{
  "state": "updated",
  "kind": "ScheduledSession",
  "id": 1,
  "modified": 637884288000000000,
  "data": {}
}
```

The `state` property must either be set to `"updated"` or value `"deleted"`. Deleted items are included in the response with a `"deleted"` state.

The `kind` property allows for the representation of different types of items, which we will come onto when discussing the different types of data you can use in your feed (??? link)

The `id` property is the unique identifier of the item. Two items **must not** share the same `id`.&#x20;

The `modified` property should contain either the modified timestamp or change number of the item. It **must** be appropriately comparable to itself as either a string or integer, representing a chronological ordering. It **must** always be updated when **any** for the item is updated, and such an update **must** set the value of the property to be greater than or equal to all existing `modified` values.&#x20;

The `data` property is present when `state` us `"updated"` but should not be present if the `state` is `"deleted"` . We will come on to what should populate the `data` property in the next section of the guide.

??? Check you response against postman link ????

## Next steps

Now you have a feed with items, but no actual data in the items. Next we will add data to your feed.
