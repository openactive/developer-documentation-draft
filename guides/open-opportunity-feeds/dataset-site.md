---
description: How to create your dataset site
---

# Publishing your feeds

Your feeds should now be ready to publish, and you may have already made them available in production. There are just two steps left, both will help people find and consume your feeds:

* Creating a dataset site
* Listing your feed in the OpenActive catalog

## Dataset Sites

Dataset Sites help data users to find your data and interact with you if they find any issues. They are both human and machine readable, and allow your data to appear everywhere from the [OpenActive Status Page](https://status.openactive.io/) to the [Google Dataset Search](https://toolbox.google.com/datasetsearch/search?query=openactive). Some example dataset sites: [GLL](http://data.better.org.uk/), [Fusion Lifestyle](https://opendata.fusion-lifestyle.com/OpenActive)​

In order for users to find your data feeds, and for your dataset to be featured on the [OpenActive status page](https://status.openactive.io/), you must deploy the [Dataset Site Template](https://developer.openactive.io/publishing-data/dataset-sites#step-1-build-dataset-sites-into-your-system) (a simple mustache template) within your codebase.

#### Single database systems

For booking systems or bespoke websites with a single database and one set of OpenActive data feeds, a single Dataset Site is likely to be sufficient for your organisation. This can be achieved by simply hard-coding the JSON passed into the mustache template.

**Multiple database systems**

If you are a booking system with multiple databases, each of which has their own set of OpenActive data feeds, a Dataset Site is required for each customer. This can be achieved using customer configuration to drive the mustache template. If you are publishing multiple dataset sites you also need to provide a [Data Catalog](https://developer.openactive.io/publishing-data/dataset-sites#step-5-providing-a-data-catalog-multiple-databases-only).

You need to create a [GitHub issues board](https://developer.openactive.io/publishing-data/dataset-sites#step-2-github-issues-board-creation) for each Dataset Site.

### What is a Dataset Site? <a href="#what-is-a-dataset-site" id="what-is-a-dataset-site"></a>

* A web page that can be referenced when discussing the dataset.
* A human and machine readable licence associated with the data (the Dataset Site contains invisible metadata which allows its details to be read automatically).
* A human and machine readable rights statement to specify how dataset users (innovators who want to build on top of/use your data) should attribute your data.
* An accessible "single point of truth" that explains where the data can be found.
* Links to documentation relating to the format of the data, including the specifications it follows, and the data fields it contains.
* A place where the community can contribute with comments, and raise issues - all Dataset Sites are linked to a GitHub issues board (e.g. [this one](https://github.com/gll-better/opendata/issues)) that allows data users to raise issues in the open.

## Step 1: Build Dataset Sites into your system <a href="#step-1-build-dataset-sites-into-your-system" id="step-1-build-dataset-sites-into-your-system"></a>

The Dataset Site Template is very easy to use and quick to apply - it's essentially a single mustache template and associated JSON structure. It is designed to work with minimal effort with an extremely [wide range of platforms and languages](https://mustache.github.io/).The[ dataset site template repository](https://github.com/openactive/dataset-site-template) contains a [mustache template](https://openactive.io/dataset-site-template/datasetsite.mustache) for creating an OpenActive dataset site.

### Native libraries <a href="#.net-php-ruby-and-javascript-typescript-libraries" id=".net-php-ruby-and-javascript-typescript-libraries"></a>

Several libraries are available that make it easy to render the dataset site template, accepting basic settings to configure your dataset site automatically.

* .NET ​​[OpenActive.DatasetSite.NET](https://www.nuget.org/packages/OpenActive.DatasetSite.NET/)
* ​PHP​ [openactive/models](https://packagist.org/packages/openactive/models)​​[openactive/dataset-site](https://packagist.org/packages/openactive/dataset-site)
* ​Ruby​[openactive](https://rubygems.org/gems/openactive)​​[openactive-dataset\_site](https://rubygems.org/gems/openactive-dataset\_site)
* ​JavaScript / TypeScript ​[@openactive/models-ts](https://www.npmjs.com/package/@openactive/models-ts)​

### Other Languages <a href="#other-languages" id="other-languages"></a>

The [Dataset Site Template](https://github.com/openactive/dataset-site-template/) is a single self-contained mustache template of an HTML page that contains embedded CSS, an embedded encoded image, and references to CDNs of [Font Awesome](https://fontawesome.com/) and [Google Fonts](https://fonts.google.com/). It works across all browsers, and includes fully compliant DCAT and schema.org machine-readable metadata to ensure it is compatible with [Google Dataset Search](https://toolbox.google.com/datasetsearch/search?query=openactive).Steps to render the template:

1. Construct the JSON-LD to match the format found in [this example](https://validator.openactive.io/?url=https%3A%2F%2Fwww.openactive.io%2Fdataset-site-template%2Fexample.json\&version=2.x\&validationMode=DatasetSite), following [this documentation](https://developer.openactive.io/data-model/types/dataset).
2. Find a [mustache library](https://mustache.github.io/) for your platform or language.
3. Write code to do the following:
   * Stringify the input JSON, and place the contents of the string within the "json" property at the root of the JSON itself (i.e. serialised JSON embedded in the original deserialised object). This is important as it is used to populate the machine-readable `<script type="application/ld+json">` tag within the generated HTML - view the source of [this page](https://reference-implementation.openactive.io/OpenActive) to see an example.
   * Use the resulting JSON with the [mustache template](https://openactive.io/dataset-site-template/datasetsite.mustache) to render the dataset site.
   * Keep in mind that OpenActive will be providing updates to the mustache template in the future, so it is best to write code that anticipates this.

#### JavaScript Prototype <a href="#javascript-prototype" id="javascript-prototype"></a>

This [JSFiddle](https://jsfiddle.net/nickevansuk/msby0vqg/) demonstrates the Dataset Site Template render steps outlined above using plain JavaScript. Please note this is only an example to demonstrate the logic and is not intended for production use. The mustache template must be copied locally and rendered server-side for production use, for security (to prevent XSS attacks) and as one of its primary purposes is SEO.

## Step 2: Personalising the Dataset Site <a href="#step-2-personalising-the-dataset-site" id="step-2-personalising-the-dataset-site"></a>

The Dataset Site Template is designed to carry the customer's brand with minimal configuration.

#### Single database <a href="#single-database" id="single-database"></a>

For booking systems or bespoke websites with a single database and one set of OpenActive data feeds, a single Dataset Site is likely to be sufficient for your organisation. This can be achieved by simply hard-coding the JSON passed into the mustache template (see [documentation](https://developer.openactive.io/data-model/types/dataset) and [example](https://validator.openactive.io/?url=https%3A%2F%2Fopenactive.io%2Fdataset-site-template%2Fexample.json\&version=2.x\&validationMode=DatasetSite)), or hard-coding the settings passed to the library (see the [relevant library documentation](https://developer.openactive.io/publishing-data/dataset-sites#net-php-and-ruby-libraries)).

Note a single Dataset Site must only be used when all feeds it includes are part of the same dataset - for example a SessionSeries feed and ScheduledSession feed that together constitute the dataset of all providers in the booking system. Where multiple feeds exist that represent distinct datasets (e.g. SessionSeries feed for Provider A, SessionSeries feed for Provider B), they must be referenced from distinct Dataset Sites, which can be constructed as per the instructions in [Multiple databases](https://developer.openactive.io/publishing-data/dataset-sites#multiple-databases) below.

#### Multiple databases <a href="#multiple-databases" id="multiple-databases"></a>

For large booking systems with multiple databases, usually a separate database for each customer, a separate Dataset Site may be created for each database. The list below illustrates the minimal number of configurable properties that can be used to generate the whole dataset site in a way that is personalised to each customer. See the example [here](https://github.com/openactive/OpenActive.Server.NET/tree/master/OpenActive.DatasetSite.NET#model-level-customisation) for how these map into the JSON data structure, for your reference.

* `organisationName` e.g. "Better",
* `datasetSiteUrl` e.g. "[https://halo-odi.legendonlineservices.co.uk/openactive/](https://halo-odi.legendonlineservices.co.uk/openactive/)",
* `datasetDiscussionUrl` e.g. "[https://github.com/gll-better/opendata](https://github.com/gll-better/opendata)",
* `datasetDocumentationUrl` e.g. "[https://docs.acmebooker.example.com/](https://docs.acmebooker.example.com/)",
* `organisationLegalEntity` e.g. "GLL",
* `organisationPlainTextDescription` e.g. "Established in 1993, GLL is the largest UK-based charitable social enterprise delivering leisure, health and community services. Under the consumer facing brand Better, we operate 258 public Sports and Leisure facilities, 88 libraries, 10 children’s centres and 5 adventure playgrounds in partnership with 50 local councils, public agencies and sporting organisations. Better leisure facilities enjoy 46 million visitors a year and have more than 650,000 members.",
* `organisationEmail` e.g. "info@better.org.uk",
* `organisationUrl` e.g. "[https://www.better.org.uk/](https://www.better.org.uk/)",
* `organisationLogoUrl` e.g. "[http://data.better.org.uk/images/logo.png](http://data.better.org.uk/images/logo.png)",
* `backgroundImageUrl` e.g. "[https://data.better.org.uk/images/bg.jpg](https://data.better.org.uk/images/bg.jpg)",
* `openDataFeedBaseUrl` e.g. "[https://halo-odi.legendonlineservices.co.uk/api/](https://halo-odi.legendonlineservices.co.uk/api/)"

We suggest if you can provide the customer with a means of customising the logo and background image (e.g. via uploading an image to the [cloudinary.com](https://cloudinary.com/) CDN, using [their widget](https://jsfiddle.net/nickevansuk/ugpnxmby/), which is free at low volume), these have the largest effect on the brand feel of the page.

Although the customer will likely be able to fill in most properties specific to them, there are two where they will require guidance:

* `datasetDiscussionUrl` - the URL of the [GitHub issues board](https://developer.openactive.io/publishing-data/dataset-sites#step-2-github-issues-board-creation) for the dataset. If your customers are sufficiently large, you will need to create a GitHub issues board for each customer, either [manually](https://developer.openactive.io/publishing-data/dataset-sites#manual-issues-board-creation) or [automatically](https://developer.openactive.io/publishing-data/dataset-sites#automatic-issues-board-creation). See [here](https://github.com/gladstonemrm) for an example of Gladstone's GitHub organization containing a GitHub issues board for each customer.
* `datasetDocumentationUrl` - as a booking system you should provide at least a single page on your website that explains the OpenActive feeds. Each customer may have the option of providing their own documentation for their dataset site that links to this, or just linking to your documentation direct. If you do not have your own documentation page, you can just link to "[https://developer.openactive.io/](https://developer.openactive.io/)".

## Step 3: GitHub Issues Board creation <a href="#step-3-github-issues-board-creation" id="step-3-github-issues-board-creation"></a>

The `discussionUrl` is the url of the GitHub issues board for that specific dataset site.We recommend that you create each GitHub repository (that will include a GitHub Issues Board) within your own GitHub organisation either [manually](https://developer.openactive.io/publishing-data/dataset-sites#manually) or via an [API call](https://developer.openactive.io/publishing-data/dataset-sites#automatically).

If you have multiple databases and customers with large data volumes, you should create one GitHub repository (that will include a GitHub Issues Board) for each customer. Single database systems need only create one GitHub repository (that will include a GitHub Issues Board).

### Helpdesk integration <a href="#helpdesk-integration" id="helpdesk-integration"></a>

If you "follow" these GitHub repositories using a new GitHub account created with your support e-mail address then you will receive notifications for each query, and be able to reply via e-mail to the notifications from your support e-mail address - these replies then appear directly in GitHub. Note that any administrator accounts automatically follow newly created GitHub repositories within your organisation.

### GitHub Organisation Creation <a href="#github-organisation-creation" id="github-organisation-creation"></a>

You must first [create a parent GitHub organisation](https://help.github.com/en/articles/creating-a-new-organization-from-scratch) on the free tier:

* For booking systems we recommend naming the parent GitHub organisation after your own organisation
* For agencies or in-house tech teams we recommend naming parent GitHub organisation after your data publishing organisation.

### Issues Board Creation <a href="#manual-issues-board-creation" id="manual-issues-board-creation"></a>

You can either create an issues board for each dataset site manually by following [these instructions](https://docs.google.com/document/d/1GO4HLYgrwpXH\_qxVuaAWXw\_HIr2Hn4KB7cl973Q1M4M/edit), or use the GitHub API [automatically create GitHub repositories](https://developer.github.com/v3/repos/#create). The recommended properties for a new repository are included below:

```
{
  "name": "AshfordLeisureTrust",
  "description": "Issues relating to open data from Ashford Leisure Trust",
  "homepage": "https://ashfordleisuretrust.leisurecloud.net/OpenActive/",
  "private": false,
  "has_issues": true,
  "has_projects": false,
  "has_wiki": false,
  "auto_init": false
}
```

## Step 4: Validating your Dataset Site <a href="#step-4-validating-your-dataset-site" id="step-4-validating-your-dataset-site"></a>

Use the [validator](https://validator.openactive.io/?url=https%3A%2F%2Fwww.openactive.io%2Fdataset-site-template%2Fexample.json\&version=2.x\&validationMode=DatasetSite) to check that the JSON-LD within your Dataset Site is conformant, by using the **Load URL** feature in the menu to load your **Dataset Site URL**, while in the "Dataset Sites" mode. The validator will automatically extract the JSON-LD from your Dataset Site's HTML and validate it.

## Step 5: Providing a Data Catalog (multiple databases only) <a href="#step-5-providing-a-data-catalog-multiple-databases-only" id="step-5-providing-a-data-catalog-multiple-databases-only"></a>

For booking systems with **multiple databases**, a Data Catalog must also be provided to allow the many Dataset Sites that are created to be easily indexed by the [OpenActive Status Page](https://status.openactive.io/) and other data users.A Data Catalog is very simply an array of the URLs of all your Dataset Sites (the `dataset` array), presented within a `DataCatalog` wrapper following a [specific format](https://developer.openactive.io/data-model/types/datacatalog). An example of a live Data Catalog from the Gladstone system can be found [here](https://opendata.leisurecloud.live/api/datacatalog), and another example [here](https://validator.openactive.io/?url=https%3A%2F%2Fopenactive.io%2Fdata-catalogs%2Fsingular.jsonld\&version=2.x\&validationMode=DataCatalog).

Please use the [validator](https://validator.openactive.io/?url=https%3A%2F%2Fopenactive.io%2Fdata-catalogs%2Fsingular.jsonld\&version=2.x\&validationMode=DataCatalog) to check that your `DataCatalog` is conformant, using the "Data Catalog" mode.

## Step 6: Adding your Dataset Site or Data Catalog to the OpenActive Data Catalog Collection <a href="#step-6-adding-your-dataset-site-or-data-catalog-to-the-openactive-data-catalog-collection" id="step-6-adding-your-dataset-site-or-data-catalog-to-the-openactive-data-catalog-collection"></a>

OpenActive Data Catalogs provide a mechanism for registering OpenActive Datasite Sites so that they can be [discovered and harvested](https://www.openactive.io/data-catalogs/) by data users.

#### Single database <a href="#single-database-1" id="single-database-1"></a>

If you have created a new Dataset Site, simply create a [Pull Request for the OpenActive Data Catalog for Singular Datasets](https://github.com/openactive/data-catalogs/edit/master/singular.jsonld) and add your Dataset Site's production URL to the `dataset` array.

The pull request will trigger GitHub Actions to run the OpenActive Test Suite to validate the live feeds within dataset. OpenActive Test Suite validation must pass before the PR can be merged.

To force the validation to re-run, please submit an empty commit to the PR:

```
git commit --allow-empty -m "trigger GitHub actions"
git push
```

The OpenActive Test Suite can be used to run the same test locally.

#### Multiple databases <a href="#multiple-databases-1" id="multiple-databases-1"></a>

If you have created a new Data Catalog that links to your Dataset Sites, simply create a [Pull Request for the OpenActive Data Catalog Collection](https://github.com/openactive/data-catalogs/edit/master/data-catalog-collection.jsonld) and add your Data Catalog's production URL to the `hasPart` array.



