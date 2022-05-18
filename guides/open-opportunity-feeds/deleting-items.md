# Deleting items

So far we are publishing all our items, but what if we want to delete one

Why not just remove it from the feed? Returning feed consumers will not know the item is gone and it will stay in their database forever.

Solution: soft deletes

Instead of removing items from your DB, add a `deleted` boolean or (preferred) `deleted_at` timestamp.

_Include options for soft deletes in various ORMs etc._

Sidebar: what if my DB and/or feed would be too big?&#x20;

Now your items can be soft deleted, make your feed as before but for items that are "deleted" set the state to "deleted" and remove the "data" field

Remember: empty objects/arrays/strings and nulls should be removed from items

