# Deleting items

Now we are publishing all our items, but naturally you will delete records from your database, but this should not result in your deleting items in your feed.

The reason for this is that returning feed consumers will not know the item is gone and it will stay in their database forever.

The best solution for this is to implement soft deletes in your database. When deleting a record you should add a `deleted` boolean or better still use a `deleted_at` timestamp.

The details of implementing soft deletes can be tricky, so it is recommended that you use an existing library or plugin for your language/framework. For example the [Paranoia](https://github.com/rubysherpas/paranoia) library for Ruby on Rails, or the [SoftDelete](https://laravel.com/api/9.x/Illuminate/Database/Eloquent/SoftDeletes.html) trait in Laravel.

{% hint style="info" %}
If you are concerned that your database or feed is too big, don't worry. We'll explain how to handle this later in the [Feed performance](efficient-database-queries.md#removing-items-from-feed-after-retention-period) section.
{% endhint %}

Now your items can be soft deleted, make your feed as before but for items that are "deleted" set the state to "deleted" and remove the "data" field.

{% hint style="info" %}
Remember:  empty objects/arrays/strings and nulls should be removed from items
{% endhint %}

Now you've understood how deleting items in your feed, let's move on to checking that the feed you've created avoids the common implementation pitfalls.&#x20;

