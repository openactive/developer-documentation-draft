# Testing with Postman

This Postman workspace, alongside the validator, are the primary tools you will be using to test your implementation as you build it.&#x20;

Postman is an API tool - it enables you to generate test requests to your local implementation and tests whether the response you are providing is correct.&#x20;

??? Install postman instructions ??? Finalise once all postman has been added

[https://www.postman.com/downloads/](https://www.postman.com/downloads/) <- installation

Link to workspace

Postman tutorial, testing against the reference implementation

Workspace configuration

* Creating an environment
* Setting the baseUrl

??? STEPS

* Find out how to add workspace to your postman
* git clone [https://github.com/openactive/openactive-test-suite.git](https://github.com/openactive/openactive-test-suite.git)
* open in your editor
* make a copy of config/default.json, call it dev.json in same folder
* edit datasetSiteUrl to your dataset site URL
* npm install
* NODE\_ENV=dev npm run start-broker
* Should retrieve feeds, wait for it to finish, leave it running in background
* Set your baseUri
* Run sample requests
* Run c1/c2/etc.
