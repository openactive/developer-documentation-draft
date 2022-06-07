# Validating your feed

Now you have a feed setup and learnt about potential pitfalls, it's time to double check that your feed is valid and operating correctly. OpenActive provides two tools for validating your feed:

* The [OpenActive Validator](https://validator.openactive.io/rpde)
* The [OpenActive test suite](https://github.com/openactive/openactive-test-suite)

## OpenActive Validator

The OpenActive Validator can be found at [validator.openactive.io](https://validator.openactive.io/). It has a variety of modes for validating feeds and API queries against the relevant OpenActive specifications.

To validate your feeds, we will first need to set up a tunnel so that the validator can access the feeds running locally on your machine, then we will go through validating a feed.

### Setting up an ngrok tunnel

At the moment the your feeds are not accessible to the wider internet, so the validator will not be able to access them. We are going to use a command line tool called ngrok to temporarily expose your feeds to the internet.

First follow the [installation instructions](https://ngrok.com/download) for ngrok for your operating system.

Start a tunnel for your local booking system using ngrok. For example if your booking system is running on port 5001:

```
ngrok http 5001
```

You will get output that looks something like this:

```
ngrok                                                                                 (Ctrl+C to quit)
                                                                                                      
Session Status                online                                                                  
Session Expires               1 hour, 59 minutes                                                      
Update                        update available (version 3.0.4, Ctrl-U to update)                      
Terms of Service              https://ngrok.com/tos                                                   
Version                       3.0.3                                                                   
Region                        Europe (eu)                                                             
Latency                       31.427902ms                                                             
Web Interface                 http://127.0.0.1:4040                                                   
Forwarding                    https://e22b-2a0e-1c80-2-00-130.eu.ngrok.io -> http://localhost:5001    
                                                                                                      
Connections                   ttl     opn     rt1     rt5     p50     p90                             
                              0       0       0.00    0.00    0.00    0.00 
```

If you visit the URL from "Forwarding" in the browser, it should tunnel to your booking system running locally.

### Validating your feed

Now your local booking system is available on the internet visit [validator.openactive.io](https://validator.openactive.io/).

![](<../../.gitbook/assets/image (3).png>)

The OpenActive validator is flexible and can be used in a number of different ways you are encouraged to explore them.&#x20;

For now click the "LOAD URL" button and paste the ngrok-tunneled URL for one of your feeds in to the input that appears.

![](<../../.gitbook/assets/image (4).png>)

Click "OPEN" and the validator will show errors, warnings, and tips for your feed on the right hand side.

![](<../../.gitbook/assets/image (2).png>)

{% hint style="info" %}
If the output of the validator doesn't look quite right, make sure the "Opportunity RDPE feed" option is selected in the "MODE" dropdown.
{% endhint %}

## OpenActive test suite

For performance reasons the OpenActive validator does not check every item in your feed. You may also be working in an environment where tunneling your locally running booking system to the wider internet is not an option. The OpenActive test suite can run a more complete check of your feeds and runs completely locally.

### Installing the OpenActive test suite

{% hint style="warning" %}
You need Node JS 14.16.0 or later installed to run the test suite&#x20;
{% endhint %}

{% hint style="warning" %}
For the test suite to run you first need to set up a dataset site. Either follow the instructions for that in [Publishing your feeds](dataset-site.md#dataset-site), or use the OpenActive validator to validate your feeds for now and come back to the test suite later.
{% endhint %}

If you have not already done so, clone the test suite project and install its dependencies:

```
git clone https://github.com/openactive/openactive-test-suite.git
cd openactive-test-suite
npm install
```

The test suite has many functions which we will use when building our Open Booking API. For now we just need to use the `validate-feeds` command. For example if your dataset site is at `localhost:5001/openactive`

```
npm run validate-feeds "https://localhost:5001/openactive"
```

The test suite will harvest all the feeds listed in your dataset site. This may take a few minutes if you have a lot of data in your feeds.

If your feeds are valid, you will eventually see output like this:

```
ScheduledSession | 20000 items harvested from 40 pages | Response time: 135ms | Elapsed: 46s | Validat
SessionSeries | 2000 items harvested from 4 pages | Response time: 16ms | Elapsed: 27s | Validated: 20
FacilityUse | 2000 items harvested from 4 pages | Response time: 19ms | Elapsed: 12s | Validated: 2000
FacilityUseSlot | 20000 items harvested from 40 pages | Response time: 342ms | Elapsed: 46s | Validate
Harvesting is up-to-date

Feed validation passed
```

Otherwise the test suite should tell you any problems it finds in your feeds. Fix them and run the command again until your tests are passing.
