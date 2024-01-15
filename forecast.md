# Forecast API

The Forecast API allows you to access film data including actuals, future predictions, and metadata like title cast and director.

 * [/api/v3/metadata](#metadata): Film metadata
 * [/api/v3/date](#date): Film actuals or predictions for a given date
 * [/api/v3/date-range](#date-range): Day-by-day film actuals or predictions for a given date range
 * [/api/v3/week](#week): Day-by-day film actuals or predictions for a given week (starting Monday and ending Sunday)
 * [/api/v3/lifetime](#lifetime): Film lifetime actuals or predictions
 * [/api/v3/lifetime/top](#top-lifetime): List the films with the highest lifetime actuals or predictions in the given territories
 * [/api/v3/opening-weekend](#opening-weekend): Film opening weekend actuals or predictions
 * [/api/v3/comps](#comps): Top comps for film

## Authentication

To be authorized, your request must include the HTTP header `Authorization: Token <your token>` with a request token which you obtain from sending a `POST` request to the `/login` endpoint:

```sh
> curl -X POST -H 'Content-type: application/json' -d '{"password": "<your password>", "email": "<your login email>"}' "https://forecast.gower.st/login"
{
  "user": { ... },
  "token": "<your token>"
}
```

Use the same `email` and `password` you use to log in to Forecast.

## Querying for multiple territories or films in a single request

Any of the endpoints support querying for multiple films or territories. To do so, repeat the `ter_id` or `ptn` parameter for each value. For example you can query for "Top Gun Maverick" and "Everything Everywhere All At Once" in both UK and Germany like so:

```
GET https://forecast.gower.st/api/v3/date?ptn=898337&ptn=945975&ter_id=UK&ter_id=DE&date=2022-05-29
```

## Endpoints

### Metadata

```
GET https://forecast.gower.st/api/v3/metadata
```

Query film metadata for one or more films in one or more territories.

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/metadata?ptn=898337&ter_id=UK"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    898337
  ],
  "data": [
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "international_title": "Top Gun: Maverick",
      "release_date": "2022-05-27",
      "title_missing": false,
      "distributor_id": "PARAMOUNT2",
      "title": "Top Gun: Maverick",
      "distributor": "Paramount Int'l",
      "actors": [
        "Bashir Salahuddin",
        "Charles Parnell",
        "Danny Ramirez",
        "Ed Harris",
        "Glen Powell",
        "Greg Tarzan Davis",
        "Jay Ellis",
        "Jean Louisa Kelly",
        "Jennifer Connelly",
        "Jon Hamm",
        "Lewis Pullman",
        "Manny Jacinto",
        "Miles Teller",
        "Monica Barbaro",
        "Tom Cruise",
        "Val Kilmer"
      ],
      "directors": [
        "Joseph Kosinski"
      ],
      "rating": "12"
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |

### Date

```
GET https://forecast.gower.st/api/v3/date
```

Film actuals or predictions for a given date returned. The response contains the actuals or predictions of the day (`base`) as well as the cumulative total for the date (`cume_base`). Both numbers are in `USD` unless a different `currency` is specified. The `type` field specifies whether the `base` and `cume_base` are actuals (i.e. have happened) or predictions (e.g. haven't happened yet).

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/date?ptn=898337&ter_id=UK&date=2022-05-29"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    898337
  ],
  "date": "2022-05-29",
  "data": [
    {
      "primary_title_no": 898337,
      "ter_id": "UK",
      "date": "2022-05-29",
      "week": 1,
      "day_of_week": 7,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `date`    | yyyy-mm-dd | The date for which we want to see actuals / predictions | **Yes** |
| `with_low_high` | true/false | Include low and high bounds for predictions (defaults to `false`) | No  |
| `with_local_currency` | true/false | Include local currency alongside `USD` for actuals / predictions | No  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `currency` | String | Three letter currency code like "GBP" for British pounds | No  |
| `currency_date` | yyyy-mm-dd | The date of for the exchange rate used if `currency` is specified | No  |



### Date Range

```
GET https://forecast.gower.st/api/v3/date-range
```

Film actuals or predictions for a all dates within given date range ranging from `from` (included) to `to` (excluded). The response contains the actuals or predictions of each day (`base`) as well as the cumulative total for each date (`cume_base`). Both numbers are in `USD` unless a different `currency` is specified. The `type` field specifies whether the `base` and `cume_base` are actuals (i.e. have happened) or predictions (e.g. haven't happened yet).

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/date-range?ptn=898337&ter_id=UK&from=2022-05-27&to=2022-05-30"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    898337
  ],
  "from": "2022-05-27",
  "to": "2022-05-30",
  "data": [
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-27",
      "week": 1,
      "day_of_week": 5,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    },
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-28",
      "week": 1,
      "day_of_week": 6,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    },
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-29",
      "week": 1,
      "day_of_week": 7,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `from`    | yyyy-mm-dd | The start of the date range (included) | No |
| `to`    | yyyy-mm-dd | The end of the date range (excluded) | No |
| `with_low_high` | true/false | Include low and high bounds for predictions (defaults to `false`) | No  |
| `with_local_currency` | true/false | Include local currency alongside `USD` for actuals / predictions | No  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `currency` | String | Three letter currency code like "GBP" for British pounds | No  |
| `currency_date` | yyyy-mm-dd | The date of for the exchange rate used if `currency` is specified | No  |


### Week

```
GET https://forecast.gower.st/api/v3/week
```

Film actuals or predictions for each day in a given week in the film's release. Week `1` is the opening week etc. To align international data, the week always starts with Monday and ends with Sunday. The response contains the actuals or predictions of each day (`base`) as well as the cumulative total for each date (`cume_base`). Both numbers are in `USD` unless a different `currency` is specified. The `type` field specifies whether the `base` and `cume_base` are actuals (i.e. have happened) or predictions (e.g. haven't happened yet).

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/week=1?ptn=898337&ter_id=UK&week=1"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    898337
  ],
  "week": 1,
  "data": [
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-25",
      "week": 1,
      "day_of_week": 3,
      "drop": null,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    },
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-26",
      "week": 1,
      "day_of_week": 4,
      "drop": 2993.17,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    },
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-27",
      "week": 1,
      "day_of_week": 5,
      "drop": null,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    },
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-28",
      "week": 1,
      "day_of_week": 6,
      "drop": null,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    },
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2022-05-29",
      "week": 1,
      "day_of_week": 7,
      "drop": null,
      "type": "actual",
      "currency": "USD",
      "base": 12345.6,
      "cume_base": 123456.7
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `week`    | Number | Use `1` for opening week, `2` for week after etc | **Yes** |
| `with_low_high` | true/false | Include low and high bounds for predictions (defaults to `false`) | No  |
| `with_local_currency` | true/false | Include local currency alongside `USD` for actuals / predictions | No  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `currency` | String | Three letter currency code like "GBP" for British pounds | No  |
| `currency_date` | yyyy-mm-dd | The date of for the exchange rate used if `currency` is specified | No  |


### Lifetime

```
GET https://forecast.gower.st/api/v3/lifetime
```

The lifetime actuals or predictions for one or more films. The lifetime is in `USD` unless a different `currency` is specified. The `type` field specifies whether the `base` is an actual (i.e. have happened) or a prediction (e.g. haven't happened yet).

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/lifetime?ptn=898337&ter_id=UK"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    898337
  ],
  "data": [
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2023-07-29",
      "week": 62,
      "type": "actual",
      "currency": "USD",
      "base": 1234567.8
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `with_low_high` | true/false | Include low and high bounds for predictions (defaults to `false`) | No  |
| `with_local_currency` | true/false | Include local currency alongside `USD` for actuals / predictions | No  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `currency` | String | Three letter currency code like "GBP" for British pounds | No  |
| `currency_date` | yyyy-mm-dd | The date of for the exchange rate used if `currency` is specified | No  |


### Top Lifetime

```
GET https://forecast.gower.st/api/v3/lifetime/top
```

Returns a list of films one or more territories ranked by their lifetime. The films returned are all released in at least one of the territories between the `from` date and the `to` date. These two values default to Jan 1st of the current year and Jan 1st of next year.

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token `cat token.txt`" "https://forecast.gower.st/api/v3/lifetime/top?ter_id=UK&ter_id=DE&limit=5" | jq '.'
```

**Sample Response**:
```json
{
  "ter_ids": [
    "DE",
    "UK"
  ],
  "from": "2023-01-01",
  "to": "2024-01-01",
  "data": [
    {
      "primary_title_no": 952324,
      "type": "estimate",
      "base": 123456789,
      "currency": "USD",
      "release_date": "2023-07-20"
    },
    {
      "primary_title_no": 894934,
      "type": "estimate",
      "base": 12345678,
      "currency": "USD",
      "release_date": "2023-04-05"
    },
    {
      "primary_title_no": 938493,
      "type": "estimate",
      "base": 1234567,
      "currency": "USD",
      "release_date": "2023-07-20"
    },
    {
      "primary_title_no": 919019,
      "type": "estimate",
      "base": 123456,
      "currency": "USD",
      "release_date": "2023-05-03"
    },
    {
      "primary_title_no": 931323,
      "type": "estimate",
      "base": 12345,
      "currency": "USD",
      "release_date": "2023-12-14"
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `from`    | yyyy-mm-dd | Include films released after this date (included) | No |
| `to`    | yyyy-mm-dd | Only Include films released before this date (excluded) | No |
| `with_low_high` | true/false | Include low and high bounds for predictions (defaults to `false`) | No  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `currency` | String | Three letter currency code like "GBP" for British pounds | No  |
| `currency_date` | yyyy-mm-dd | The date of for the exchange rate used if `currency` is specified | No  |


### Opening Weekend

```
GET https://forecast.gower.st/api/v3/opening-weekend
```

The opening weekend actuals or predictions for one or more films. The opening weekend is in `USD` unless a different `currency` is specified. The `type` field specifies whether the `base` is an actual (i.e. have happened) or a prediction (e.g. haven't happened yet).

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/lifetime?ptn=898337&ter_id=UK"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    898337
  ],
  "data": [
    {
      "ter_id": "UK",
      "primary_title_no": 898337,
      "date": "2023-07-29",
      "week": 62,
      "type": "actual",
      "currency": "USD",
      "base": 1234567.8
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `with_low_high` | true/false | Include low and high bounds for predictions (defaults to `false`) | No  |
| `with_local_currency` | true/false | Include local currency in response (defaults to `false`) | No  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `currency` | String | Three letter currency code like "GBP" for British pounds | No  |
| `currency_date` | yyyy-mm-dd | The date of for the exchange rate used if `currency` is specified | No  |

### Comps

```
GET https://forecast.gower.st/api/v3/comps
```

A ranked list of comps for one or more films. The comps are ranked by the Gower st "Smart Score" which weights the comp votes of the API user and the company of the API user more highly than comp votes by users in other organisations. You can use the `view` parameter to adjust the ranking behaviour. The default value is `all`. Other values are:
 * `user`: Only see comps added or voted on by API user
 * `team`: Only see comps added or voted on by all members of organisation
 * `gower`: Only see comps added or voted on by members of the Gower St team
 * `all`: All comps, with votes by API user and organisation weighted higher
 * `core`: All comps with no special weight attached to votes by API user or other users in same organisation

**Sample request**:

```
curl -H "Content-type: application/json" -H "Authorization: Token <token>" "https://forecast.gower.st/api/v3/comps?ter_id=UK&view=core&ptn=920845&limit=1"
```

**Sample Response**:
```json
{
  "ter_ids": [
    "UK"
  ],
  "primary_title_nos": [
    920845
  ],
  "view": "core",
  "data": [
    {
      "primary_title_no": 871308,
      "date": "2023-07-25T15:14:34Z",
      "favours": [],
      "comp_booking_id": "71945",
      "parent_booking_id": "165059",
      "territory_id": "UK",
      "company_id": "1",
      "parent_primary_title_no": 920845,
      "score": 61,
      "user_id": "352",
      "source_id": 1
    }
  ]
}
```

**Parameters**:

| Parameter  | Type    | Description  | Required |
|------------|---------|--------------|----------|
| `ter_id`     | String  | Two letter country code (e.g. `DE` for Germany) | **Yes**  |
| `ptn`     | Number  | IBOE Title Number (e.g. `898337` for Top Gun) | **Yes**  |
| `use_dom_ptn` | true/false  | Use BOE title number instead of the default behaviour of using IBOE title number | No  |
| `view` | String | Methodology for smart score (`all`, `user`, `team`, `gower`, `core`) | No  |
| `limit` | Number | Limit of how many comps are returned | No  |
