---
description: >-
  Test your Open Booking API implementation, using data from your Open
  Opportunity Feeds.
---

# Testing with Postman

Postman is an API tool - it enables you to generate test requests to your local implementation and tests whether the response you are providing is correct. Postman will be used to test your Open Booking API implementation.&#x20;

{% hint style="info" %}
You should have complete your Open Opportunity Feed implementation before using setting up Postman.
{% endhint %}

Firstly you need to create a postman account and install the postman app locally, a download can be found here [https://www.postman.com/downloads/](https://www.postman.com/downloads/).

Next, you should import the Open Booking API collection into your postman.

Follow [this link](https://documenter.getpostman.com/view/21015180/Uz5DqdCf), then click "Run in Postman" and you will be prompted to run Postman locally and import the collection to a workspace.

![](<../../.gitbook/assets/Screenshot from 2022-06-01 10-09-20.png>)

![](<../../.gitbook/assets/Screenshot from 2022-06-01 10-10-21.png>)



![](<../../.gitbook/assets/Screenshot from 2022-06-01 10-10-58.png>)



### Broker Microservice setup

The postman collection you have imported operates by using a Broker Microservice that harvest data from your Open Opportunity Feeds. To set this service up follow these steps:&#x20;

* Open a terminal in a appropriate directory and run&#x20;
  * `git clone` [`https://github.com/openactive/openactive-test-suite.git`](https://github.com/openactive/openactive-test-suite.git)``
* Change into the `openactive-test-suite` directory and open the code in your code editor
* Make a copy of the `config/default.json` file, and leave the copy in the config folder
* Rename this copied file `dev.json`
* Within the `dev.json` file change the `datasetSiteUrl` to your dataset site URL and save the file
* Return to your terminal within the openactive-test-suite directory run `npm install`
* You can then start the broker, which will retrieve your feeds, to do this run:
  * `NODE_ENV=dev npm run start-broker`
* You will see the logs of the Broker Microservice running, this will keep running until it has harvested your feed. Leave it running in the background.
* In your Postman, select the Open Booking API collection, and select the 'Variables' tab.&#x20;
* Set the baseUri variable to your Dataset site URL, so the postman knows where your Open Opportunity Feeds are located.&#x20;

![](<../../.gitbook/assets/Screenshot from 2022-06-01 10-53-59.png>)

* Select the 'Sample requests' GET request and click 'Send'.&#x20;
* You should see a 200 response with a body that contains an object with data that will be used for other requests in the Postman collection.

![](<../../.gitbook/assets/Screenshot from 2022-06-01 10-55-51.png>)

You are now ready to make other requests to your Open Booking API endpoints - you may not have started creating them yet, that's okay, you can start by heading to [this guide here.](../../required-features-guides/simple-booking/)
