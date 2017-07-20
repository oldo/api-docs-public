---
title: API Reference

language_tabs:

toc_footers:

includes:

search: true
---

# Introduction

Hi there! This is a preliminary version of Bookinglayer's Public API docs which is used to define expected endpoint routes, request payloads and structure of responses.

This is a work in progress at this stage but we will be actively working to ensure that all the important information required to build a customised enquiry form will be available - if there is anything that you'd like to request or any questions that you have, please get in contact at [tech@bookinglayer.com](mailto:tech@bookinglayer.com).

We will also be working to further define the various objects that are returned by the API to give the developer a full understanding on what each field represents.

# Request Structure

`https://api.bookinglayer.io/pub/` is the root url for all requests to the Public API.

At this stage all parameters are passed to endpoints as URL query parameters. The endpoint definitions below stipulate which parameters are required or optional.

All requests require an API identification key, which is also passed to the endpoint as a URL query parameter: `?key=XXXXXXXX`. [Contact us](mailto:tech@bookinglayer.com) if you need to be issued an API key.

# Products

## Get All Products

```json
{
  "categories": {
    "1": {
      "id": 1,
      "title": "Surf Camps",
      "image": "https://url.to.image.jpg",
      "products": [384, 12, 745]
    }
  },
  "locations": {
    "12": {
      "id": 12,
      "title": "Costa Rica",
      "products": [384, 42]
    }
  },
  "products": {
    "384": {
      "id": 384,
      "type": "package",
      "title": "Surf Camp",
      "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
      "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
      "category_id": 1,
      "location_id": 12,
      "capacity": 10,
      "flexible_duration": true,
      "default_duration": 7,
      "duration_unit": "DAY",
      "default_start_time": null,
      "private": false,
      "allowed_checkin_days": "Mon,Tue,Fri,Sat,Sun",
      "gender": "MF",
      "min_pax": 1,
      "max_pax": 12,
      "min_duration": 7,
      "duration_variants": [2, 6, 10],
      "has_timeslots": false,
      "image": {
        "small": "https://url.to.small.image.jpg",
        "medium": "https://url.to.medium.image.jpg",
        "large": "https://url.to.large.image.jpg",
      },
      "price_from": 199
    }
  }
}
```

Returns general information about categories and all available products. Note that products need to be associated with a frontoffice category or set as promoted in order to be returned by this endpoint:

### HTTP Request

`GET https://api.bookinglayer.io/pub/products`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code. If no translation is available then default untranslated values are returned.
location | no | `12` | Filter results in `products` to a particular location
type | no | `lesson` | Filter results in `products` to a particular product type. Product types options are: `package`, `accommodation`, `lesson`, `rental`, `service`, `bundle` and `item`.

## Get Specific Product

```json
{
  "id": 384,
  "title": "Surf Camp",
  "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
  "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
  "category_id": 1,
  "location_id": 12,
  "capacity": 10,
  "flexible_duration": true,
  "default_duration": 7,
  "duration_unit": "DAY",
  "default_start_time": null,
  "private": false,
  "allowed_checkin_days": "Mon,Tue,Fri,Sat,Sun",
  "gender": "MF",
  "min_pax": 1,
  "max_pax": 12,
  "min_duration": 7,
  "duration_variants": [2, 6, 10],
  "has_timeslots": false,
  "image": {
    "small": "https://url.to.small.image.jpg",
    "medium": "https://url.to.medium.image.jpg",
    "large": "https://url.to.large.image.jpg",
  },
  "price_from": 199,
  "dates": {
    "2018-05-01": {
      "allowed_for_checkin": true,
      "blocked": false,
      "available": 6,
      "time_slots": null
    },
    "2018-05-02": {
      "allowed_for_checkin": true,
      "blocked": true,
      "available": 6,
      "time_slots": null
    },
    "2018-05-03": {
      "allowed_for_checkin": true,
      "blocked": true,
      "available": 6,
      "time_slots": null
    },
    "2018-05-04": {
      "allowed_for_checkin": false,
      "blocked": false,
      "available": 5,
      "time_slots": null
    }
  }
}
```

Returns information about specific product including `dates` information for one month from current date:

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code. If no translation is available then default untranslated values are returned.

## Package Extras & Upgrades

```json
[
  {
    "product_id": 396,
    "product_type": "service",
    "type": "option",
    "title": "Pharetra Quam Dolor",
    "request_qty": false,
    "translated": null,
    "price": 0,
    "image": {
      "small": "https: //url.to.small.image.jpg",
      "medium": "https: //url.to.medium.image.jpg",
      "large": "https: //url.to.large.image.jpg",
    },
    "default_qty": 1,
    "min_qty": 0,
    "max_qty": 8,
    "pricing": "fixed",
    "description": "Integer posuere erat a ante venenatis dapibus posuere velit aliquet.",
    "extended_description": "Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus. Nullam quis risus eget urna mollis ornare vel eu leo."
  }
]
```

Returns an array of extras and upgrade options for a package.

Note that `type` can have one of the following values:

