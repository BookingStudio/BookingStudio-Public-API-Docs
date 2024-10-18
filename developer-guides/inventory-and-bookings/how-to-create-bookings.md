# How to Create Bookings

The purpose of this document is to provide high level information about how to create `Bookings` in the API.

The API is designed to be consumed by `Partners` that want to integrate the `Client` inventory and to make possible for the `Partner` to make bookings in the `Client` system.

> [!NOTE]
> For detailed information about the API, please refer to the API documentation.

> [!IMPORTANT]
> Please refer the [terminology document](terminology.md) for a detailed explanation of the terminology used in this document.

### Versioning

The API is versioned. The version is specified in the header of the request. It should not be confused with the version
specified in the `Uri` of the request. This refers to the version of the API itself.

> [!IMPORTANT]
> Each endpoint has its own version that should be used and provided in the header of the request.

## Creating a Booking

The API provides an `Endpoint` that allows the `Partner` to create bookings.

### Price strategy

The `Partner` API key is configured by the `Client` on how to handle bookings. The `Client` can choose what strategy
should be
applied if there is a mismatch between the price of the booking being created and the price within the system. The
options are:

* Accept the booking, but with the price from the system
* Deny the booking

> [!NOTE]
> At the moment we don't have a strategy that allows the system to accept the price provided by the `Partner` if it's
> different from the price in the system.

### Example Request and Response

> [!WARNING]
> Use version 3 of our Booking `Endpoint`. Version 1 and 2 is deprecated and will be removed.

> [!NOTE]
> **Rate Limit** Rate limiting is in place to prevent abuse. If you exceed the rate limit you will receive a 429 status.
> Under normal circumstances you should not hit this limit.

**Example Request Headers**

```http request
POST /v1/booking/create.xml
Version: 3
```

**Example Request Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<BookingRequest>
    <Customer FirstName="John" LastName="Doe" Email="john-doe@john-doe.com" IsoCountry="DK" PostalCode="8000"
              Address="John Doe Street 1" City="Aarhus"/>
    <Note>Some note from the customer</Note>
    <InternalNote>Some internal note</InternalNote>
    <BookingOption LodgingId="1234"/>
    <Items>
        <Item ItemId="1" Quantity="1"/>
        <Item ItemId="2" Quantity="2"/>
    </Items>
</BookingRequest>
```

**Example Response Headers**

```http header
HTTP/1.1 201 Created
```

**Example Response Body**

```xml
<?xml version="1.0" encoding="utf-8"?>
<OrderItem Type="Quote" Id="1345" ReservationId="1002" Currency="EUR" RawBookingPrice="995.00"/>
```

> [!IMPORTANT]
> The 'Currency' and the 'RawBookingPrice' attributes are only returned if the 'Type' is 'Quote'.

> [!NOTE]
> The `ReservationId` is the `Id` of the `Booking` created and should be stored in your system.

### References

#### Reference for the BookingRequest Element (Request)

This is the root element for the `BookingRequest` and must be included in the request.

| Name          | Type    | Required | Description                                                                                                                                                                                                                                                                                        |
|---------------|---------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Customer      | Element | No       | The root element for the `Customer`. **Important** If the `CustomerId` is not provided, the `Customer` must be provided.                                                                                                                                                                           |
| CustomerId    | Integer | No       | The existing `Id` of the `Customer` in the `Client` installation. **Important** If the `Customer` is not provided, the `CustomerId` must be provided.                                                                                                                                              |
| Note          | String  | No       | A note from the ´Customer`                                                                                                                                                                                                                                                                         |
| InternalNote  | String  | No       | An internal note to the `Client` about the `Booking`                                                                                                                                                                                                                                               |
| BookingOption | Element | Yes      | The root element for the `BookingOption`.                                                                                                                                                                                                                                                          |
| Items         | Element | No       | An array of `Item` to be placed on the `Booking`                                                                                                                                                                                                                                                   |
| PaymentRates  | Element | No       | An array of `PaymentRate` to be placed on the `Booking`. There is a **hard** limit of maximum two `PaymentRates` and the `DueDate` must be before or on the date of arrival. **Important** Normally this is handled automatically by the system, but the `ApiKey` can be configured to allow this. |
| Payment       | Element | No       | The root element for a payment card `Payment` to be applied to the `Booking`. **Note** This is for `Client` website integration only, as it requires an `AUTHORIZED` payment from their provider to function!                                                                                      |
| AffiliateId   | Integer | No       | The `Id` of the affiliate that should be associated with the `Booking`. By default the `AffiliateId` configured for the `ApiKey` will be used, but if the configuration allows it, this can be overridden.                                                                                         |   

#### Reference for the Customer Element (Request)

This is the root element for the `Customer`. Fields mut be provided as attributes.

