# Opening Hours

Please also refer to the reference documentation for the `OpeningHoursSpecification` type.

{% hint style="warning" %}
Implementation of the [proposal for OpeningHoursSpecification](https://github.com/openactive/modelling-opportunity-data/issues/258) within the tooling has been accelerated in response to the COVID-19 pandemic. The proposal follows [Google's documented usage](https://developers.google.com/search/docs/data-types/local-business#business\_hours), and so is unlikely to change significantly, however we welcome your contribution to the [proposal](https://github.com/openactive/modelling-opportunity-data/issues/258) with any thoughts and feedback from your implementation.
{% endhint %}

## Standard opening hours

The standard opening hours of a `Place` may be described using the `openingHoursSpecification` property as documented below. Note this property **must not** be used to define exceptional hours, such as specific public holidays, closures due to bad weather, or a pandemic-related lockdown.

### Open all year

Excluding the `validFrom` and `validThrough` properties signifies that the hours are valid year-round. This example shows a business open every day from 6:30am until 21:30pm:

```javascript
"openingHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Monday",
      "https://schema.org/Tuesday",
      "https://schema.org/Wednesday",
      "https://schema.org/Thursday",
      "https://schema.org/Friday",
      "https://schema.org/Saturday",
      "https://schema.org/Sunday"
    ],
    "opens": "06:30",
    "closes": "21:30"
  }
]
```

### Public holidays

The `dayOfWeek` value of `https://schema.org/PublicHolidays` is defined as a placeholder for all official public holidays in the `location` in which the `Place` is located. It can be used to indicate opening hours on public holidays, overriding general opening hours for the day of the week on which a public holiday occurs:

```javascript
"openingHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Monday",
      "https://schema.org/Tuesday",
      "https://schema.org/Wednesday",
      "https://schema.org/Thursday",
      "https://schema.org/Friday"
    ],
    "opens": "06:30",
    "closes": "21:30"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Saturday"
    ],
    "opens": "07:15",
    "closes": "17:30"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Sunday",
      "https://schema.org/PublicHolidays"
    ],
    "opens": "09:00",
    "closes": "17:30"
  }
]
```

### Open seasonally

For Places that are open only seasonally (for example an Ice Rink), both the `validFrom` and `validThrough` may be used. This example shows a business open only during weekends during the winter holidays:

```javascript
"openingHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Saturday"
    ],
    "opens": "09:30",
    "closes": "20:30",
    "validFrom": "2015-12-23",
    "validThrough": "2016-01-05"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Sunday"
    ],
    "opens": "09:30",
    "closes": "17:30",
    "validFrom": "2015-12-23",
    "validThrough": "2016-01-05"
  }
]
```

### All-day open / closed

To show a business as open 24 hours a day, set the `open` property to `"00:00"` and the `closes` property to `"23:59"`.

To show a business is closed all day, set both `opens` and `closes` properties to `"00:00"`.

This example shows a business open all day Saturday and closed all day Sunday:

```javascript
"openingHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Saturday"
    ],
    "opens": "00:00",
    "closes": "23:59"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Sunday"
    ],
    "opens": "00:00",
    "closes": "00:00"
  }
]
```

### Late night hours

For hours past midnight, define opening and closing hours using a single `OpeningHoursSpecification` property. This example defines hours from Saturday at 6pm until Sunday at 3am:

```javascript
"openingHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Saturday"
    ],
    "opens": "18:00",
    "closes": "03:00"
  }
]
```

### Multiple opening hours

For days with multiple hours, these must be provided as separate instances of `OpeningHoursSpecification`.

This example defines the following hours:

```javascript
"openingHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Monday",
      "https://schema.org/Tuesday",
      "https://schema.org/Wednesday",
      "https://schema.org/Thursday"
    ],
    "opens": "06:00",
    "closes": "13:00"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Monday",
      "https://schema.org/Tuesday",
      "https://schema.org/Wednesday",
      "https://schema.org/Thursday"
    ],
    "opens": "16:00",
    "closes": "20:00"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Friday"
    ],
    "opens": "06:00",
    "closes": "12:00"
  },
  {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": [
      "https://schema.org/Saturday",
      "https://schema.org/Sunday",
      "https://schema.org/PublicHolidays"
    ],
    "opens": "08:00",
    "closes": "12:00"
  }
]
```

## Special opening hours

The `specialOpeningHoursSpecification` property of the `Place` is used to explicitly override standard opening hours brought in scope by `openingHoursSpecification`. Note this property **must not** be used to define standard hours, and should instead describe exceptions such as specific public holidays, closures due to bad weather, or a pandemic-related lockdown.

### Christmas and New Year

Use `specialOpeningHoursSpecification` combined with `validFrom` and `validThrough` properties to specify opening hours for specific public holidays. This example shows a business closed on Christmas Day:

```javascript
"specialOpeningHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "opens": "00:00",
    "closes": "00:00",
    "validFrom": "2015-12-25",
    "validThrough": "2015-12-25"
  }
]
```

### COVID-19 Lockdown

Use `specialOpeningHoursSpecification` combined with `validFrom` and `validThrough` properties to specify closure during specific dates. This example shows a business closed completely for all of January:

```javascript
"specialOpeningHoursSpecification": [
  {
    "@type": "OpeningHoursSpecification",
    "opens": "00:00",
    "closes": "00:00",
    "validFrom": "2021-01-01",
    "validThrough": "2021-01-31"
  }
]
```
