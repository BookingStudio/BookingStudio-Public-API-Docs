# Terminology

This is a list of terms that are used in the BookingStudio API documentation for inventory and bookings.

* **Client**: A client is the company that has the BookingStudio account and owns the data in the `Endpoint`.
* **Partner**: A partner is a company that consumes the `Client` API. The `Client` provides the `Partner` with an API
  key to access the `Client` API.
* **Endpoint**: An endpoint is a specific `Uri` to the `Client` API.
* **Lodging**: A `Lodging` is a place where a guest can stay. It can be a house, an apartment, a cottage, etc.
* **BookingOption**: A `BookingOption` is a specific offer for a `Lodging` for a specific stay. It includes information
  about the number of persons, the price, the arrival and departure, etc.
* **Calendar**: A `Calendar` is a list of all the available dates for a `Lodging`. It includes information about the
  availability and the season.
* **Facility**: A `Facility` is an amenity or a service that a lodging offers. It can be a swimming pool, a sauna, a
  parking space, allowed pets, etc. Facilities are specific to the `Client` and can be different from one `Client` to
  another.
* **MasterFacility**: A `MasterFacility` is a BookingStudio identity of a `Facility`. Master facilities are the same
  across all `Clients`. The `Client` needs to map their facilities to the master facilities in order to provide the
  correct information to the `Partner`.
* **Item**: An `Item`, also called `Extra`, is an additional service that a guest can book. It can be a cleaning
  service, a bedlinen or basically anything that can be added to a booking. Some items are mandatory and some are
  optional.
* **Season**: A `Season` is a range of days with the same season code within a seasonal year. Each `Lodging` has a base
  price for each season for each seasonal year.
* **Currency**: Represent a three letter monetary code. For example, the Euro (EUR) and the Danish Krone (DKK).
* **Language**: Represents a `Language` that is used to communicate with guests. It's used for translations of `Lodging`
  descriptions, `Facility` name, `Item` names and also `Bookings`. **Important** Not all languages are available for all
  `Clients`.