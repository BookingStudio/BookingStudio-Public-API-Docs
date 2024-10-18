The BookingStudio API
=====================

Welcome to the BookingStudio API. This documentation if for the latest BookingStudio API, not our legacy APIs, they have their own documentation. Read more about our legacy APIs in the Legacy API section.

Root Url
--------

All URLs start with **`https://{customerId}-api.bookingstudio.dk`**. URLs are HTTPS only. The {customerId} should be replaced with the ID of the customer!

Legacy API
----------

The BookingStudio Legacy APIs is hosted on the same domain as the BookingStudio API and is located on the following roots:

* **`https://{customerId}-api.bookingstudio.dk/rest/v1`**
* **`https://{customerId}-api.bookingstudio.dk/rest/v1admin`**
* **`https://{customerId}-api.bookingstudio.dk/data/`**

You can find the documentation to the BookingStudio Legacy APIs here:

* **`https://{customerId}-api.bookingstudio.dk/rest/v1/docs.html`**
* **`https://{customerId}-api.bookingstudio.dk/rest/v1admin/docs.html`**
* **`https://{customerId}-api.bookingstudio.dk/data/swagger`**

Please remember to replace the {clientId} with the actual ID of the client.

Authentication
--------------

To authenticate you must use tht X-API-KEY header with your API-KEY.

Routes and parameters
---------
We use kebab-style-lower-case-urls with parameters in camelCase

JSON only
---------

We use JSON for all API data. The style is no root element and camelCase object keys. 

Pagination
----------

Most collection APIs paginate their results. The number of requests that'll appear on each page is variable. The BookingStudio API follows the [RFC5988 convention](https://tools.ietf.org/html/rfc5988) of using the `Link` header to provide URLs for the `next` page. Follow this convention to retrieve the next page of data. Please don't build the pagination URLs yourself!

Here's an example response header from requesting the second page of reservations for the lodging with id 5:

```
Link: <https://demo-api.bookingstudio.dk/lodgings/5/reservations?from=2023-01-01&to=2023-02-01&page=2>; rel="next"
```

If the `Link` header is blank, that's the last page. The BookingStudio API also provides the `X-Total-Count` header, which displays the total number of resources in the collection you are fetching.


Using HTTP caching
------------------

You must use HTTP freshness headers to speed up your application and lighten the load on our servers. Most API responses will include an `ETag` or `Last-Modified` header. When you first request a resource, store these values. On subsequent requests, submit them back to us as `If-None-Match` and `If-Modified-Since`, respectively. If the resource hasn't changed since your last request, you'll get a `304 Not Modified` response with an empty body, saving you the time and bandwidth of sending something you already have.


Handling errors
---------------

API clients must expect and gracefully handle transient server errors and rate limits. We recommend baking graceful 5xx and 429 retries into your integration from the beginning so errors are handled automatically.

### 403 Forbidden

API requests may return 403 due insufficient permissions. Please contact the account owner to obtain sufficient permissions.
Do not automatically retry these requests.

### 404 Not Found

API requests may return 404 due to deleted content. Detect these conditions to give your users a clear explanation about why they can't connect to BookingStudio. Do not automatically retry these requests.

### Rate limiting (429 Too Many Requests)

We return a [429 Too Many Requests](http://tools.ietf.org/html/draft-nottingham-http-new-status-02#section-4) response when you've exceeded a rate limit. Consult the `Retry-After` response header to determine how long to wait (in seconds) before retrying the request.

Plan ahead to gracefully handle the failure modes that API backpressure will exert on your integration. Multiple rate limits are in effect, e.g. for GET vs POST requests and per-second/hour/day limits, and they're adjusted dynamically, so responding to them dynamically is essential, particularly at high traffic levels.

For a sense of scale, the first rate-limit you'll commonly encounter is currently a token bucket with 60 requests being filled with 10 requests per 10 seconds per API Key.

> [!NOTE]
> Some endpoints have different rate limits. Please consult the documentation for the specific endpoint you are using.

### 5xx Server Errors

If BookingStudio is having trouble, you will get a response with a 5xx status code indicating a server error. 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout) may be retried with [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff).

Read more about [5xx server errors](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#5xx_Server_errors)

## Developer Guides

* [Developer Guide - Inventory and Bookings](developer-guides/inventory-and-bookings/index.md)