| Name              | Type    | Required | Description                                         |
|-------------------|---------|----------|-----------------------------------------------------|
| FirstName         | String  | Yes      | The first name of the customer                      |
| LastName          | String  | Yes      | The last name of the customer                       |
| Email             | String  | No       | The email of the customer                           |
| IsoCountry        | String  | No       | The ISO 3166-1 alpha-2 country code of the customer |
| PostalCode        | String  | No       | The postal code of the customer                     |
| Address           | String  | No       | The address of the customer                         |
| City              | String  | No       | The city of the customer                            |
| CustomFieldValues | Element | No       | An array of `CustomFieldValue`                      |

#### Reference for the CustomFieldValue Element (Request)

This is the root element for the `CustomFieldValue`. Fields must be provided as attributes.

| Name          | Type   | Required | Description                   |
|---------------|--------|----------|-------------------------------|
| CustomFieldId | String | Yes      | The `Id` of the custom field  |
| Value         | String | Yes      | The value of the custom field |

#### Reference for the BookingOption Element (Request)

This is the root element for the `BookingOption`. This is the actual `Booking` to be created. Fields must be provided as
attributes.

| Name              | Type    | Required | Description                                                    |
|-------------------|---------|----------|----------------------------------------------------------------|
| LodgingId         | Integer | Yes      | The `Id` of the lodging in the `Client` installation           |
| LodgingUnitTypeId | Integer | Yes      | The `Id` of the lodging unit type in the `Client` installation |
| LanguageId        | Integer | Yes      | The `Id` of the language in the `Client` installation          |
| Arrival           | Date    | Yes      | The date of arrival                                            |
| Departure         | Date    | Yes      | The date of departure                                          |
| Duration          | Integer | Yes      | The length of the stay in days                                 |
| Adults            | Integer | Yes      | The number of adults                                           |
| Children          | Integer | No       | The number of children (if omitted the system assumes 0)       |
| Infants           | Integer | No       | The number of infants (if omitted the system assumes 0)        |
| Pets              | Integer | No       | The number of pets (if omitted the system assumes 0)           |

#### Reference for the Items Element (Request)

This is the root element for the `Items`. Fields must be provided as attributes.

| Name     | Type    | Required | Description                                         |
|----------|---------|----------|-----------------------------------------------------|
| ItemId   | Integer | Yes      | The `Id` of the `Item` in the `Client` installation |
| Quantity | Integer | Yes      | The quantity of the `Item`                          |

#### Reference for the PaymentRates Element (Request)

This is the root element for the `PaymentRates`. Fields must be provided as attributes.

| Name    | Type    | Required | Description                       |
|---------|---------|----------|-----------------------------------|
| Amount  | Decimal | Yes      | The amount of the `PaymentRate`   |
| DueDate | Date    | Yes      | The due date of the `PaymentRate` |

#### Reference for the Payment Element (Request)

This is the root element for the `Payment`. Fields must be provided as attributes.

| Name          | Type    | Required | Description                  |
|---------------|---------|----------|------------------------------|
| Id            | String  | Yes      | The `Id` of the payment.     |
| TransactionId | String  | Yes      | The `Id` of the transaction. |
| Status        | String  | Yes      | The status of the payment.   |
| Amount        | Decimal | Yes      | The amount of the payment.   |
| Currency      | String  | Yes      | The currency of the payment. |
| Fee           | Decimal | Yes      | The fee of the payment.      |

#### Reference for the OrderItem Element (Response)

| Name            | Type    | Description                                                                                                                                                                                              |
|-----------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Type            | String  | The type of the order item created. It can be `Option`, `Quote`, `Invoice` (see definitions below)                                                                                                       |
| Id              | Integer | The `Id` of the order item created and associated with the reservation.                                                                                                                                  |
| ReservationId   | Integer | The `Id` of the reservation created.                                                                                                                                                                     |
| Currency        | String  | The currency of the order item created. **Important** This is only returned if the `Type` is `Quote`                                                                                                     |
| RawBookingPrice | Decimal | The raw rental price of the `Booking` created. The raw rental price is the value of the `Booking` subtracted any hidden and included items  **Important** This is only returned if the `Type` is `Quote` |

**Understanding the `Type` of an `OrderItem`**

* **Option**: When the `OrderItem` has this type, it means that the `Booking` created was a `Probability` or a
  `ProbabilityAsBooking`. It's a forehand booking and the price is not guaranteed. It will later be converted to a
  `Quote` by the `Client` and by then the `Guest` can accept or decline the booking.
* **Quote**: When the `OrderItem` has this type, it means that the `Booking` was created, but no payment has been made
  yet.
* **Invoice**: When the `OrderItem` has this type, it means that the `Booking` was created and full payment or partial
  payment has been made.
