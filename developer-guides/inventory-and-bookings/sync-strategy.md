# Sync Strategy

Our recommendation is to fetch the `Lodgings` and the `BookingOptions` once every night to keep your data in sync.

> [!IMPORTANT]
> Please refer the [terminology document](terminology.md) for a detailed explanation of the terminology used in this document.

## Once Every Day

Fetch the following data once per day between midnight and 06:00 (danish time):

* Fetch the `Items` for each language you need
* Fetch the `Lodgings` for each language you need
* Fetch the `BookingOptions` for person combinations and currencies you need (currencies is tied to languages). Follow
  the pagination to get all the `BookingOptions`.

> [!TIP]
> Often the prices of the `BookingOption` doesn't change with the number of persons.
> You can make one import with *adu=1* and use this as the price for all the person combinations allowed in the
`Lodging`.
> Then make another import with *adu=2* and only update the prices that has changed compared to *adu=1*. This will save
> you a lot of time and will almost always reflect the prices correctly. If the `Lodging` allows pets, you should also
> make the query with *pet=1* and *pet=0* to get the prices with and without pets.

> [!IMPORTANT]
> Ask the `Client` if there are any special rules for their prices you need to account for in the integration with that
`Client`, and implement them accordingly.

## Maximum Once Every Half Hour

* Fetch the `Calendars` to keep the availability of the `Lodgings` in sync

## Webhooks

Webhooks is an extra module for some `Clients`. If the `Client` has this module, you can use it to get near real-time updates.

* Have the `Client` set up some webhooks for availability changes and price changes.

## On Checkout

Check the availability of the `BookingOption` before creating the `Booking`

When the `Guest` is entering the checkout flow on your website, make sure to call the system to ensure both
`Availability` and the correct prices for the `Booking` and the `Items`.

> [!IMPORTANT]
> The specific `BookingOption` contains the latest price and discount information along with the available items that
> can be added to the booking.
> All prices of the `Items` are calculated based on the number of persons and pets specified in the request
`BookingOption`.
