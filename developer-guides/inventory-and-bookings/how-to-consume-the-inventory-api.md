# How to Consume the Inventory Api

The purpose of this document is to provide high level information about how to consume the BookingStudio Inventory API. The document includes information about the terminology, the basic information about the API, and how to
consume the API along with examples and references.

The API is designed to be consumed by `Partners` that want to integrate the `Client` inventory and to make possible for the `Partner` to make bookings in the `Client` system.

> [!NOTE]
> For detailed information about the API, please refer to the API documentation.
> [More information here](../../README.md)

> [!IMPORTANT]
> Please refer the [terminology document](terminology.md) for a detailed explanation of the terminology used in this document.

## Basic Information About the Api

This section provides basic information about the API. It includes information about `Authentication`, the `User-Agent`,
the `Versioning`, and the `Rate Limiting`.

### Authentication

The API uses HTTP Basic Authentication. You need to obtain an api key from the `Client` in order to consume the ´Api`.
The api key is used as the username and the password are empty.

### User-Agent

You should set the `User-Agent` header to a string that identifies your application. For example:

```
User-Agent: MyApplication/1.0
```

> [!IMPORTANT]
> If you don't set the `User-Agent` header, your requests will be rejected.

### Versioning

The API is versioned. The version is specified in the header of the request. It should not be confused with the version
specified in the `Uri` of the request. This refers to the version of the API itself.

> [!IMPORTANT]
> Each endpoint has its own version that should be used and provided in the header of the request.

### Rate Limiting

The API is rate limited. If you exceed the rate limit you will receive a 429 status code, and you will have to wait
before you can make another request.

> [!WARNING]
> Some `Endpoints` has a fair usage policy. This means that you are only allowed to fetch in line with the policy
> outlined in this document. This is a soft policy, and you will not be blocked if you exceed the limit, but we reserve
> the
> right to block you, **without notice**, if you exceed the limit by a large margin. This is to ensure that the system
> is
> not overloaded and for the benefit of all `Partners` and `Clients`.

> [!IMPORTANT]
> If you are being rate limited, you should implement an exponential backoff strategy. This means that you should wait
> before making another request and not make another request immediately.

## Consuming the API

This sections describes how to consume the API on a high level. It includes information about the endpoints and how to
use them.

### Fetching Languages

The API provides an endpoint with all the `Languages` available in the `Client`.

#### Example Request

> [!NOTE]
> Use version 1 of this `Endpoint`

> [!NOTE]
> **Rate Limit** Rate limiting is in place to prevent abuse. If you exceed the rate limit you will receive a 429 status.
> Under normal circumstances you should not hit this limit.

```http request
GET /v1/languages.xml
Version: 1
```

> [!IMPORTANT]
> The `Id` of the language is **not** the same across `Clients` and should be treated as unique for the `Client` you are
> working with.

### Fetching Currencies

The API provides an endpoint with all the `Currencies` available in the `Client`.

#### Example Request

> [!NOTE]
> Use version 1 of this `Endpoint`

> [!NOTE]
> **Rate Limit** Rate limiting is in place to prevent abuse. If you exceed the rate limit you will receive a 429 status.
> Under normal circumstances you should not hit this limit.

```http request
GET /v1/currencies.xml
Version: 1
```

> [!IMPORTANT]
> The `Id` of the currency is **not** the same across `Clients` and should be treated as unique for the `Client` you are
> working with.

### Fetching Items

The API provides an `Endpoint` with all the `Items` made available for the `Partner` by the `Client`.

> [!IMPORTANT]
> The `Id` of the item is **not** the same across `Clients` and should be treated as unique for the `Client` you are
> working with.

> [!NOTE]
> At the moment extras can only be added when you're creating a booking.

#### Example Request and Response

> [!NOTE]
> Use version 1 of this `Endpoint`

> [!NOTE]
> **Rate Limit** Rate limiting is in place to prevent abuse. If you exceed the rate limit you will receive a 429 status.
> Under normal circumstances you should not hit this limit.

**Example Request**

```http request
GET /v1/items.xml
Version: 1
```

**Example Response Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<Items>
    <Item/>
    <Item/>
</Items>
```

### Fetching Lodgings

