---
title: API Reference

language_tabs:

toc_footers:

includes:

search: true
---

# Introduction

Welcome to Bookinglayer's Public API documentation.

If you have any questions or requests please send them through to [ollie@bookinglayer.com](mailto:ollie@bookinglayer.com).

# Request Structure

`https://api2.bookinglayer.io/pub/v2/` is the root url for all requests to the Public API.

At this stage all parameters are passed to endpoints as URL query parameters. The endpoint definitions below stipulate which parameters are required or optional.

All requests require an API identification key, which is also passed to the endpoint as a URL query parameter: `?key=XXXXXXXX`. [Contact us](mailto:tech@bookinglayer.com) if you need to be issued an API key.

<aside class="notice">
TODO: handle authentication with API key in request header
</aside>

# Products

## Get All Products

```json
[
  {
    "id": 384,
    "type": "package",
    "title": "Surf Camp",
    "backoffice_title": "Advanced Surf Camp",
    "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
    "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
    "image": {
      "small": "https://url.to.small.image.jpg",
      "medium": "https://url.to.medium.image.jpg",
      "large": "https://url.to.large.image.jpg",
    },
    "gender": "mf",
    "category": {
      "id": 1,
      "title": "Surf camps"
    },
    "location": {
      "id": 12,
      "title": "Kauai, Hawaii"
    },
    "accommodation_type": null,
    "duration": {
      "is_flexible": true,
      "min": 7,
      "default": 7,
      "unit": "day",
      "variants": [7, 10, 14],
    },
    "pax": {
      "min": 1,
      "max": 10,
    },
    "default_start_time": null,
    "is_private": false,
    "allowed_checkin_days": {
      "mon": true,
      "tue": false,
      "wed": true,
      "thu": true,
      "fri": false,
      "sat": true,
      "sun": false
    },
    "is_on_timeslots": false,
    "price_from": [
      {
        "value": 199,
        "currency": "EUR"
      },
      { ... }
    ]
  },
  { ... },
  { ... },
  { ... }
]
```

Returns general information about categories and all available products. Note that products need to be associated with a frontoffice category or set as promoted in order to be returned by this endpoint:

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/products`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code.
location | no | `12` | Filter results in `products` to a particular location
type | no | `lesson` | Filter results in `products` to a particular product type. Product types options are: `package`, `accommodation`, `lesson`, `rental`, `service`, `bundle` and `item`.

## Get Specific Product

```json
{
  "id": 384,
  "type": "package",
  "title": "Surf Camp",
  "backoffice_title": "Advanced Surf Camp",
  "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
  "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
  "image": {
    "small": "https://url.to.small.image.jpg",
    "medium": "https://url.to.medium.image.jpg",
    "large": "https://url.to.large.image.jpg",
  },
  "gender": "mf",
  "category": {
    "id": 1,
    "title": "Surf camps"
  },
  "location": {
    "id": 12,
    "title": "Kauai, Hawaii"
  },
  "accommodation_type": null,
  "duration": {
    "is_flexible": false,
    "min": 7,
    "default": 7,
    "unit": "day",
    "variants": [7, 10, 14],
  },
  "pax": {
    "min": 1,
    "max": 10,
  },
  "default_start_time": null,
  "is_private": false,
  "allowed_checkin_days": {
    "mon": true,
    "tue": false,
    "wed": true,
    "thu": true,
    "fri": false,
    "sat": true,
    "sun": false
  },
  "is_on_timeslots": false,
  "price_from": [
    {
      "value": 199,
      "currency": "EUR"
    },
    { ... }
  ]
}
```

Returns information about specific product.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/products/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code.

# Packages

## Introduction

> simplified overview of response structure

```json
{
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "single_items": [
    { product object },
    { product object },
    { product object },
    { product object }
  ],
  "groups": [
    {
      "is_configurable_dates": true,
      "items": [
        { product object },
        { product object },
      ]
    },
    { ... },
    { ... }
  ]
}
```

Returns package items and information about the way in which a package can be configured.

There are two ways in which package items can be offered for sale:

* A single item:
  * A single item that is sold without any conditional dependencies between it and any other package item
  * Returned as an array of product objects in `single_items`
* A group of items:
  * A number of items where the select state of one item will affect the select state of the others.
  * Can either be:
      * A `default`/`alternative`/`upgrade` group, where the default item is pre-selected and group behaves like a radio button group; or
      * A group of `option` items where no item is preselected and one or none items may be selected from the group.
  * Returned as an array of `groups`, where each group contains an array of product objects in `items`.

The product object in `single_items` and `groups` are of the same structure.

## Packages Endpoint

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/packages/{id}`

Where `{id}` is the id of the package.

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
key | yes | Your unique API access key.
start_date | yes | The start date for the response in `YYYY-MM-DD` format.
duration | yes | Duration of the enquiry in nights.
pax | yes | Number of people making the enquiry
currency | no | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | If translation is available, returns translated fields such as "title" & "description" according to language code.

