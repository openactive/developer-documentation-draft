# Deleting items

Now we are publishing all our items, but naturally you will delete records from your database, but this should not result in your deleting items in your feed.

The reason for this is that returning feed consumers will not know the item is gone and it will stay in their database forever.

The best solution for this is to implement soft deletes in your database. When deleting a record you should add a `deleted` boolean or better still use a `deleted_at` timestamp.&#x20;

_??? Include options for soft deletes in various ORMs ???_

{% hint style="info" %}
If you are concerned that your database or feed is too big, don't worry. We'll explain how to handle this later.
{% endhint %}

Now your items can be soft deleted, make your feed as before but for items that are "deleted" set the state to "deleted" and remove the "data" field.&#x20;

These items do not have to be in your feed forever, they should, however remain in your feed for at least 7 days so all data consumers can remain in-sync.

{% hint style="info" %}
Remember:  empty objects/arrays/strings and nulls should be removed from items
{% endhint %}