The API provides an `Endpoint` with all the `Lodgings` made available for the `Partner` by the `Client`.

#### Example Request and Response

> [!NOTE]
> Use version 1 of this `Endpoint`

> [!NOTE]
> **Rate Limit** You're allowed to fetch this list once per language every night (danish time) to keep your data in
> sync.

**Example Request**

```http request
GET /v1/lodgings.xml?lan={languageid}
Version: 1
```

**Example Response Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<Lodgings>
    <Lodging/>
    <Lodging/>
</Lodgings>
```

### Fetching BookingOptions

The API provides an `Endpoint` where you can query the `BookingOptions` available for the `Lodgings` made available for
the `Partner` by the `Client`.

> [!NOTE]
> The combinations of `BookingOptions` is high and importing each and every combination is probably not the best
> strategy. We recommend that you import the most common combinations and then update the prices for the other
> combinations that are not imported.

#### The Most Important Parameters

The most important parameters for this `Endpoint` are:

| Name | Required | Description                                                                                                                                                                               |
|------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| lan  | Yes      | The language id                                                                                                                                                                           |
| dur  | Yes      | A comma separated list of length of the stay in days                                                                                                                                      |
| adu  | No       | The number of adults. If omitted the request will assume 2                                                                                                                                |
| chi  | No       | The number of children. If omitted the request will assume 0                                                                                                                              |
| pet  | No       | The number of pets. If omitted the request will assume 0                                                                                                                                  |
| itp  | No       | An enum value that specifies what kind of items you want to include in the response. Valid values are `None`, `Mandatory` (default), `All`. If omitted the system uses the default value. |

*To see the full list of parameters, please refer to the API documentation.*

#### Example Request and Response

> [!WARNING]
> Always use version 3 of our BookingOption. Version 1 and 2 is deprecated and will be removed.

> [!NOTE]
> **Rate Limit** You're allowed to fetch the prices once per language and reasonable person combination every night (
> danish time) to keep your data in sync.

**Example Request**

We have a similar `Endpoint` that allows you to fetch the `BookingOptions` for a specific `Lodging`. This is useful if
you want to fetch the `BookingOptions` for a specific `Lodging` and not all `Lodgings`, for example when you are
reacting to webhooks.
The `Endpoint` is `/v1/lodgings/{lodgingid}/bookingoptions.xml`.

This example shows how to fetch the `BookingOptions` for all `Lodgings` for a specific language and duration.

```http request
GET /v1/bookingoptions.xml?lan={languageid}&adu={adults}&dur={durations}
Version: 3
```

**Example Response Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<BookingOptions>
    <BookingOption/>
    <BookingOption/>
</BookingOptions>
```

Each `BookingOption` includes information about the price, discount and items according to the value of the `itp`
parameter specified.

**Pagination**

The response will be paginated using the **RFC5988 convention** with the `Link` header. The `Link` header will include a
`next` link if there are more pages to fetch.

```http header
Link: <https://{apiHost}/rest/v1/bookingoptions.xml?lan=1&pos=2055489e-bd01-4ec4-9ade-a3445d65ade8> rel="next"
```

#### References

##### Reference for the BookingOption Element (Response)

Here are a list of the most important data on a `BookingOption`.

| Name              | Type    | Description                                                                                                                                                                          |
|-------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LodgingUnitTypeId | Integer | The `Id` of the `LodgingUnitType` in the `Client` installation                                                                                                                       |
| LodgingId         | Integer | The `Id` of the lodging in the `Client` installation                                                                                                                                 |
| Duration          | Integer | The length of the stay in days                                                                                                                                                       |
| Arrival           | Date    | The date of arrival                                                                                                                                                                  |
| Departure         | Date    | The date of departure                                                                                                                                                                |
| Currency          | String  | The currency of the `Price`                                                                                                                                                          |
| NormalPrice       | Decimal | The price before any discount is subtracted                                                                                                                                          |
| Price             | Decimal | The price of the booking option after subtracting any `Discount` to be displayed to the `Guest`. **Note** If there are no discounts, the `Price` and the `NormalPrice` will be equal |
| HasDiscount       | Boolean | A flag that indicates if there is a discount on the `Price`                                                                                                                          |
| LanguageId        | Integer | The `Id` of the language in the `Client` installation                                                                                                                                |
| Adults            | Integer | The number of adults the price is calculated for                                                                                                                                     |
| Children          | Integer | The number of children the price is calculated for                                                                                                                                   |
| Infants           | Integer | The number of infants the price is calculated for                                                                                                                                    |
| Pets              | Integer | The number of pets the price is calculated for                                                                                                                                       |
| Status            | String  | The status of the booking option. It can be `Booking`, `Probability`, `ProbabilityAsBooking` (see definition below)                                                                  |
| ItemPrices        | Array   | An array of `ItemPrice` elements that indicates what items must or can be added to the booking                                                                                       |