> detailed overview of response structure

```json
{
  "start_date": "2019-09-01",
  "end_date": "2019-09-04",

  "single_items": [
    {
      "id": 27299,
      "role": "option",
      "is_configurable_dates": true,
      "product": {
        "id": 3337,
        "type": "lesson",
        "title": "Surf lesson on timeslots",
        "backoffice_title": "Advanced Surf Lesson",
        "description": "Maecenas faucibus mollis interdum.",
        "extended_description": "Aenean lacinia bibendum nulla sed consectetur. Cras mattis consectetur purus sit amet fermentum.",
        "image": {
          "small": "https://url.to.small.image",
          "medium": "https://url.to.medium.image",
          "large": "https://url.to.large.image",
        },
        "is_on_timeslots": true,
        "price": {
          "value": 10,
          "currency": "EUR",
          "type": "person",
          "from_price": true      <-- "this is true if qty or person prices are set for product"
        }
      },
      "dates": [
        {
          "date": "2019-10-01",
          "available": null,    <-- "unlimited || number || null if available is defined in `timeslots`"
          "capacity": null,     <-- "unlimited || number || null if capacity is defined in `timeslots`"
          "timeslots": [        <-- "null || array of available times for the day for timeslot products"
            {
              "time": "10:30",
              "available": 10,
              "capacity": 10,
            },
            {
              "time": "15:00",
              "available": 7,
              "capacity": 10,
            }
          ]
        },
        { ... },
        { ... }
      ],
    },
    { ... },
    { ... },
    { ... }
  ],
  
  "groups": [
    {
      "is_configurable_dates": true,
      "items": [
        {
          "id": 27299,
          "role": "default",
          "is_configurable_dates": true,
          "product": {
            "id": 3337,
            "type": "lesson",
            "title": "Surf Session",
            "backoffice_title": "Late Surf Session",
            "description": "Maecenas faucibus mollis interdum.",
            "extended_description": "Aenean lacinia bibendum nulla sed consectetur. Cras mattis consectetur purus sit amet fermentum.",
            "image": {
              "small": "https://url.to.small.image",
              "medium": "https://url.to.medium.image",
              "large": "https://url.to.large.image",
            },
            "is_on_timeslots": false,
            "price": {
              "value": 10,
              "currency": "EUR",
              "type": "person",
              "from_price": true
            },
          },
          "dates": [
            {
              "date": "2019-10-01",
              "available": 10,
              "capacity": 15,
              "timeslots": null
            },
            { ... },
            { ... }
          ],
        },
        {
          "id": 27299,
          "role": "alternative",
          "is_configurable_dates": true,
          "product": {
            "id": 3337,
            "type": "lesson",
            "title": "Surf Session",
            "backoffice_title": "Late Surf Session",
            "description": "Maecenas faucibus mollis interdum.",
            "extended_description": "Aenean lacinia bibendum nulla sed consectetur. Cras mattis consectetur purus sit amet fermentum.",
            "image": {
              "small": "https://url.to.small.image",
              "medium": "https://url.to.medium.image",
              "large": "https://url.to.large.image",
            },
            "is_on_timeslots": false,
            "price": {
              "value": 10,
              "currency": "EUR",
              "type": "person",
              "from_price": true
            },
          },
          "dates": [
            {
              "date": "2019-10-01",
              "available": "unlimited",
              "capacity": "unlimited",
              "timeslots": null
            },
            { ... },
            { ... }
          ],
        }
      ]
    },
    { ... },
    { ... },
    { ... }
  ]
}
```

### Response Object

#### Root items

Attribute | Description
--------- | -----------
start_date | The start date in `YYYY-MM-DD` format
end_date | The end date in `YYYY-MM-DD` format
single_items | An array of package items that have no relation to other package items
groups | An array containing information regarding grouped package items.

#### Package items

Attribute | Description
--------- | -----------
id | Uniquely identifies the package item
role | `default`, `alternative` or `option` (see more details below)
product | Object of product details
qty | Object with min, max and default quantities for the package item
price | Price object (see below)
is_configurable_dates | Bool which sets whether the available dates in `dates` can be individually configured. If `true` then each date in `dates` can be individually selected/deselected. If `false` then package item should be presented as a checkbox.
dates | Array of dates that the package item is available on. Be sure to reference `configurable_dates` to know whether the package item has the ability to be configured. See below for more details on `dates` structure.

# Accommodation

