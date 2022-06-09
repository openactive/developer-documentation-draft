# Going live

Congratulations, you now have a working Open Booking API!&#x20;

You might have already deployed to a staging environment but if not it is recommended you do so to iron out any issues with running your API in your production environment. It should also be fine to deploy to production, though you may want to disable your dataset site and restrict access through your OpenID authentication in production for now.

There are a few things left to consider when going live:

* Testing your staging environment with the test suite.
* Adding Open Booking API administration tools to your booking system.
* Making your API discoverable by the OpenActive community.

## Testing staging

Testing your staging environment is not that different to testing locally.&#x20;

In the OpenActive test suite repo make a copy of your development configuration file `config/dev.json` called `staging.json` and change the following fields:

* broker -> datasetSiteUrl should point to your staging dataset site URL
* the sellers -> primary/secondary -> @id fields should be valid IDs for your staging OpenID authentication

Now run the tests against your staging environment:

```
NODE_ENV=staging npm start -- core
```

The tests should pass the same as they do in your development environment.&#x20;

### Testing production

Testing the Open Booking API in production with the test suite is not advised.&#x20;

* If you have integrationTests -> useRandomOpportunities set to `true` then the test suite will book random opportunities in your system which is obviously not a good idea in production.&#x20;
* Running in controlled mode would not have the same issue but it is not safe to enable the test interface required by controlled mode in a production environment.

Testing your production feeds is possible with the following test suite command

```
npm run validate-feeds "<your production dataset site URL>"
```

## Administration tools

### Single seller systems

If you have a single seller system you may want to provide a user interface to administrators. This could be to help with debugging feeds, or to provide metrics on how your feeds and API are being used. You may already have these administration tools and can simply integrate the Open Booking API in a way that makes sense for your booking system.

However, for single seller systems, none of these tools are required to go live.

### Multiple seller systems

If you have a multiple seller system there are some administration tools you must provide for your sellers before you go live.

#### Opportunity feed opt-in

If you do not own the data to be published, sellers must have a user interface where they can opt-in to publishing their data in your feeds.

#### Booking API booking partners

Sellers should have a user interface where they can choose which booking partners are approved to book their opportunities through your API. Here "booking partner" means the authenticating party which represents one or more brokers.

You are encouraged to give sellers control over the seller to broker relationship and therefore not to use a simple on/off option that enables the API for all booking partners.

#### Terms and conditions

Sellers should be able to provide URLs for their terms and conditions and privacy policy, to be published in the feed.

#### Feed validation interface

You should provide an interface that allows sellers to see how their data will be rendered in the feeds and to see any missing or invalid data. This will allow sellers to make any adjustments to their data so booking partners can make bookings through the API.

#### Reporting

Sellers should be able to see information on bookings that have been made through the API. This could be a simple list of bookings made through the API with relevant metadata like broker, activity, etc. You may also want to provide useful aggregate metrics such as bookings-per-broker or similar.
