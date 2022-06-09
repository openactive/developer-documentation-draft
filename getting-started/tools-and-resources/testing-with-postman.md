---
description: >-
  Test your Open Booking API implementation, using data from your Open
  Opportunity Feeds.
---

# Testing with Postman

Postman is an API tool - it enables you to generate test requests to your local implementation and tests whether the response you are providing is correct. Postman will be used to test your Open Booking API implementation.&#x20;

{% hint style="info" %}
You should have completed your Open Opportunity Feed implementation before using setting up Postman.
{% endhint %}

Firstly you need to create a Postman account and install the Postman app locally. A download can be found here [https://www.postman.com/downloads/](https://www.postman.com/downloads/).

{% embed url="https://www.postman.com/openactive/workspace/openactive-implementation-guide/overview" %}
Open Postman Workspace
{% endembed %}

### **Overview**

This Postman Collection contains "Sample" requests that use the test suite to pick a random opportunity from OpenActive feeds. It then provides C1, C2 and B requests to make a booking against this opportunity.

{% hint style="info" %}
Note that each request validates the response automatically using the OpenActive Validator within Postman, and any validator errors are displayed in the "Test Results" tab in Postman within C1, C2 etc.
{% endhint %}

### **Getting Started**

First, test this collection against the OpenActive Reference Implementation to ensure that you've installed everything correctly by doing the following:

1. Run the broker-microservice from within the OpenActive Test Suite:\
   a. `git clone https://github.com/openactive/openactive-test-suite.git`\
   b. `npm install`\
   c. `npm run start-broker`
2. "Fork" the "Open Booking API" collection in Postman
3. Ensure the "Hosted Reference Implementation" is selected from the Environment dropdown in the top right-hand corner.
4. Select the "Open Booking API" collection, and then at the bottom of the page**,** click "Get New Access Token", use the username "test1", and the password "test1" to complete the authentication flow, then click "Use Token".
5. Click one of the "Sample" requests on the left and "Run" the request.
6. Proceed through C1, C2, and B requests to make a booking for the Simple Flow (or C1, C2, P, accept, B for the Approval Flow).

{% hint style="warning" %}
**Ensure that you have pulled the latest updates for your collection**

****![](<../../.gitbook/assets/Screenshot 2022-06-09 at 11.53.49.png>)****
{% endhint %}

### Postman and your implementation

{% hint style="info" %}
Note that there is an Oauth2 template setup in the authorisation tab of the collection, that you may need to adjust to work when building your implementation. Here is the [Postman documentation on authorizing requests](https://learning.postman.com/docs/sending-requests/authorization/).
{% endhint %}

Throughout the guide, you will see prompts to run the Postman against your implementation. As a guide, here are those stages:

1. Create a new Environment in Postman with values relevant to your implementation
2. Setup a [`dev.json` file](https://developer.openactive.io/open-booking-api/test-suite#step-2-create-a-local-configuration-file) to point the test suite to the same environment
3. Run the broker-microservice from within the OpenActive Test Suite using the configuration data in `dev.json`:\
   a. `export NODE_ENV=dev`\
   b. `npm run start-broker`
4. Click one of the "Sample" requests on the left and "Run" the request.
5. Proceed through C1, C2, and B requests to make a booking for the Simple Flow (or C1, C2, P, accept, B for the Approval Flow).
