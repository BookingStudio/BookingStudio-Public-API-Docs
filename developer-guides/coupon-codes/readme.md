# Coupon codes API

This API is used to administer (CRUD) coupon codes and its groups.

## Coupon code redemption

The redemption process starts by reserve a coupon code for redemption against a bookingoption. 
The actual redemption is done as a part of the booking request in legacy v1 API where the reservation token of the coupon code is applied.

### Sequence diagram of booking flow using payment, coupon code, gift cards 
``` mermaid
sequenceDiagram
    actor c as Customer
    box Website provider
    participant w as Website
    participant cl as API client
    end
    box Bookingstudio
    participant v1 as /rest/v1
    participant ma as /api
    end
    participant pp as Payment provider
    c->>w: bookingOptions:=Search for available bookings
    w->>cl: findAvailableBookingOptions
    cl->>+v1: bookingOption:=/rest/v1/bookingoptions ¹
    cl-->>w: bookingOption
    c->>w: bookingOption:=Choose an available booking
    opt with coupon code redemption
    c->>w: Apply coupon code(couponCode)
    alt couponCode valid on bookingOption
    cl->>+ma: couponCodeReservationToken:=/api/admin/coupon-codes/redemptions/reserved (couponCode, bookingOption, reservationTimeInSeconds)²
    cl-->>w: couponCodeReservationToken
    end
    end
    opt with gift card redemption
    loop foreach gift card code
    c->>w: applyGiftCardCode(giftcardCode)
    w-->>cl: giftcardCode
    cl->>ma: validatedGiftCard:=/api/admin/gift-cards/codes/validate (giftcardCode) ³
    alt validatedGiftCard is valid
    cl->>cl: giftCards = [validatedGiftCard, giftCards ..]
    end
    cl-->>w: giftCards
    end
    end
    opt with Payment
    w->>cl: payRates:=getPayRates(bookingOption)
    cl->>+v1: payRates:=/v1/payrates.xml (bookingOption) ⁴
    cl-->>w: payRates
    w->>w: paymentAmount:=calculatePaymentAmount(payRates)
    alt with gift card redemption
    loop giftcards giftCard
    w->>w: paymentAmount:=paymentAmount - giftCard.amount
    end
    end
    w-->>c: showConfirmation(paymentAmount, couponCode, giftCards)
    c->>w: confirm(paymentAmount, couponCode, giftCards)
    w->>cl: confirm(paymentAmount, couponCode, giftCards)
    cl->>+pp: paymentAuthorizationToken:=authorizePayment (paymentAmount, couponCode, giftcards)
    end 
    cl->>+v1: (reservationId, orderItemId):=/rest/v1/bookings/create.xml (bookingOption, couponCodeReservationToken, paymentAuthorizationToken, paymentAuthorizationAmount) ⁵
    v1->>-c: quote 
    alt if with gift card redemption choosen
    loop for all giftcards giftcard
    cl->>+ma: /api/admin/gift-cards/apply (giftcard) ⁶
    ma-->>c: paymentNotification(giftcard)
    end
    end
```

### API documentation
> [!NOTE] 
Links point to videodemo api which should be exchanged to actual client FQDN

¹ [/rest/v1/bookingoptions](https://videodemo-api.bookingstudio.dk/rest/v1/docs.html#resource-bookingoptions{target=_blank})<br/>
² [/api/admin/coupon-codes/redemptions/reserved](https://videodemo-api.bookingstudio.dk/api/swagger/index.html#/Coupon%20Codes/ReserveCouponCodeForRedemption{target=_blank})<br/>
³ [/api/admin/gift-cards/codes/validate](https://videodemo-api.bookingstudio.dk/api/swagger/index.html#/Gift%20cards/ValidateGiftCardCodes{target=_blank})<br/>
⁴ [/v1/payrates.xml](https://videodemo-api.bookingstudio.dk/rest/v1/docs.html#resource-bookingoptions{target=_blank})<br/>
⁵ [/rest/v1/bookings/create.xml](https://videodemo-api.bookingstudio.dk/rest/v1/docs.html#resource-bookings{target=_blank})<br/>
⁶ [/api/admin/gift-cards/apply](https://videodemo-api.bookingstudio.dk/api/swagger/index.html#/Gift%20cards/ApplyGiftCardCodes{target=_blank})<br/>