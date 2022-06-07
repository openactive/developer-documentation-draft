# Adding additional information to your feeds

You're Open Opportunity Feed needs to give the same information for booking as your booking system does if you want it to be used correctly. The best approach is consider the information that is currently in your system and how that maps to the different feeds you would like to publish.&#x20;

In order to compare your system with the required, recommended and optional properties of each of the feeds you should use the [references](../../reference/data-model/) that we have provided for each type.

You may also realise as you are implementing the Open Booking API that there are fields that you may wish to include in your feeds, you can find a table of those [open data fields here](../../reference/open-data-fields.md). Below is an example of one that you may wish to use.

### Example - Amenities

You may wish to publish certain amenities that sellers offer via your booking system. This can be used inside a `Place` Open Active type (i.e. `@type`).

The reference documentation shows you a table that outlines what the additional information is used for, in this case for sharing information on amenities such as car park, wifi, changing room or vending machine information.&#x20;

Other columns in the reference documentation with give you information on where the property can be placed, and an example of the object. For Amenities you will see the reference outlines taht the value in the object can be set to "True / false / Null" and the example looks as follows:&#x20;

<details>

<summary>Amenities example</summary>

```
{
  "@context": "https://openactive.io/",
  "@type": "Place",
  "amenityFeature": [
    {
      "name": "Changing Facilities",
      "value": true,
      "@type": "ChangingFacilities"
    },
    {
      "name": "Showers",
      "value": false,
      "@type": "Showers"
    },
    {
      "name": "Toilets",
      "value": false,
      "@type": "Toilets"
    },
    {
      "name": "Lockers",
      "value": true,
      "@type": "Lockers"
    },
    {
      "name": "Towels",
      "value": false,
      "@type": "Towels"
    },
    {
      "name": "Creche",
      "value": false,
      "@type": "Creche"
    },
    {
      "name": "Parking",
      "value": true,
      "@type": "Parking"
    },
    {
      "name": "BabyChanging",
      "value": true,
      "@type": "BabyChanging"
    },
    {
      "name": "Bar",
      "value": true,
      "@type": "beta:Bar"
    },
    {
      "name": "Cafe",
      "value": true,
      "@type": "beta:Cafe"
    },
  ],
}
```

</details>