* `default`: included in the package by default;
* `alternative`: an item that can be selected as an alternative to the default option at no extra expense;
* `upgrade`: an item that can be selected as an alternative to the default option at an additional expense; and
* `option`: an optional extra that can be added to the package.

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/{id}/extras`

Where `{id}` is the id of the package.

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | yes | `2018-02-08` | The start date for the response in `YYYY-MM-DD` format.
duration | yes | `3` | Duration of the enquiry in nights.
pax | yes | `2` | Number of people making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.

# Accommodation

```json
[  
  {
    "id": 392,
    "title": "Surfside Dowm",
    "description": "Sed posuere consectetur est at lobortis",
    "location_id": 12,
    "start": "2017-08-01",
    "end": "2017-08-08",
    "pax": {
      "males": 2,
      "females": 3,
      "couples": 0
    },
    "pricing": "person",
    "image":  {
      "small":  "https: //url.to.small.image.jpg",
      "medium":  "https: //url.to.medium.image.jpg",
      "large":  "https: //url.to.large.image.jpg",
    },
    "item_type": "upgrade",
    "upgrade_price": 120,
    "srs": true,
    "srs_charge": 252,
    "variant_id": null
  }
]
```

Returns an array of accommodation options for a package.

### HTTP Request

`GET https://api.bookinglayer.io/pub/accommodations/{id}`

Where `{id}` is the id of the package which contains the accommodation options.

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | yes | `2018-02-08` | The start date for the response in `YYYY-MM-DD` format.
duration | yes | `3` | Duration of the enquiry in nights.
males | yes | `2` | Number of males making the enquiry
females | yes | `2` | Number of females making the enquiry
couples | yes | `2` | Number of couples making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.

# Dates & Availability

## Get Date & Availability

> Response for package, accommodation, unscheduled and single date activities, services & rentals:

```json
{
  "2018-05-01": {
    "allowed_for_checkin": true,
    "blocked": false,
    "available": 6,
    "time_slots": null
  },
  "2018-05-02": {
    "allowed_for_checkin": true,
    "blocked": true,
    "available": 6,
    "time_slots": null
  },
  "2018-05-03": {
    "allowed_for_checkin": true,
    "blocked": true,
    "available": 6,
    "time_slots": null
  },
  "2018-05-04": {
    "allowed_for_checkin": false,
    "blocked": false,
    "available": 5,
    "time_slots": null
  }
}
```

> Response for activities on timeslots:

```json
{
  "2018-05-01": {
    "time_slots": {
      "10:00": {
        "available": 10
      },
      "13:00": {
        "available": 5
      }
    }
  },
  "2018-05-02": {
    "blocked": true,
    "time_slots": null
  },
  "2018-05-03": {
    "time_slots": {
      "17:30": {
        "available": 10
      }
    }
  },
  "2018-05-04": {
    "time_slots": null
  }
}
```

Returns dates and availability information for a specific product.

* If product is a fixed date product then all future dates will be returned; else
* 1 month worth of availability data will be returned from today's date or `start_date` if provided.

Note that the flag for whether the product is on timeslots is returned as part of the `/products` and/or `/products/{id}` response.

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/availabilities/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | no | `2018-02-08` | The start date for the response in `YYYY-MM-DD` format. If not provided then the current date will be taken as the start date.

# Prices

```json
{
  "total_price": 12
}
```

Returns price information for a specific product.

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/prices/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
start_date | yes | `2018-02-08` | The start date for the response in `YYYY-MM-DD` format.
pax | yes | `2` | Number of guests making the enquiry
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product. If nothing is provided the product's `default_duration` will be used

# Seasons

```json
{
  "80": {
    "id": 80,
    "title": "High",
    "season_segments": [
      {
        "id": 138,
        "from": "1900-01-01T00:00:00+0000",
        "to": "1900-04-29T00:00:00+0000",
        "season_id": 80
      },
      {
        "id": 139,
        "from": "1900-08-01T00:00:00+0000",
        "to": "1900-12-31T00:00:00+0000",
        "season_id": 80
      }
    ]
  },
  "81": {
    "id": 81,
    "title": "Low",
    "season_segments": [
      {
        "id": 137,
        "from_date": "1900-05-30T00:00:00+0000",
        "to_date": "1900-07-31T00:00:00+0000",
        "season_id": 81
      }
    ]
  }
}
```

Returns seasons for a business.

### HTTP Request

`GET https://api.bookinglayer.io/pub/seasons`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.

# Deeplinking

## Deeplink URL to Product

```json
{
  "url": "https://yourbusiness.bookinglayer.io/frontoffice/product/384?start='2018-03-02'&duration=4&male=2"
}
```

Returns the URL required to link directly to the product in Bookinglayer's frontoffice booking form, including all start date, end date, duration and pax settings.

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/{id}/deeplink`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
key | yes | | Your unique API access key.
product_id | yes | `384` | The product id.
start_date | no | `2018-02-08` | The start date in `YYYY-MM-DD` format.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product.
end_date | no | `2018-02-20` | The end date in `YYYY-MM-DD` format.
males | no | `2` | Number of males.
females | no | `3` | Number of females.
couples | no | `1` | Number of couples.
language | no | `es` | Language that the frontoffice form should be rendered in.
currency | no | `USD` | Currency that should be selected when frontoffice loads.
