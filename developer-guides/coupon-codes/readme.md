#Coupon Codes API

This API is used to admin and redeem coupon codes. The whole integration spans both minimal api for CRUD and reserve of coupon codes. Legacy v1 API is used to make a booking using a reserved coupon code.

# Sequence diagram of booking flow using payment, coupon code, gift cards 
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
    cl->>+v1: bookingOption:=/rest/v1/bookingoptions
    cl-->>w: bookingOption
    c->>w: bookingOption:=Choose an available booking
    opt with coupon code redemption
    c->>w: Apply coupon code(couponCode)
    alt couponCode valid on bookingOption
    cl->>+ma: couponCodeReservationToken:=/api/admin/coupon-codes/redemptions/reserved (couponCode, bookingOption, reservationTimeInSeconds)
    cl-->>w: couponCodeReservationToken
    end
    end
    opt with gift card redemption
    loop foreach gift card code
    c->>w: applyGiftCardCode(giftcardCode)
    w-->>cl: giftcardCode
    cl->>ma: validatedGiftCard:=/api/admin/gift-cards/codes/validate (giftcardCode)
    alt validatedGiftCard is valid
    cl->>cl: giftCards = [validatedGiftCard, giftCards ..]
    end
    cl-->>w: giftCards
    end
    end
    opt with Payment
    w->>cl: payRates:=getPayRates(bookingOption)
    cl->>+v1: payRates:=/v1/payrates.xml (bookingOption)
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
    cl->>+v1: (reservationId, orderItemId):=/rest/v1/bookings/create.xml (bookingOption, couponCodeReservationToken, paymentAuthorizationToken, paymentAuthorizationAmount)
    v1->>-c: quote 
    alt if with gift card redemption choosen
    loop for all giftcards giftcard
    cl->>+ma: /api/admin/gift-cards/apply (giftcard)
    ma-->>c: paymentNotification(giftcard)
    end
    end
```

[^1]:[/rest/v1/bookingoptions](https://videodemo-api.bookingstudio.dk/rest/v1/docs.html#resource-bookingoptions)
[^2]:[/api/admin/coupon-codes/redemptions/reserved](https://videodemo-api.bookingstudio.dk/api/swagger/index.html#/Coupon%20Codes/ReserveCouponCodeForRedemption)
[^3]:[/api/admin/gift-cards/codes/validate](https://videodemo-api.bookingstudio.dk/api/swagger/index.html#/Gift%20cards/ValidateGiftCardCodes)
[^4]:[/v1/payrates.xml](https://videodemo-api.bookingstudio.dk/rest/v1/docs.html#resource-bookingoptions)
[^5]:[/rest/v1/bookings/create.xml](https://videodemo-api.bookingstudio.dk/rest/v1/docs.html#resource-bookings)
[^6]:[/api/admin/gift-cards/apply](https://videodemo-api.bookingstudio.dk/api/swagger/index.html#/Gift%20cards/ApplyGiftCardCodes)