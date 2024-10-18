# Getting Started

> [!NOTE]
> For detailed information about the API, please refer to the API documentation.
> [More information here](../../README.md)

> [!IMPORTANT]
> Please refer the [terminology document](terminology.md) for a detailed explanation of the terminology used in this document.

## Basic Information About the Api

This document provides general information about the `Inventory` and the `Booking` APIs. 

It includes information about `Authentication`, the `User-Agent`, the `Versioning`, and the `Rate Limiting`.

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

## Learn More

* [How to Consume the Inventory Api](how-to-consume-the-inventory-api.md)
* [How to Create Bookings](how-to-create-bookings.md)
* [Sync Strategy](sync-strategy.md)
