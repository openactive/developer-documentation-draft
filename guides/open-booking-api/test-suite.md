# Test suite

{% hint style="warning" %}
You need Node JS 14.16.0 or later installed to run the test suite&#x20;
{% endhint %}

{% hint style="info" %}
This section will be easier to follow if you have first validated your feeds as described in [Validating your feed](../open-opportunity-feeds/validating-your-feed.md).
{% endhint %}

So far we have been using Postman to test your API responses but now you have the basic functionality of an Open Booking API in place, it is a good time to start using a more comprehensive testing strategy.

The [OpenActive test suite](https://github.com/openactive/openactive-test-suite) is a tool for testing booking systems against the OpenActive standards. The test suite can be configured to match your implementation of the Open Booking API and once all tests pass you can be reasonably sure that any OpenActive broker will be able to integrate with your system.

You can find a more comprehensive explanation of the test suite in the [Tools and Resources](../../getting-started/tools-and-resources/#openactive-test-suite) section.

## Setup

Clone the test suite project on to your computer if you haven't already.

```
git clone https://github.com/openactive/openactive-test-suite.git
```

You may have already completed this step from the tools and resources section, but in case you have not - you need to configure the test suite to point to the local URL of your booking system running in development. Make a copy of the file `default.json` in the config folder of the test suite and call it `dev.json` _._ This file will contain all the information the test suite needs to test your API implementation. Don't worry about all the different settings for now, we will go through everything you need to change step by step.&#x20;

All you need to do for now is change the broker -> datasetSiteUrl property to the local URL of the dataset site you set up in [Publishing your feeds](../open-opportunity-feeds/dataset-site.md#dataset-site).&#x20;

## Running the test suite

Run the test suite against your booking system:

```
NODE_ENV=dev npm start -- core
```

{% hint style="warning" %}
The test suite runs a mock broker service on port 3000. If you already have something running on that port you can change the broker service's port by adding the environment variable `PORT=<port>` to the command.
{% endhint %}

The test suite uses the `NODE_ENV` environment variable to choose which config file to use, here we have told it to use the `dev.json` file you just created. The `core` option tells the test suite to run just the core API and feeds tests.

You should see the following error in the output:

```
  ***************************************************************************
  |                   OpenID Connect Authentication Error!                  |
  |                                                                         |
  | NOTE: Due to OpenID Connect Authentication failure, tests unrelated to  |
  | authentication will not run and open data harvesting will be skipped.   |
  | Please use the 'authentication' tests to debug authentication,          |
  | in order to allow other tests to run.                                   |
  |                                                                         |
  ***************************************************************************
```

{% hint style="info" %}
If you get a different error you may have not set up your dataset site or feeds correctly. The error shown should give you a hint at how to fix it. If you get stuck you can get help on the [OpenActive Slack](https://openactive.io/public-chat/).
{% endhint %}

This is because the test suite expects your API to use [OpenID authentication](authentication.md), which we have not implemented yet. This is not essential to test the basic functionality of your API, so for now we will add fake authentication.

## Adding fake authentication

We will use the [OpenActive reference implementation](https://reference-implementation.openactive.io/OpenActive) authentication server to trick the test suite in to thinking we have implemented authentication. Change the accessService -> authenticationAuthority field in your dataset site's embedded JSON-LD schema to this URL:

```
https://auth.reference-implementation.openactive.io/
```

The default credentials in your `dev.json` configuration will work with the reference authentication server. Your endpoints do not actually have authentication enabled so this should be enough for the tests to run.

## Running the test suite (again)

Run the test suite again.

```
NODE_ENV=dev npm start -- core
```

This time the test suite will start harvesting your feeds, and after a minute or two (depending on the size of your feeds) it will start to run the tests. The final output should look something like this:

```
Ran 2516 tests in 11.776s
✅ 1228 passing
❌ 1288 failing

Pausing broker microservice...
Harvesting paused

When data feeds are stable or when using 'controlled' mode, tests can be rerun
quickly without reharvesting. However, for 'random' mode, if data feeds have
been updated without the RPDE 'modified' property being updated (e.g. when
implementing the RPDE feed itself), please exit the test suite and rerun it,
in order to harvest the latest data.
```

Some of the tests should pass, however many of them will fail. The default configuration for the test suite tests some features that you have not implemented yet, and possibly some that you may not need to implement for your booking system.&#x20;

Let's adjust the configuration so that it better matches your implementation.

## Configuration

Open the `config/dev.json` file you created earlier, it should look something like this:

<details>

<summary>config/dev.json</summary>

```
{
  "ci": false,
  "consoleOutputLevel": "dot",
  "broker": {
    "outputPath": "./output/",
    "datasetSiteUrl": "https://localhost:5001/openactive",
    "requestLogging": false,
    "waitForHarvestCompletion": true,
    "verbose": false,
    "loginPagesSelectors": {
      "username": "[name='username' i]",
      "password": "[name='password' i]",
      "button": ".btn-primary"
    },
    "bookingPartners": {
      "primary": {
        "authentication": {
          "initialAccessToken": "openactive_test_suite_client_12345xaq"
        }
      },
      "secondary": {
        "authentication": {
          "clientCredentials": {
            "clientId": "clientid_800",
            "clientSecret": "secret"
          }
        }
      }
    }
  },
  "integrationTests": {
    "outputPath": "./output/",
    "useRandomOpportunities": true,
    "generateConformanceCertificate": false,
    "conformanceCertificateId": "https://www.example.com/",
    "conformanceCertificatePath": "./conformance/",
    "requestHeaderLogging": true,
    "openBookingApiRequestTimeout": 20000,
    "waitForItemToUpdateInFeedTimeout": 60000,
    "testTimeout": 360000,
    "maximumNumberOfSimultaneousBookings": 4,
    "additionalReporters": [],
    "bookableOpportunityTypesInScope": {
      "ScheduledSession": true,
      "FacilityUseSlot": true,
      "IndividualFacilityUseSlot": false,
      "CourseInstance": false,
      "CourseInstanceSubEvent": false,
      "HeadlineEvent": false,
      "HeadlineEventSubEvent": false,
      "Event": false,
      "OnDemandEvent": false
    },
    "bookingFlowsInScope": {
      "OpenBookingSimpleFlow": true,
      "OpenBookingApprovalFlow": true
    },
    "implementedFeatures": {
      "test-interface": true,
      "opportunity-feed": null,
      "dataset-site": true,
      "availability-check": true,
      "common-error-conditions": true,
      "amending-order-quote": true,
      "order-deletion": true,
      "agent-broker": true,
      "free-opportunities": true,
      "non-free-opportunities": true,
      "prepayment-required": true,
      "prepayment-required-unavailable": true,
      "prepayment-optional": true,
      "prepayment-unavailable": true,
      "minimal-proposal": true,
      "proposal-amendment": true,
      "multiple-sellers": true,
      "payment-reconciliation-detail-validation": true,
      "booking-window": true,
      "customer-requested-cancellation": true,
      "customer-requested-cancellation-always-allowed": false,
      "cancellation-window": true,
      "seller-requested-cancellation": true,
      "seller-requested-cancellation-message": true,
      "seller-requested-replacement": true,
      "named-leasing": true,
      "anonymous-leasing": true,
      "customer-details-capture-non-essential": true,
      "customer-details-capture-identifier": true,
      "attendee-details-capture": true,
      "additional-details-capture": true,
      "access-code": true,
      "access-pass-image": true,
      "access-pass-barcode-seller-provided": true,
      "reseller-broker": true,
      "reseller-broker-tax-calculation": null,
      "no-broker": true,
      "business-to-consumer-tax-calculation-net": true,
      "business-to-consumer-tax-calculation-gross": true,
      "business-to-business-tax-calculation-net": true,
      "business-to-business-tax-calculation-gross": true,
      "offer-overrides": null,
      "dynamic-payment": null,
      "booking-restrictions": null,
      "customer-notice-notifications": true,
      "change-of-logistics-notifications": false,
      "access-code-update-notifications": true,
      "access-pass-update-notifications": true,
      "opportunity-attendance-updates": true,
      "terms-of-service-for-booking-system": true,
      "terms-of-service-for-seller": true,
      "terms-of-service-with-consent": null,
      "terms-of-service-with-consent-with-date-modified": null,
      "booking-partner-authentication": true,
      "dynamic-client-registration": true,
      "access-channel": true,
      "access-channel-update-notifications": true
    },
    "testDatasetIdentifier": "uat-ci",
    "bookingPartnersForSpecificTests": {
      "dynamicPrimary": {
        "authentication": {
          "initialAccessToken": "dynamic-primary-745ddf2d13019ce8b69c"
        }
      },
      "dynamicSecondary": {
        "authentication": {
          "initialAccessToken": "dynamic-secondary-a21518cb57af7b6052df"
        }
      },
      "authorizationPersisted": {
        "authentication": {
          "clientCredentials": {
            "clientId": "clientid_801",
            "clientSecret": "secret"
          }
        }
      }
    }
  },
  "sellers": {
    "primary": {
      "@type": "Organization",
      "@id": "https://reference-implementation.openactive.io/api/identifiers/sellers/1",
      "authentication": {
        "loginCredentials": {
          "username": "test1",
          "password": "test1"
        }
      },
      "taxMode": "https://openactive.io/TaxGross",
      "paymentReconciliationDetails": {
        "name": "AcmeBroker Points",
        "accountId": "SN1593",
        "paymentProviderId": "STRIPE"
      }
    },
    "secondary": {
      "@type": "Organization",
      "@id": "https://reference-implementation.openactive.io/api/identifiers/sellers/2",
      "authentication": {
        "loginCredentials": {
          "username": "test2",
          "password": "test2"
        }
      },
      "taxMode": "https://openactive.io/TaxNet"
    }
  }
}
```

</details>

The section we are interested in is `integrationTests` . Change the following parts of that section to match the current state of your API implementation.

* `bookableOpportunityTypesInScope` By default `ScheduledSession` and `FacilityUseSlot` are set to true. This means the test suite will only run tests that are relevant to those opportunity types. Configure this so that opportunity types that currently work with your API are set to `true` and all the rest are set to `false`.
* `bookingFlowsInScope` By default both simple and approval booking flows are tested. If you have not implemented the approval booking flow, set `OpenBookingApprovalFlow` to `false` .
* `implementedFeatures` This is where you will eventually configure the test suite to enable the specific features your booking system implements. For now, set all fields that are currently set to `true` to false, except for `dataset-site` , `opportunity-feed`, and `multiple-sellers`, `named-leasing`, or `anonymous-leasing` if applicable.

Now run the test suite again and fewer tests will run, and more of them should pass. If any have failed go back and fix your implementation so that they pass.

## Random and Controlled mode

As it is currently configured the test suite will pick random opportunities from your feeds to test your API. After several runs, it might run out of bookable opportunities to test against and therefore fail.&#x20;

One way to solve this issue is to run a script to reseed your development database so that the test suite has fresh data to run against. Each time you run the test suite, first run your script and the tests should work as expected. This does mean that the test suite will need to harvest your feeds every time you reseed your database.

A more robust alternative is to use the "controlled" mode of the test suite which can be turned on by setting integrationTests -> useRandomOpportunities in the configuration to `false`. In controlled mode the test suite will reseed your database for you, removing the need for your own script and for the test suite to harvest your feeds on every run.&#x20;

Controlled mode requires your booking system to implement the OpenActive test interface. You can read about how to implement this in [Implementing the test interface](../../reference/implementing-the-test-interface.md). Implementing the test interface so you can use controlled mode is not essential but it is strongly recommended as it will allow you to iterate on your booking API implementation more quickly.
