# @context and JSON-LD

The page explains the various "contexts" used within the OpenActive data model\
to standardise the data structure.

## Contexts, Properties and Types

A **context** provides globally unique identifiers for standard **properties** and **types** used to describe data.

For example, the OpenActive **context,** which can be referenced using the globally unique identifier "`https://openactive.io/`", defines the **property** "`startDate`" of the **type** "`Event`". Additionally every instance of a **type** optionally has a unique **"`@id`"** in the form of a URL. In JSON an instance of a type is represented as a JSON object.

{% hint style="info" %}
**`@id`** properties are used as identifiers within the OpenActive models. For compatibility with [JSON-LD](https://www.w3.org/TR/json-ld/), the value of such a property **must** always be an absolute URI that provides a stable globally unique identifier.

The primary purpose of the URI format in this context is to provide natural namespacing for the identifier. Hence, the URI itself **may** not resolve to a valid endpoint (unless otherwise specified in this documentation), but **must** be globally unique and use a domain name controlled by the organization or system publishing the data.

If the URI resolves to a valid endpoint, that endpoint **should** provide a JSON-LD representation of the object.
{% endhint %}

When using **types** and **properties** from a particular context, that context must be referenced using "`@context`" in the top-level JSON object as follows:

```javascript
{
  "@context": [ "https://openactive.io/" ],
  "@type": "Event",
  "@id": "https://example.com/events/3",
  "startDate": "startDate",
  "ageRange": {
    "@type": "QuantitativeValue",
    "minValue": 16
  }
}
```

The OpenActive context "[https://openactive.io/](https://openactive.io/)" brings together several standards, including [schema.org](https://schema.org) and [SKOS](http://www.w3.org/2004/02/skos/core) define some standard properties, which we can use to describe our data.

There are four primary contexts which are used within OpenActive data. These are listed below. Clicking on the name of each context will take you to a list of properties within it. Additionally data publishers can define their own properties and publish their own context, which is known as an extension context.

OpenActive data is valid JSON-LD, which means that properties published in OpenActive data will always exist in one of the specified contexts included in`"@context"`).

| Context                                              | Prefix | URL                                                                         | Bundles          | Prefix Required | Status          |
| ---------------------------------------------------- | ------ | --------------------------------------------------------------------------- | ---------------- | --------------- | --------------- |
| [schema.org](http://schema.org/)                     | schema | [https://schema.org/](https://schema.org/)                                  |                  | No              | Living Standard |
| [SKOS](http://www.w3.org/2004/02/skos/core)          | skos   | [http://www.w3.org/2004/02/skos/core#](http://www.w3.org/2004/02/skos/core) |                  | No              | Stable          |
| [OpenActive](https://www.openactive.io/ns)           | oa     | [https://openactive.io/](https://openactive.io/)                            | schema.org, SKOS | No              | Stable          |
| [OpenActive Beta](https://www.openactive.io/ns-beta) | beta   | [https://openactive.io/ns-beta](https://openactive.io/ns-beta)              |                  | Yes             | Experimental    |
| \[[Extensions](broken-reference)]                    | \*     | \*                                                                          |                  | Yes             | Experimental    |

The [OpenActive modelling specification](https://www.openactive.io/modelling-opportunity-data/) includes a "profile" (subset) of schema.org and SKOS which has been defined for OpenActive use, along with the specification for additional OpenActive-specific properties.

### Understanding which contexts are in use

When reading a response which contains JSON-LD, you'll see an "`@context`" property to let you know which contexts are in use.

Three key things to remember:

* The OpenActive context bundles SKOS and schema.org contexts inside it too.
  *   So writing

      `"@context": [ "https://openactive.io/" ]`

      is the same as writing

      `"@context": [ "https://openactive.io/", "https://schema.org", "http://www.w3.org/2004/02/skos/core#" ]`
* Using the OpenActive context means that properties defined in OpenActive, SKOS and schema.org do not require a prefix when using the properties defined within them.
  * So you **must** write `"name": "Tai chi Class"` instead of `"schema:name": "Tai chi Class"`
* All other properties always require a prefix.
  * You **must** write `"beta:attendeeCount"` and not `"attendeeCount"`

#### Example

The following example references the contexts OpenActive (which automatically includes schema.org and SKOS) and OpenActive Beta.

```javascript
{
  "@context": [
    "https://openactive.io/",
    "https://openactive.co/ns-beta"
  ],
  "@type": "SessionSeries",
  "name": "Tai chi Class",
  "url": "http://www.example.org/events/1",
  "beta:attendeeCount": 4
}
```

## Context Evolution

#### schema.org

[Schema.org](https://schema.org/) is a collaborative, community activity with a mission to create, maintain, and promote schemas for structured data on the Internet, on web pages, in email messages, and beyond. It was founded by Google, Microsoft, Yahoo and Yandex, Schema.org vocabularies.

The terms of schema.org continue to evolve through lively discussions on their [mailing list](https://www.w3.org/community/schemaorg/) and [GitHub issues](https://github.com/schemaorg/schemaorg/issues). Often properties are added for some specific use case, and their potential relationship to other areas of schema.org only becomes clear later. This gives rise to changes in textual definition and property-to-type associations that gradually make schema.org more coherent, without introducing radical changes in meaning. Consumers of schema.org data can generally rely on schema.org term meanings not changing dramatically; however term definitions often evolve gradually over time, to accommodate new usage scenarios or to improve usability. When schema.org properties are occasionally deprecated, they stay in the context so that they can still be used and are simply referenced to their replacement property.

The OpenActive W3C Community Group contributes to discussions within schema.org, however consensus involves a number of stakeholders and can be time consuming to reach.

Where a schema.org property is explicitly included in the [OpenActive modelling specification](https://www.openactive.io/modelling-opportunity-data/), it follows the OpenActive versioning policy. Where a schema.org property is not included in the OpenActive modelling specification, its usage may be subject to change, however due to schema.org's internal processes and policies it is highly unlikely to change in name, and more likely to subtly change in definition.

### SKOS: Simple Knowledge Organization System

The [SKOS specification](https://www.w3.org/TR/skos-reference), published in 2009, is centred around the `Concept` type, and provides a mechanism for organising these concepts into a hierarchy. It is used for the OpenActive activity list and other controlled vocabularies where an enumeration of specific terms are defined.

This specification is not expected to be updated, however the use of the specification within OpenActive is subject to change in line with the OpenActive versioning policy.

### OpenActive

The [OpenActive W3C Community Group](http://www.w3.org/community/openactive/) was established with the objective of facilitating the sharing and use of physical activity data. We very much encourage you to join the conversation and help shape the standards through this process.

The [OpenActive modelling specification](https://www.openactive.io/modelling-opportunity-data/) defines a subset ("profile") of schema.org and SKOS which has been defined for OpenActive use, along with additional OpenActive-specific properties that feature in the OpenActive context.

The [OpenActive versioning policy](https://www.openactive.io/modelling-opportunity-data/#versioning-policy) within that specification specifies the use of version numbering to indicate potential for breaking changes. Minor versions, e.g. 1.1, 2.1, etc should be backwards compatible. Major versions, e.g. 2.0, 3.0 are likely to include breaking changes. It is estimated that such breaking major version releases will only occur at most annually.

### OpenActive Beta

The [OpenActive Beta](https://www.openactive.io/ns-beta/) context provides a custom context that can be used by publishers experimenting with new properties that are likely to be added to the core specification. It is defined as a convenience to help document properties that are in active testing and review by the community. Data consumers should not assume that properties in the beta context will either be added to the core specification or be included in the OpenActive context over the long term.

## Extensions

OpenActive data can contain additional publisher-specific contexts, known as extensions. Data publishers may use these extensions to include additional fields which are not currently being considered as part of the evolving OpenActive standards.

#### Example

In the example below the `"britishcycling:gpxFile"` field is defined by an extension provided by British Cycling, using their custom context `"http://data.goskyride.com/opendata/britishcycling.jsonld"`.

```json
{
  "@context": [
    "https://openactive.io/",
    "https://data.letsride.co.uk/opendata/britishcycling.jsonld"
  ],
  "@type": "Event",
  "name": "Wheel Do It! - Blindside loop (with Betty-Ann)",
  ...
  "britishcycling:gpxFile": "https://lr-media.staging.phoenixdigital.agency/download/2c89c364a0738a26fde9b68eb35bfeb0",
  "meetingPoint": "Start of Loxley Road just past the bus stop, which is immediately after the pedestrian crossing/traffic lights."
}
```
