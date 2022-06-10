# Including geo coordinates

## Using geographic coordinates

In order to maximise the usefulness of your data, it is highly recommended that it includes `GeoCoordinates`. Many data users will not use your data unless it includes a **`latitude`** and **`longitude`** within the `geo` property of each **`Place`**.

```javascript
"location": {
  "@type": "Place",
  "url": "https://www.everyoneactive.com/centres/Middlesbrough-Sports-Village",
  "name": "Middlesbrough Sports Village",
  "description": "You can enjoy a huge range of different sports and activities at Middlesbrough Sports Village. There’s a state-of-the-art gym, a full programme of group exercise classes, indoor badminton sessions and a soft play area for kids. Outside, the Village boasts a full range of athletics facilities, including an eight-lane 400m track, a 10-lane 100m track, as well as four long jump pits. There’s also high jump, hammer throwing, javelin and pole vaulting facilities. The latest addition to the facility is a £1.6m, 250m velodrome, alongside five all-weather outdoor pitches and a skate park for BMX bikes, scooters and inline skaters.",
  "telephone": "01642 728555",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "Alan Peacock Way",
    "addressLocality": "Village East",
    "addressRegion": "Middlesbrough",
    "postalCode": "TS4 3AE",
    "addressCountry": "GB"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": 54.543964,
    "longitude": -1.20978500000001
  }
}
```

## Licensing and republishing

In order to publish geographic coordinates in your open data feed, you must ensure that the source of these coordinates (usually your geocoder) permits you to republish this data under an open licence.

The [OpenCage Geocoder](https://opencagedata.com/) is an example of a geocoder that [permits such republishing](https://opencagedata.com/why-use-open-data).

The basic [Google Geocoding API](https://developers.google.com/maps/documentation/geocoding/policies) licence **does not** permit open data republishing, and does not permit including the data in any externally facing API.

## Rendering a geocoding user interface <a href="#rendering-the-openactive-activity-list-with-skos-js" id="rendering-the-openactive-activity-list-with-skos-js"></a>

Given the inaccuracy of geocoding for sports venues, we recommend using [leaflet.js](https://leafletjs.com/) (or a similar service) to implement a map to allow your users to move a pin to a specific location.

### Leaflet.js and OpenCage implementation example <a href="#activity-list-dropdown-implementation-example" id="activity-list-dropdown-implementation-example"></a>

Below is a copy-and-pastable example of a drag-and-drop map pin and geocoder, that can be used within your booking system to allow the user to enter reliable latitude and longitude data.‌

It is powered by [OpenStreetMap](https://www.openstreetmap.org/), [open data compatible geocoding](https://opencagedata.com/), and [open source libraries](https://leafletjs.com/).

On load you may specify the **`map.centerMap(lat, lng)`** to set the initial position of the map.‌

On pressing the geocode button, the geocoder provides both **`latitude`** and **`longitude`**, which can then be stored in your database and later used within your open data feed(s).‌ The map pin can also be used to adjust these values.

See the "**Result**" tab below for a live demo.

{% embed url="https://jsfiddle.net/nickevansuk/d1gn0h87/" %}

### Gladstone: Implementation example

An example of the map pin interface within the Gladstone leisure management system is shown below.
