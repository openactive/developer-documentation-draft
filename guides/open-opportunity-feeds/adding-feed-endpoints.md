# Adding feed endpoints

Goal: Reader has http endpoint with feed that returns correct top level fields (that passes first postman test)

Prerequisites: postman with environment baseUrl

## Empty endpoint

Make a pair of feed endpoints e.g.

/feeds/session-series

/feeds/scheduled-sessions &#x20;

For now just have them return an empty JSON object (ie "{}")

If following in postman set environment variables sessionSeriesFeedPath and scheduledSessionsFeedPath

Run the "Empty feeds" postman tests

## Top level fields

The top level fields are always next/items/license.

For now set the next field to be the current URL

Set the license field to be "[https://creativecommons.org/licenses/by/4.0/](https://creativecommons.org/licenses/by/4.0/)"

Set the items field to an empty array

Run the postman

## Next steps

Depending on which feed type(s) match your booking system, move on to the relevant page

Links to pages