> [!IMPORTANT]
> The `LodgingId` and the `LodgingUnitTypeId` are often the same but can be different. They represent two different
> things in the system and both should be stored in your system.

**Understanding the `Status` of a `BookingOption`**

* **Booking**: The `BookingOption` is available for booking and the price is correct.
* **Probability**: The `BookingOption` can be booked but the price is **not yet available** (the price is zero), and the
  `Booking` is not guaranteed by the `Client`. This is often used for `Lodgings` that are not yet open for booking for
  the seasonal year.
* **ProbabilityAsBooking**: The `BookingOption` can be booked but the price is an indication and is subject to change.
  The `Booking` is not guaranteed by the `Client` nor is the price. This is often used for `Lodgings` that are not yet
  open for booking for the seasonal year.

> [!NOTE]
> By default the `Probability` and `ProbabilityWithPrice` are not available to the `Partner`, but only on the `Clients`
> own website.
> If you need those `BookingOptions` you need to contact the `Client` to get access to them.

##### Reference for the ItemPrice Element (Response)

Here are a list of the data on a `ItemPrice` element.

| Name        | Type    | Description                                                           |
|-------------|---------|-----------------------------------------------------------------------|
| ItemId      | Integer | The `Id` of the `Item` in the `Client` installation                   |
| UnitPrice   | Decimal | The unit price of the `Item`                                          |
| Currency    | String  | The currency of the `UnitPrice`                                       |
| IsMandatory | Boolean | A flag that indicates if the `Item` is mandatory on the `Booking`     |
| MinQuantity | Integer | The minimum quantity of the `Item` that can be added to the `Booking` |
| MaxQuantity | Integer | The maximum quantity of the `Item` that can be added to the `Booking` |

### Fetching Calendars

The API provides an `Endpoint` with the availability of all the `Lodgings` made available for the `Partner` by the
`Client`.

You can easily parse each calendar and mark affected `BookingOptions` as unavailable on your side.

#### Example Request and Response

> [!WARNING]
> Use version 2 of our Calendar `Endpoint`. Version 1 is deprecated and will be removed.

> [!NOTE]
> **Rate Limit** You're allowed to fetch this list every half an hour around the clock to keep the data in sync.

**Example Request**

```http request
GET /v1/calendars.xml
Version: 2
```

**Example Response Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<Calendars>
    <Calendar LodgingUnitTypeId="99" AnchorDate="2024-10-15T00:00:00+02:00"
              Availabilities="0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0"
              SeasonCodes="D,D,D,D,E,E,E,E,E,E,E,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,D,D,D,D,D,D,D,D,D,D,D,D,A,A,A,A,A,A,A,D,D,D,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,E,E,E,E,E,E,E,E,E,E,E,E,E,E,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,C,C,C,C,C,B,B,B,B,B,B,C,C,C,C,C,F,F,F,F,F,F,F,F,F,F,F,F,F,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,D,D,D,D,D,E,E,E,E,E,E,E,E,E,E,E,D,D,D,D,D,D,D,C,C,C,C,C,C,C,B,B,B,B,B,B,B,A+,A+,A+,A+,A+,A+,A+,A+,A+,A+,A+,A+,A+,A+,A+,A,A,A,A,A,A,A,A,A,A,A,A,A,B,B,B,B,B,B,B,C,C,C,C,C,C,C,D,D,D,D,D,D,D,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,E,D,D,D,D,D,D,D,C,C,C,C,C,C,C,D,D,D,D,D,D,D,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,F,D,D,D,D,D,D,D,D,D,D,D,D,A,A,A,A,A,A,A,D,D,D,D,F,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,"/>