```json
[  
  {
    "start": "2017-08-01",
    "end": "2017-08-08",
    "available": 2,
    "price": {
      "type": "person",
      "value": 200,
      "currency": "EUR"
    },
    "product": {
      "id": 384,
      "type": "accommodation",
      "title": "Double room",
      "backoffice_title": "Room 13A",
      "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
      "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
      "image": {
        "small": "https://url.to.small.image.jpg",
        "medium": "https://url.to.medium.image.jpg",
        "large": "https://url.to.large.image.jpg",
      },
      "gender": "mf",
      "category": {
        "id": 1,
        "title": "Surf camps"
      },
      "location": {
        "id": 12,
        "title": "Kauai, Hawaii"
      },
      "accommodation_type": {
        "id": 1345,
        "title": "Double Room",
      },
      "pax": {
        "min": 1,
        "max": 2
      },
      "is_private": false,
    }
  },
  { ... },
  { ... },
  { ... }
]
```

Returns an array of accommodation options for a package.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/packages/{id}/accommodations`

Where `{id}` is the id of the package which contains the accommodation options.

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | yes | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format.
duration | yes | `3` | Duration of the enquiry in nights.
males | yes | `2` | Number of males making the enquiry
females | yes | `2` | Number of females making the enquiry
couples | yes | `0` | Number of couples making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.

# Dates & Availability

## Get Date & Availability

> Response for package, accommodation, unscheduled and single date activities, services & rentals:

```json
{
  "2019-05-01": {
    "allowed_for_checkin": true,
    "blocked": false,
    "available": 6,
    "capacity": 8,
    "time_slots": null
  },
  "2019-05-02": {
    "allowed_for_checkin": true,
    "blocked": true,
    "available": 6,
    "capacity": 8,
    "time_slots": null
  },
  "2019-05-03": {
    "allowed_for_checkin": true,
    "blocked": true,
    "available": 6,
    "capacity": 8,
    "time_slots": null
  },
  "2019-05-04": {
    "allowed_for_checkin": false,
    "blocked": false,
    "available": 5,
    "capacity": 8,
    "time_slots": null
  }
}
```

> Response for activities on timeslots:

```json
{
  "2019-05-01": {
    "time_slots": [
      {
        "time": "10:00",
        "available": 10,
        "capacity": 10
      },
      {
        "time": "13:00",
        "available": 5,
        "capacity": 10
      }
    ]
  },
  "2019-05-02": {
    "blocked": true,
    "time_slots": []
  },
  "2019-05-03": {
    "time_slots": [
      {
        "time": "15:30",
        "available": 10,
        "capacity": 10
      }
    ]
  },
  "2019-05-04": {
    "time_slots": []
  }
}
```

Returns dates and availability information for a specific product.

* If product is a fixed date product then all future dates will be returned; else
* 1 month worth of availability data will be returned from today's date or `start_date` if provided.

Note that the flag for whether the product is on timeslots is returned as part of the `/products` and/or `/products/{id}` response.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/products/{id}/availabilities`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | no | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format. If not provided then the current date will be taken as the start date.

# Prices

## Single Product

```json
{
  "total_price": 12
}
```

Returns the total price for a product.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/products/{id}/prices`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | yes | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format.
pax | yes | `2` | Number of guests making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product. If nothing is provided the product's `default_duration` will be used

## Multiple Products

```json
{
  "329": 12,
  "125": 456,
  "789": 123
}
```

Returns an object where the keys are product ids for the requested products and values are total prices.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/products/prices`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | yes | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format.
pax | yes | `2` | Number of guests making the enquiry
product_ids | yes | See below | An array of product ids
duration | yes | `3` | Duration of the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.

Note that the array of product ids should be passed in the form:

`&product_ids[]=329&product_ids[]=125&product_ids[]=789`

# Seasons

```json
[
  {
    "id": 80,
    "title": "High",
    "segments": [
      {
        "id": 138,
        "from": "1900-01-01",
        "to": "1900-04-29",
        "season_id": 80
      },
      {
        "id": 139,
        "from": "1900-08-01",
        "to": "1900-12-31",
        "season_id": 80
      }
    ]
  },
  {
    "id": 81,
    "title": "Low",
    "segments": [
      {
        "id": 137,
        "from_date": "1900-05-30",
        "to_date": "1900-07-31",
        "season_id": 81
      }
    ]
  }
]
```

Returns array of seasons and season segments for a business.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/seasons`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.

# Deeplinking

## Deeplink URL to Product

```json
{
  "url": "https://yourbusiness.bookinglayer.io/frontoffice/product/384?start='2019-03-02'&duration=4&male=2"
}
```

Returns the URL required to link directly to the product in Bookinglayer's frontoffice booking form, including all start date, end date, duration and pax settings.

### HTTP Request

`GET https://api2.bookinglayer.io/pub/v2/products/{id}/deeplink`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
product_id | yes | `384` | The product id.
start_date | no | `2019-02-08` | The start date in `YYYY-MM-DD` format.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product.
end_date | no | `2019-02-20` | The end date in `YYYY-MM-DD` format.
males | no | `2` | Number of males.
females | no | `3` | Number of females.
couples | no | `1` | Number of couples.
language | no | `es` | Language that the frontoffice form should be rendered in.
currency | no | `USD` | Currency that should be selected when frontoffice loads.
