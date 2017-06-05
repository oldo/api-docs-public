---
title: API Reference

language_tabs:

toc_footers:
  - Thanks for checking out Bookinglayer's API docs :-)
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Hola! This is a preliminary version of Bookinglayer's Public API docs which we can use to define expected endpoint routes, request payloads and structure of responses.

# Things to Discuss

## General Stuff

* Client identification? API keys?
* Translations - return responses with default language of account. Language will be optional parameter with translations returned if set for product.
* Currency - return responses with default currency of account. Currency will be optional parameter with prices returned in requested currency.
* How to identify/handle multiple locations?

API docs for inspiration

* https://stripe.com/docs/api/node#intro
* https://docs.travis-ci.com/api/#overview

# Request Structure

Insert details of the request structure here:

* Root URL: `https://api.bookinglayer.io/pub/`
* How are we going to identify the business? Just pass `business_id` with each request? Or have an actual API key?
* Header parameters:
  * Accept
  * Content-Type?
  * Authorization?

# Products

## Get All Products

```json
{
  "categories": [
    {
      "id": 1,
      "title": "Surf Camps",
      "image": "https://url.to.image.jpg"
    }
  ],
  "packages": [
    {
      "id": 384,
      "title": "Surf Camp",
      "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
      "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
      "category_id": 1,
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
      "duration_variants": null,
      "has_timeslots": false,
      "image": {
        "small": "https://url.to.small.image.jpg",
        "medium": "https://url.to.medium.image.jpg",
        "large": "https://url.to.large.image.jpg",
      },
      "price_from": 199
    }
  ],
  "accommodation": [],
  "activities": [],
  "services": [],
  "rentals": [],
  "items": [],
  "bundles": []
}
```

Returns general information about all available products:

### HTTP Request

`GET https://api.bookinglayer.io/pub/products`

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
business_id | yes | `643` | Your unique business identifier.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code. If no translation is available then default untranslated value is returned.

## Get Specific Product

```json
{
  "id": 384,
  "title": "Surf Camp",
  "description": "Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.",
  "extended_description": "Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus.",
  "category_id": 1,
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
  "duration_variants": null,
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
      "available": 6
    },
    "2018-05-02": {
      "allowed_for_checkin": true,
      "blocked": true,
      "available": 6
    },
    "2018-05-03": {
      "allowed_for_checkin": true,
      "blocked": true,
      "available": 6
    },
    "2018-05-04": {
      "allowed_for_checkin": false,
      "blocked": false,
      "available": 5
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
business_id | yes | `643` | Your unique business identifier.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
language | no | `es` | If translation is available, returns translated fields such as "title" & "description" according to language code. If no translation is available then default untranslated value is returned.

# Dates & Availability

## Get Date & Availability

> Response for package, accommodation, unscheduled and single date activities, services & rentals:

```json
{
  "2018-05-01": {
    "allowed_for_checkin": true,
    "blocked": false,
    "available": 6
  },
  "2018-05-02": {
    "allowed_for_checkin": true,
    "blocked": true,
    "available": 6
  },
  "2018-05-03": {
    "allowed_for_checkin": true,
    "blocked": true,
    "available": 6
  },
  "2018-05-04": {
    "allowed_for_checkin": false,
    "blocked": false,
    "available": 5
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

`GET https://api.bookinglayer.io/pub/products/availability/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
business_id | yes | `643` | Your unique business identifier.
start_date | no | `2018-02-08` | The start date for the response in `YYYY-MM-DD` format. If not provided then the current date will be taken as the start date.

# Prices

```json
{
  "unit_price": 6,
  "gross_price": 12,
}
```

Returns price information for a specific product.

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/prices/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
business_id | yes | `643` | Your unique business identifier.
currency | no | `EUR` | Currency for returned prices. If not supplied the account's default currency will be used in response.
start_date | yes | `2018-02-08` | The start date for the response in `YYYY-MM-DD` format.
pax | yes | `2` | Number of guests making the enquiry
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product. If nothing is provided the product's `default_duration` will be used

# Deeplink URL to Product

```json
{
  "url": "https://yourbusiness.bookinglayer.io/frontoffice/product/1568?start='2018-03-02'&duration=4&male=2"
}
```

Returns the URL required to link directly to the product, including all start date, end date, duration and pax settings.

### HTTP Request

`GET https://api.bookinglayer.io/pub/products/link/{id}`

Where `{id}` is the id of the product

### Query Parameters

Parameter | Required | Example | Description
--------- | ------- | ------- | -----------
business_id | yes | `643` | Your unique business identifier.
start_date | no | `2018-02-08` | The start date in `YYYY-MM-DD` format.
duration | no | `3` | Duration of the enquiry in the `duration_unit` defined for the product.
end_date | no | `2018-02-20` | The end date in `YYYY-MM-DD` format.
males | no | `2` | Number of males.
females | no | `3` | Number of females.
couples | no | `1` | Number of couples.
language | no | `es` | Language that the frontoffice form should be rendered in.
currency | no | `USD` | Currency that should be selected when frontoffice loads.