</Calendars>
```

> [!NOTE]
> This endpoint utilizes our fair usage policy, meaning it's allowed to fetch this list every half an hour
> around the clock to keep the data in sync.

#### References

##### Reference for the Calendar Element (Response)

| Name              | Type    | Description                                                                                                                                                                                                                                            |
|-------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LodgingUnitTypeId | Integer | The `Id` of the `LodgingUnitType` in the `Client` installation                                                                                                                                                                                         |
| AnchorDate        | Date    | The date of the first day in the calendar. This is the date that the first availability in the calendar is for.                                                                                                                                        |
| Availabilities    | String  | A comma separated list of availabilities for each day in the calendar. The value is `1` if the lodging is available and `0` if the lodging is not available. The first value is for the `AnchorDate` and the last is for the last day in the calendar. |
| SeasonCodes       | String  | A comma separated list of season codes for each day in the calendar. The first value is for the `AnchorDate` and the last is for the last day in the calendar.                                                                                         |

### Checking for Availability on Checkout

We recommend that you check for availability during checkout to make sure that the `BookingOption` is still
available and to get the correct prices for both the `Booking` and the `Items` for the specific request.

The request will return a **200 status code** if the `BookingOption` is available. Otherwise, it will return a **404
status code** meaning that the `BookingOption` is not available.

#### Example Request and Response

> [!WARNING]
> Always use version 2 of this `Endpoint`. Version 1 is deprecated and will be removed.

> [!NOTE]
> **Rate Limit** You're allowed to check the availability every time a `BookingOption` is selected by the `Guest`. There
> is some basic rate limiting in place to prevent abuse, but under normal circumstances you should not be affected by
> this limit.

**Example Request Headers**

```http request
POST /v1/bookingoptions/available.xml
Version: 2
```

**Example Request Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<BookingOption
        LodgingId="4"
        LodgingUnitTypeId="4"
        LanguageId="1"
        Arrival="2024-11-15"
        Departure="2024-11-22"
        Duration="7"
        Adults="2"
        Children="0"
        Infants="0"
        Pets="0"
/>
```

Required Parameters in the BookingOption:

* LodgingUnitTypeId
* LanguageId
* Arrival
* Departure
* Duration
* Adults
* Children (if omitted the request will assume 0)
* Infants (if omitted the request will assume 0)
* Pets (if omitted the request will assume 0)

**Example Response Headers**

```http header
Status: 200 OK
```

**Example Response Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<BookingOption LodgingUnitTypeId="4" WinterRuleWarning="false" IsAvailable="true" Priority="Normal" Duration="7"
               Departure="2024-11-22T00:00:00" Arrival="2024-11-15T00:00:00" Currency="DKK" Price="2821"
               NormalPrice="3135" PartlyWithoutDiscountTags="false" DaysWithDiscount="7" Tags="LastMinute"
               DiscountInternalName="LastMinute 10%" HasDiscount="true" Pets="0" Infants="0" Children="0"
               Adults="2" LanguageId="1" IsRegularWeek="true" LodgingId="4" Status="Booking" DiscountTags="LastMinute"
               DaysWithAddition="0">
    <ItemPrices>
        <ItemPrice ItemId="3" UnitPrice="195" Currency="DKK" IsMandatory="true" MinQuantity="1" MaxQuantity="1"/>
        <ItemPrice ItemId="5" UnitPrice="75" Currency="DKK" IsMandatory="false" MinQuantity="0" MaxQuantity="8"/>
    </ItemPrices>
</BookingOption>
```

> [!NOTE]
> If the `BookingOption` is available, you'll get a complete `BookingOption` with the `Booking` price and all the prices
> for both mandatory `Items` and optional `Items`.

### Webhooks

If the `Client` has our `Automation Module` enabled, it's possible for them to set up a webhook that can help to keep
your data in sync with the `Client`.

We have webhooks for the following events to help you keep your data in sync:

* Availability changes
* Price changes

> [!NOTE]
> Prices can change by custom rules laid out by the `Client`. This normally happens when the date changes from today to
> tomorrow. The webhook for prices will **not** be called for these changes, but the nightly import will make sure to
> catch these changes.
