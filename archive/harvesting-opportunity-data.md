# Harvesting opportunity data

## **Introduction** <a href="#introduction" id="introduction"></a>

To follow along you’ll need some existing knowledge of programming, including familiarity with HTTP, JSON, and APIs.

This guide is intended to be language agnostic, and is instead a guide to strategy and a general approach that should suit multiple implementations. Where code examples exist, they are intended as quick start assets rather than being production-ready.

By the end of this tutorial you’ll have:

* An overview of the concept of how to harvest OpenActive data
* Understood the method of data transport
* Understood how to work with paging
* A plan for keeping your newly gathered data up to date

Whilst you’ll be able to follow along with this tutorial as your sole guide, you may wish to refer to the OpenActive [Realtime Paged Data Exchange](https://www.openactive.io/realtime-paged-data-exchange/) specification for greater detail at points.

## **Before we begin** <a href="#before-we-begin" id="before-we-begin"></a>

Before we dive into implementation, let’s start with a bit of background. [OpenActive](https://www.openactive.io/) intends to facilitate the sharing and use of physical activity opportunity data. To date, a number of datasets have been made available for use. You can find out more about them in our upcoming guide ‘How to find physical activity opportunity data using the OpenActive Opportunity Data Dashboard’, or through the [status dashboard](https://status.openactive.io/) itself.

This is a tutorial which aims to cover how to harvest the data from these feeds. For simplicity, we’re going to focus on harvesting data from a single source, however at the end of this tutorial we’ll include some prompters for how to approach combining data from multiple sources.

## **Data transport** <a href="#data-transport" id="data-transport"></a>

First of all, it’s important to understand the general approach to how the data is transported.

Each dataset has a URL endpoint. You can find these by visiting [status.openactive.io](http://status.openactive.io/) and selecting one of the links under the Endpoint column. For this tutorial, we’re going to use the London Sport endpoint, so find it and navigate through the link.

![](https://lh3.googleusercontent.com/38w0HRDd-i7zjzqAhu2nbbeyH62-jAGY1hfPyyAlO5slWJnJVkQQXFwaTSazASwUpPqTXR\_SoAEmj0Jh\_Iyf5IXfy9rW2dJM5CZ0jAS9KOmLLspqdBsS\_RR2h1RZz59zbw)

The OpenActive opportunity data dashboard, showing available endpoints

You should have been taken to the URL [https://opensessions.io/api/Session/GetSessionsForOpenActive](https://opensessions.io/api/Session/GetSessionsForOpenActive), and have been presented with data.

Whilst we were doing this in-browser, programmatic requests to harvest data work in exactly the same way; with an HTTP GET request to the endpoint. At present, all harvesting is done through polling: as a pull rather than as a push from the data source. This means you need to regularly harvest data from each feed to get the latest information.

The RPDE specification recommends use of standard HTTP status codes. So your code should get a 200 response for a successful request, or 4XX or 5XX errors for other problems. Be sure to check for and respect 503 errors which are used to indicate that the server is overloaded. Retry after a random interval (between 60 and 120 minutes) to ease load from multiple consumers.

To harvest all of the data from a feed your code will need to make repeated HTTP requests until it has fetched all the data. There’s no need for any authentication as all of the endpoints are freely available.Now that we understand the mechanic for transferring data, let’s move on to understanding the content of what’s returned from a request, and how we navigate through all of the content.

## **Understanding paging** <a href="#understanding-paging" id="understanding-paging"></a>

Before we grab the data there are a few key concepts that are important to understand.

### **Data is returned as pages** <a href="#data-is-returned-as-pages" id="data-is-returned-as-pages"></a>

Looking at the [URL we previously visited](https://opensessions.io/api/Session/GetSessionsForOpenActive), you’ll notice that the data isn’t extensive - there are only 10 items in the items array.

![](https://lh5.googleusercontent.com/7mdF\_pYa-nEB1c-2gLlKsE0xQbMnzHIMqE7rkkLLUD8txXq181UiPwdizyOttNxBXeBqok7RamlmkmYhcdpIaO89RJ3D5yt3qVd-LzrfbYvlH1Xl7bQWHrVEgx2DIuQXkw)

An in-browser view of the London Sport endpoint, with individual items collapsed

Each publisher will decide on an appropriate page size, and your application should not expect a set amount of items. This becomes even more relevant later on when we discuss keeping data up to date.

From the link we opened (and the image above), you’ll notice the property next, which contains a URL string. This is a required property, and the specification states that it must be an absolute URL and returned for every request. We page through the full dataset by following the URLs provided in the next property. The response from each of these URLs will be a new page of results which you can process in your application.

Follow the links a couple of times to get a feel for this, but there’s no need to go to the end (we’ll come on to that shortly).

### **How items appear in the overall list (and pages)** <a href="#how-items-appear-in-the-overall-list-and-pages" id="how-items-appear-in-the-overall-list-and-pages"></a>

If you’ve looked into some of the items that have been returned, you may notice that some opportunities are in the past. The next important point to understand here is that the specification requires strict chronological ordering of items, with newly added and modified data added to the end of the feed. There are some simple rules that govern items in the overall list:

* Every item (record) is only represented once in this overall list at a given moment.
* Every item can be distinguished by a unique identifier (so as to be able to reference it for updates/deletes)
* The item’s position in the list will depend on when it was last updated (moving to the end when changes occur).
* Each item exists in the list in perpetuity unless deleted. The full feed includes items from the past, and references to deleted items - [see section on deleted items](https://www.openactive.io/realtime-paged-data-exchange/#deleted-items). You may need to skip over older data depending on your needs.

![](https://docs.google.com/drawings/d/s4UsBghaSDhuYyFO326x-RA/image?w=305\&h=538\&rev=60\&ac=1\&parent=1JW4GXXWOfJIFhAhzaCh-VU2AQm-O8Mk1NU4fZiuHZTA)

A conceptual view of a first page of items, followed by the rest of the list. Note that item 3 would have been modified after the creation of item 7, but before the creation of item 8.

Let’s move on to putting this into practice, to understand it in context.

## **Doing our initial download** <a href="#doing-our-initial-download" id="doing-our-initial-download"></a>

When consuming the data for the first time, pages are initially downloaded sequentially to catch up with the current state of the data publisher. As we’ve seen, this is achieved by following the next property of each page until the last page is reached.

### **The last page** <a href="#the-last-page" id="the-last-page"></a>

According to the spec definition, the last page of data must have both of the following properties:

* The items property is an empty array.
* The next property matches the URL of the current page.

You can therefore feel confident that you have reached the end of the initial download once these are met.

In pseudocode, a very basic (and unrobust) page through the dataset for the first time looks something like the following. We’ll be building up this example as we go through this tutorial.

| <ul><li>Set variable for endpoint URL to be harvested</li><li>Make GET HTTP request to the URL</li><li><p>If the response contains a success HTTP status code</p><ul><li><p>If there are data items</p><ul><li>Retrieve (and store) all data items contained within the page</li><li>Request the URL in the next property</li></ul></li><li><p>Else</p><ul><li><p>If the URL in the next property is equal to the current URL being harvested</p><ul><li>End harvesting</li></ul></li><li>Else request the URL in the next property</li></ul></li></ul></li><li>Else honour response code and fail gracefully</li></ul> |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

(Please note that in practice, because the endpoints don't tend to allow cross-origin access at the moment this example won’t run and is intended for illustration only. To see a working example you can visit [https://glitch.com/\~oa-quickharvest](https://glitch.com/\~oa-quickharvest) which employs a proxy).

![](https://lh3.googleusercontent.com/bvWnOyiZQkvEotmTiZsjbaIZb9jyp1Gj6fr8\_2Xc-H0aK81iV74c\_r6xkK1c\_Tr4VTSK3QneTdak6GCsb5c9wjBQztKe6IFNY-k26mtLnv6dIJNCs1T1V2ha7vJRiSq1ww)

At this point, what you have is an as-is store of all of the items in the dataset, from as far back as they are available. Whilst this tutorial has not covered any processing of the data, so you will need to be mindful of getting it into a sensible state for your needs.

## **Deleted items** <a href="#deleted-items" id="deleted-items"></a>

Whilst we’ve captured all of the items, what we also haven’t done is given any consideration to their state.

As we’ve already discussed, items exist in the dataset in perpetuity whilst they have an "updated" state - that is when they’re created or modified. When an item is deleted in the publisher’s master data store it cannot be simply removed from the feed, as this would require all consumers to constantly request the entire data set in order to have an up-to-date view of the items.

Instead, when items are deleted from the master system, they receive a state of "deleted", are included in the feed with no \<data>, and remain in the feed for at least 7 days.

For our initial download of data, for now (we’ll revisit this in the next section), let’s discard any items where the property state is set to "deleted". These items look something like this:

```javascript
{
  "state": "deleted",
  "kind": "session",
  "id": "{d97f73fb-4718-48ee-a6a9-9c7d717ebd85}",
  "modified": 1453931925
}
```

| <ul><li><p>If the response contains a success HTTP status code</p><ul><li><p>If there are data items</p><ul><li><p>Where items do not possess the state "deleted"</p><ul><li>Retrieve (and store) all data items contained within the page</li></ul></li><li>Request the URL in the next property</li></ul></li><li><p>Else</p><ul><li><p>If the URL in the next property is equal to the current URL being harvested</p><ul><li>End harvesting</li></ul></li><li>Else request the URL in the next property</li></ul></li></ul></li><li>Else honour response code and fail gracefully</li></ul> |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

## **Keeping data up to date** <a href="#keeping-data-up-to-date" id="keeping-data-up-to-date"></a>

### **Handling updates and deletions** <a href="#handling-updates-and-deletions" id="handling-updates-and-deletions"></a>

Even with a small initial import, we run the risk of an item that we harvest early on actually being updated or deleted in the publisher’s system at the same time. As we’ve seen, this will lead to that item moving through the queue and being added to the end, so it could show up one to many times before our overall harvest is finished.

In the below diagram we may encounter item A on our first page, receive a modified version on our second page, and finally a deleted version of it in our third page. Unless our consuming application needs to track state over time, we want to ensure that we don’t create a duplicate for the modification, and don’t ignore the deletion.

![](https://docs.google.com/drawings/d/srS9\_4v\_YXwNHF1vNWMmvaw/image?w=665\&h=226\&rev=144\&ac=1\&parent=1JW4GXXWOfJIFhAhzaCh-VU2AQm-O8Mk1NU4fZiuHZTA)

Our pseudocode therefore becomes a little bit more detailed:

| <ul><li>Set variable for endpoint URL to be harvested</li><li>Make GET HTTP request to the URL</li><li><p>If the response contains a success HTTP status code</p><ul><li><p>If there are data items</p><ul><li><p>If items possess the state "deleted"</p><ul><li>Remove any existing items in our store that match the ID</li></ul></li><li><p>Else</p><ul><li><p>If item ID already exists in our store</p><ul><li>Update item details</li></ul></li><li><p>Else</p><ul><li>Create new item in our store</li></ul></li></ul></li><li>Request the URL in the next property</li></ul></li><li><p>Else</p><ul><li><p>If the URL in the next property is equal to the current URL being harvested</p><ul><li>End harvesting</li></ul></li><li>Else request the URL in the next property</li></ul></li></ul></li><li>Else honour response code and fail gracefully</li></ul> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Whilst we’ve reached the end of the feed, handled modifications and deletions, the changes don’t stop there. In order to keep your data updated in near real-time, you may wish to keep track of the URL for the last page, and infrequently poll this to check for updates. Revisiting the rules of what defines a page as the last page:

* The items property is an empty array.
* The next property matches the URL of the current page.

If items are found, your harvester should resume the behaviour of the initial download, capturing items and following the next property until it reaches a new end page. Let’s extend our pseudocode one more time:

| <ul><li><strong>Set variable for endpoint URL to be harvested</strong></li><li><strong>Make GET HTTP request to the URL</strong></li><li><p><strong>If the response contains a success HTTP status code</strong></p><ul><li><p><strong>If there are data items</strong></p><ul><li><p><strong>If items possess the state "deleted"</strong></p><ul><li><strong>Remove any existing items in our store that match the ID</strong></li></ul></li><li><p><strong>Else</strong></p><ul><li><p><strong>If item ID already exists in our store</strong></p><ul><li><strong>Update item details</strong></li></ul></li><li><p><strong>Else</strong></p><ul><li><strong>Create new item in our store</strong></li></ul></li></ul></li><li><strong>Request the URL in the next property</strong></li></ul></li><li><p><strong>Else</strong></p><ul><li><p><strong>If the URL in the next property is equal to the current URL being harvested</strong></p><ul><li><strong>Make a note of the current (last page) URL</strong></li><li><strong>End harvesting</strong></li><li><strong>Wait until next poll attempt</strong></li><li><p><strong>Poll previously noted URL</strong></p><ul><li><p><strong>If new items found</strong></p><ul><li><strong>Resume harvest</strong></li></ul></li><li><p><strong>Else</strong></p><ul><li><strong>Resume wait</strong></li></ul></li></ul></li></ul></li><li><strong>Else request the URL in the next property</strong></li></ul></li></ul></li><li><strong>Else honour response code and fail gracefully</strong></li></ul> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

As mentioned at the start of this tutorial, once you’ve mastered harvesting from a single source you may well wish to extend this by harvesting multiple sources.

Whilst this is beyond the scope of this tutorial, we’ve included some pointers for areas that you will need to consider:

* Maintaining a list of the datasets - there is a file available from the [status dashboard](https://status.openactive.io/) to help with this.
* Whether or not you wish to re-harvest the full set of data every time.
* Whether or not you plan to harvest datasets sequentially, or in parallel.

## **Resources** <a href="#resources" id="resources"></a>

The specification that covers the Realtime Pages Data Exchange format in more detail can be found here: [https://www.openactive.io/realtime-paged-data-exchange/](https://www.openactive.io/realtime-paged-data-exchange/)​

For further information on the data model, to consider when storing or processing data for your needs please see the specification: [https://www.openactive.io/modelling-opportunity-data/](https://www.openactive.io/modelling-opportunity-data/)​

If you’d like a language-specific tutorial we have one available for Ruby/Elastic Search: Indexing Opportunity Data Using Elastic Search [https://github.com/openactive/openactive-es-example](https://github.com/openactive/openactive-es-example)​

We also have a [Ruby gem](https://github.com/openactive/openactive.rb) available that covers:

* Listing all datasets that have been published as part of the project
* Fetching and harvesting a [Realtime Paged Data Exchange](https://www.openactive.io/realtime-paged-data-exchange/) (RPDE) containing opportunity data
* Identifying whether a feed conforms to the RPDE specification and/or the [Modelling Opportunity Data](https://www.openactive.io/modelling-opportunity-data/) specification

Or if you’d like to contribute a tutorial for another language please get in touch.
