---
title: API Reference

language_tabs:

toc_footers:

includes:

search: true
---

# Introduction

<aside class="notice">
  This public API is in beta and endpoints are subject to change.
</aside>

Welcome to Bookinglayer's Public API documentation.

If you have any questions or requests please send them through to [tech@bookinglayer.com](mailto:tech@bookinglayer.com).

# Authentication

Each consumer of the Public API uses a unique key that is passed in the header of each request in the following format:

`Authorization: Bearer {api_key}`

The API key not only provides access to the Public API but also associates all actions with a 'user' associated with that key. It is possible for a business to have multiple API keys if there is a need for actions from multiple API consumers to be individually identified within the business' account.

[Contact us](mailto:tech@bookinglayer.com) if you need to be issued an API key.

# Request Structure

`https://api2.bookinglayer.io/api/pub/v2/` is the root url for all requests.

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
    "backoffice_category": {
      "id": 1,
      "title": "Surf packages"
    },
    "frontoffice_category": {
      "id": 12,
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

`GET https://api2.bookinglayer.io/api/pub/v2/products`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code.
location | no | `12` | Filter results in `products` to a particular location
type | no | `activity` | Filter results in `products` to a particular product type. Product types options are: `package`, `accommodation`, `activity`, `rental`, `service`, `bundle` and `item`.

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
  "backoffice_category": {
    "id": 1,
    "title": "Surf packages"
  },
  "frontoffice_category": {
    "id": 12,
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

`GET https://api2.bookinglayer.io/api/pub/v2/products/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code.

# Packages

## Introduction

Two important endpoints for fetching information about packages are:

* `/packages/{id}` - returns package items and information about the way in which a package can be configured
* `/packages/{id}/accommodations` - returns information about accommodations available to be booked as part of the package.

## Package Items

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

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/packages/{id}`

Where `{id}` is the id of the package.

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
start_date | yes | The start date for the response in `YYYY-MM-DD` format.
duration | yes | Duration of the enquiry in nights.
currency | no | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | If translation is available, returns translated fields such as "title" & "description" according to language code.

> detailed overview of response structure

```json
{
  "start_date": "2019-09-01",
  "end_date": "2019-09-04",

  "single_items": [
    {
      "package_item_id": 27299,
      "role": "option",
      "is_configurable_dates": true,
      "product": {
        "id": 3337,
        "type": "activity",
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
          "from_price": true      <-- "this is true if qty prices are set for product (i.e. if there is a discount for multiple items)"
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
          "package_item_id": 27299,
          "role": "default",
          "is_configurable_dates": true,
          "product": {
            "id": 3337,
            "type": "activity",
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
          "package_item_id": 27299,
          "role": "alternative",
          "is_configurable_dates": true,
          "product": {
            "id": 3337,
            "type": "activity",
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

## Accommodation

```json
[
  {
    "start_date": "2019-08-01",
    "end_date": "2019-08-08",
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
      "backoffice_category": {
        "id": 1,
        "title": "Surf packages"
      },
      "frontoffice_category": {
        "id": 12,
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

`GET https://api2.bookinglayer.io/api/pub/v2/packages/{id}/accommodations`

Where `{id}` is the id of the package which contains the accommodation options.

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
start_date | yes | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format.
duration | yes | `3` | Duration of the enquiry in nights.
males | yes | `2` | Number of males making the enquiry
females | yes | `2` | Number of females making the enquiry
couples | yes | `0` | Number of couples making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.

# Availability

## Single Product

> Response for package, accommodation, unscheduled and single date activities, services & rentals:

```json
{
  "2019-05-01": {
    "available_for_checkin": true,
    "available_for_checkout": true,
    "blocked": false,
    "available": 6,
    "capacity": 8,
    "timeslots": null
  },
  "2019-05-02": {
    "available_for_checkin": true,
    "available_for_checkout": false,
    "blocked": true,
    "available": 6,
    "capacity": 8,
    "timeslots": null
  },
  "2019-05-03": {
    "available_for_checkin": true,
    "available_for_checkout": true,
    "blocked": true,
    "available": 6,
    "capacity": 8,
    "timeslots": null
  },
  "2019-05-04": {
    "available_for_checkin": false,
    "available_for_checkout": true,
    "blocked": false,
    "available": 5,
    "capacity": 8,
    "timeslots": null
  }
}
```

> Response for activities on timeslots:

```json
{
  "2019-05-01": {
    "timeslots": [
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
    "timeslots": []
  },
  "2019-05-03": {
    "timeslots": [
      {
        "time": "15:30",
        "available": 10,
        "capacity": 10
      }
    ]
  },
  "2019-05-04": {
    "timeslots": []
  }
}
```

Returns dates and availability information for a specific product.

* If product is a fixed date product then all future dates will be returned; else
* 1 month worth of availability data will be returned from today's date or `start_date` if provided.

Note that the flag for whether the product is on timeslots is returned as part of the `/products` and/or `/products/{id}` response.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/products/{id}/availabilities`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
start_date | no | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format. If not provided then the current date will be taken as the start date.
end_date | no | `2019-03-08` | The end date for the response in `YYYY-MM-DD` format. If not provided then 30 days in advance of `start_date` will be used.

## Multiple Products

> Response for package, accommodation, unscheduled and single date activities, services & rentals:

```json
[
  "3368": {
    "2019-05-01": {
      "available_for_checkin": true,
      "available_for_checkout": true,
      "blocked": false,
      "available": 6,
      "capacity": 8,
      "timeslots": null
    },
    "2019-05-02": {
      "available_for_checkin": true,
      "available_for_checkout": true,
      "blocked": true,
      "available": 6,
      "capacity": 8,
      "timeslots": null
    },
    "2019-05-03": {
      "available_for_checkin": true,
      "available_for_checkout": true,
      "blocked": true,
      "available": 6,
      "capacity": 8,
      "timeslots": null
    },
    "2019-05-04": {
      "available_for_checkin": false,
      "blocked": false,
      "available": 5,
      "capacity": 8,
      "timeslots": null
    },
    { ... },
    { ... },
    { ... }
  },
  "4562": { ... },
  "5283": { ... },
  "9742": { ... },
]

```

Returns dates and availability information for multiple products.

* 1 month worth of availability data will be returned from today's date or `start_date` if provided.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/products/availabilities`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
start_date | no | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format. If not provided then the current date will be taken as the start date.
end_date | no | `2019-03-08` | The end date for the response in `YYYY-MM-DD` format. If not provided then 30 days in advance of `start_date` will be used.
ids[] | no | 3456 | The ids of specific products that you want availability information
type | no | `package` | Fetch availability information for products of this type
backoffice_category_id | no | 764 | Fetch availability information for products of this backoffice category

# Prices

## Single Product

```json
{
  "total_price": 12
}
```

Returns the total price for a product.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/products/{id}/prices`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
start_date | yes | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format.
pax | yes | `2` | Number of guests making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product. If nothing is provided the product's `default_duration` will be used
duration_unit | no | `day` | Duration unit associated with the duration. If nothing is provided the product's `default_duration_unit` will be used

## Multiple Products

> Request

```json
{
  "products": {
    "329": {
      "duration": 7,
      "duration_unit": "day"
    },
    "512": {
      "qty": 6,
      "duration": 4,
      "duration_unit": "hour"
    },
    "145": {
      "duration": 7,
      "duration_unit": "day"
    },
  }
}
```

> Response

```json
{
  "329": 12,
  "512": 456,
  "145": 123
}
```

Returns an object where the keys are product ids for the requested products and values are total prices.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/products/prices`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
start_date | yes | `2019-02-08` | The start date for the response in `YYYY-MM-DD` format.
pax | yes | `2` | Number of guests making the enquiry
product_ids | yes | See below | An array of product ids
duration | yes | `3` | Duration of the enquiry
duration_unit | no | `day` | Duration unit associated with the duration.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.

Note that the array of product ids should be passed in the form:

`&product_ids[]=329&product_ids[]=125&product_ids[]=789`

## Package Prices

> Request

```json
{
  "currency_id": "USD",
  "guests": [
    {
      "unique_id": "guest_1",
      "first_name": "Oliver",
      "last_name": "Nicholson",
      "gender": "M",
      "email": "oliver@gmail.com",
      "language_code": "en",
      "package": {
        "id": 3374,
        "start_date": "2019-01-09",
        "end_date": "2019-01-16",
        "accommodation_id": 3343,
        "items": [
          {
            "package_item_id": 41514,
            "dates": ["2019-01-11", "2019-01-13"]
          },
          {
            "package_item_id": 41516,
            "dates": ["2019-01-12"]
          },
          {
            "package_item_id": 41520,
            "dates": ["2019-01-09"]
          }
        ]
      }
    }
  ]
}
```

> Response

```json
{
  "guest_1": [
    {
      "package_item_id": 41530,
      "product_id": 3343,
      "price": 3710,
      "is_fixed": false,
      "guest_to_charge": [
        "guest_1"
      ]
    },
    {
      "package_item_id": 41514,
      "product_id": 3354,
      "price": 220,
      "is_fixed": false,
      "guest_to_charge": [
        "guest_1"
      ]
    },
    {
      "package_item_id": 41516,
      "product_id": 3355,
      "price": 150,
      "is_fixed": false,
      "guest_to_charge": [
        "guest_1"
      ]
    },
    {
      "package_item_id": 41520,
      "product_id": 3357,
      "price": 520,
      "is_fixed": false,
      "guest_to_charge": [
        "guest_1"
      ]
    }
  ]
}
```

To avoid clientside calculation of prices, this endpoint returns an object breaking down the price of each package item that has been selected. Each guest is identified in the request by a temporary `unique_id` which is set by the API consumer. This `unique_id` serves as the key to identifying the guest in the response.

### HTTP Request

`POST https://api2.bookinglayer.io/api/pub/v2/packages/prices`

### Arguments

The request payload is the same as when <a href="#adding-items-to-the-cart">adding items to the cart</a> however each guest object must contain a `unique_id`

# Seasons

```json
[
  {
    "id": 80,
    "title": "High",
    "segments": [
      {
        "id": 138,
        "start_date": "2019-01-01",
        "end_date": "2019-04-29"
      },
      {
        "id": 139,
        "start_date": "2019-08-01",
        "end_date": "2019-12-31"
      }
    ]
  },
  {
    "id": 81,
    "title": "Low",
    "segments": [
      {
        "id": 137,
        "start_date": "2019-05-30",
        "end_date": "2019-07-31"
      }
    ]
  }
]
```

Returns array of seasons and season segments for a business.

Minor clarification: season segment `start_date` and `end_date` are inclusive dates. This means, for example, that if a season segment has a `start_date` of `2019-02-01` and an `end_date` of `2019-02-10`, that both `2019-02-01` and `2019-02-10` are considered part of that season segment.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/seasons`

# Settings

## Fetch business level settings


```json
{
  "id": 176,
  "title": "LUEX Demo",
  "business_domain": "luex.bookinglayer.io",
  "company_name": "LUEX ",
  "website_url": null,
  "currencies": [
    {
      "id": 1,
      "title": "Euro",
      "symbol": "€",
      "iso_code": "EUR"
    },
    { ... }
    { ... }
  ],
  "diets": [
    {
      "id": 386,
      "title": "Vegetarian",
      "description": "No meat at all. ",
      "abbreviation": null
    },
    { ... },
    { ... },
  ],
  "levels": [
    {
      "id": 491,
      "title": "Beginner",
      "description": "You have never (or only once or twice) performed this activity in the past and need a supervised environment.",
      "abbreviation": null
    },
    { ... },
    { ... },
  ],
  "languages": [
    "en",
    "de"
  ],
  "settings": {
    "guest_fields": {
      "birthday": {
        "request": true,
        "required": false
      },
      "minimum_age": 0
    },
    "customer_fields": {
      "address": {
        "request": false,
        "required": false
      },
      "phone": {
        "request": true,
        "required": true
      },
      "minimum_age": 0
    }
  },
  "terms": "These are our booking terms and conditions."
}
```

Returns all business level settings.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/settings`

# Deeplinking

## Deeplink URL to Product

```json
{
  "url": "https://yourbusiness.bookinglayer.io/frontoffice/product/384?start='2019-03-02'&duration=4&male=2"
}
```

Returns the URL required to link directly to the product in Bookinglayer's frontoffice booking form, including all start date, end date, duration and pax settings.

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/products/{id}/deeplink`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
product_id | yes | `384` | The product id.
start_date | no | `2019-02-08` | The start date in `YYYY-MM-DD` format.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product.
end_date | no | `2019-02-20` | The end date in `YYYY-MM-DD` format.
males | no | `2` | Number of males.
females | no | `3` | Number of females.
couples | no | `1` | Number of couples.
language | no | `es` | Language that the frontoffice form should be rendered in.
currency | no | `USD` | Currency that should be selected when frontoffice loads.

# Making a Booking

## Introduction

Making a booking is a minimum two step process:

* __Add items to the cart:__ call `/bookings/add` with information about the package being booked, including selected accommodation, package items and basic info about guests.
  * Can be called multiple times.
  * The first time `/bookings/add` is called a `cart_id` is returned in response.
  * Subsequent calls to `/bookings/add` need to also include `cart_id` to add those items to the same cart as previous calls.
* __Checkout cart:__ call `/carts/{cart_id}/checkout` to finalise the booking, passing along information about the person making the booking and acceptance of terms of booking.

In addition there are endpoints for managing the cart: extend and abandon.

## Adding Items to the Cart

> example request payload for calling `/bookings/add`

```json
{
  "currency": "EUR",
  "cart_id": "6e518664-7325-4f7f-aa20-2880b9cfc305",
  "guests": [
    {
      "first_name": "Oliver",
      "last_name": "Nicholson",
      "email": "ollie@bookinglayer.com",
      "gender": "m",
      "language_code": "en",
      "birth_date": "1984-02-04",
      "package": {
        "id": 384,
        "start_date": "2018-10-01",
        "end_date": "2018-10-08",
        "accommodation_id": 392,
        "items": [
          {
            "package_item_id": 394,
            "dates": [
              "2018-10-01"
            ]
          },
          {
            "package_item_id": 400,
            "dates": [
              "2018-10-02 10:30:00",
              "2018-10-07 15:00:00"
            ]
          },
          {
            "package_item_id": 385,
            "dates": [
              "2018-10-04",
              "2018-10-06",
            ]
          }
        ]
      }
    },
    { ... },
    { ... }
  ]
}
```

Items can be added to a cart as many times as is wanted before checking the cart out to complete order. The first time that `/bookings/add` is called the `cart_id` that is returned in the response should be used in all subsequent calls to associate future bookings with a particular cart.

`default` items are handled automatically by the API so `default` items should not be included when calling `/bookings/add`. Only `option`, `alternative` and `upgrade` items need to be passed with request in `items` array.

### HTTP Request

`POST https://api2.bookinglayer.io/api/pub/v2/bookings/add`

### Arguments

Parameter | Required | Description
--------- | ------- |  -----------
currency | yes | The currency code for the order
cart_id | no | The cart_id which is returned the first time `/bookings/add` is called when making an order
guests | yes | An array of guest objects containing information about the guest and products they are booking

#### Guest Object Arguments

Parameter | Required | Description
--------- | ------- |  -----------
first_name | yes | The first name of the guest
last_name | yes | The lastname of the guest
email | no | The email of the guest (note: must be unique for each guest)
gender | yes | The gender of the guest (`m` or `f`)
language_code | no | The guest's preferred language in two-letter ISO 639-1 format
birth_date | no | The guest's date of birth in `YYYY-MM-DD` format
package | yes | An object detailing the guest's configured package

#### Package Object Arguments

Parameter | Required | Description
--------- | ------- |  -----------
id | yes | The id of the package
start_date | yes | The start date in `YYYY-MM-DD` format
end_date | yes | The start date in `YYYY-MM-DD` format
accommodation_id | yes/no | The id of the selected accommodation (this field is mandatory if the package has accommodation)
items | no | An array of `option`, `alternative` and `upgrade` items that the guest has selected. Default items should not be returned (if they are they will be ignored)

#### Package Items Object Arguments

Parameter | Required | Description
--------- | ------- |  -----------
id | yes | The id of the package item (note that this is not the product id of the package item)
dates | yes | An array of dates that the package item should be applied in `YYYY-MM-DD` or `YYYY-MM-DD HH:mm:ss` format.

## Checking out the cart

> example request payload for calling `/carts/{cart_id}/checkout`

```json
{
  "terms_accepted": true,
  "newsletter_opt_in": false,
  "customer": {
    "first_name": "Oliver",
    "last_name": "Nicholson",
    "email": "ollie@bookinglayer.com",
    "gender": "m",
    "language_code": "en",
    "birth_date": "1984-02-04",
    "phone_countrycode": 61,
    "phone": "403 201 767",
    "address_line_1": "Unit 1",
    "address_line_2": "45 Armstrong Street",
    "city": "Byron Bay",
    "state": "NSW",
    "zipcode": "2481",
    "country": "Australia"
  }
}
```

Completing the order is performed by checking out the cart.

The `cart_id`, customer details and optional newsletter opt-in are all that is required.

### HTTP Request

`POST https://api2.bookinglayer.io/api/pub/v2/carts/{cart_id}/checkout`

### Arguments

Parameter | Required | Description
--------- | ------- |  -----------
terms_accepted | yes | A bool specifying whether the customer agrees to the business' terms and conditions, as provided in [Terms and Conditions](#terms-and-conditions) response. Must be `true` to complete checkout.
newsletter_opt_in | no | A bool specifying whether the customer agrees to receive promotional material from the supplier business
customer | yes | An object of details of the customer; the person making the order

#### Customer Object Arguments

Parameter | Required | Description
--------- | ------- |  -----------
first_name | yes | The first name of the customer
last_name | yes | The lastname of the customer
email | yes | The email of the customer (note: must be unique for each customer)
gender | no | The gender of the customer (`m` or `f`)
language_code | no | The customer's preferred language in two-letter ISO 639-1 format
birth_date | no | The customer's date of birth in `YYYY-MM-DD` format
phone_countrycode | no | A number for the customer's phone country code (e.g. Australia is 61, not "+61")
phone | no | A string of the customer's phone number
address_line_1 | no | The customer's address
address_line_2 | no | The customer's address
city | no | The customer's city/town
state | no | The customer's state
zipcode | no | The customer's zipcode/postcode
country | no | The customer's country _in English_

## Viewing the cart

Returns the contents of the cart

### HTTP Request

`GET https://api2.bookinglayer.io/api/pub/v2/carts/{cart_id}`

## Extend cart

> Response

```json
{
  "cart_id": "6e518664-7325-4f7f-aa20-2880b9cfc305",
  "expiry_date": "2019-09-19T20:05:50+0000"
}
```

Before a cart is checked out there is an expiry time, at which the cart is automatically abandon so that any items which are added to the cart are made available to other customers. However before a cart expires it is possible to extend the cart so that the expiry is delayed by 10 minutes from the time that the request is made.

### HTTP Request

`PUT https://api2.bookinglayer.io/api/pub/v2/carts/{cart_id}/extend`

### Response

Parameter | Description
--------- | -----------
cart_id | The id of the cart
expiry_date | The date and time of expiry of the cart in UTC time

## Abandon cart

> Response

```json
{
  "success": true
}
```

Abandon the cart, making all items that were added to the cart available to other customers.

### HTTP Request

`DELETE https://api2.bookinglayer.io/api/pub/v2/carts/{cart_id}`
