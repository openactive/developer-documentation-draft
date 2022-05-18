---
description: Creating a generic URL which returns a JSON object
---

# Starting your feed

In order to create an opportunity feed you need to create a URL where your feed can be accessed via a HTTP request.

To start create an endpoint called events, for example`localhost:3000/feeds/events` (you can change this name later on) that returns successfully.&#x20;

## Top level fields

Your feed is made up of JSON objects, we will start by returning three top level fields from your URL.

Setup your endpoint so that it has three top level fields `next`, `items`, and `license`. So it returns this:

```
{
  next: "localhost:3000/feeds/events",
  items: [],
  licence: "https://creativecommons.org/licenses/by/4.0/"
}  
```

You can check your implementation by running this postman link.

This is what next is blah blah blah

This is what licence is blah blah blah

This is what items is blah blah blah see next

## What are Items?

An item is the generic term for the object listed in your feed. Below is an example of one item in a feed.&#x20;

Make your URL return this in the items array.

```
{
  "state": "updated",
  "kind": "ScheduledSession",
  "id": 1,
  "modified": 637884288000000000,
  "data": {}
}
```

Check in postman

State means w

Kind means x

Id means y

Modified means z

Set data to empty for now

## Next steps

Now you have a feed with items, but no actual data in the items. Next we will add data to your feed.
