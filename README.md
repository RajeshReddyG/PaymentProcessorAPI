# PaymentProcessorAPI
The API Controller has function that responds for POST request which is useful for processing the payment, inserting the state of the payment and transaction details in the database table by receiving the credit card details.

## Problem Statement

Write a WebAPI with only 1 method called “ProcessPayment” that receives a request like this:
- CreditCardNumber (mandatory, string, it should be a valid CCN)
- CardHolder: (mandatory, string)
- ExpirationDate (mandatory, DateTime, it cannot be in the past)
- SecurityCode (optional, string, 3 digits)
- Amount (mandatoy decimal, positive amount)

Write a Payment domain/entity with the same properties as the request and a second entity to store
the payment state (pending, processed, failed). Use Entity framework code first approach, write
entity configurations and generate the migrations.
The response of this method should be 1 of the followings based on
- Payment is processed: 200 OK
- The request is invalid: 400 bad request
- Any error: 500 internal server error
- The request should be validated before processed.

The payment could be processed using different payment providers (external services) called:
- IExpensivePaymentGateway or
- ICheapPaymentGateway.

The payment gateway that should be used to process each payment follows the next set of business
rules
1. If the amount to be paid is less than £20, use ICheapPaymentGateway.
2. If the amount to be paid is £21-500, use IExpensivePaymentGateway if available. Otherwise, retry
only once with ICheapPaymentGateway.
3. If the amount is > £500, try only PremiumPaymentService and retry up to 3 times in case payment
does not get processed
4. Store/update the payment and payment state entities created previously once the processing is
completed.

## DB Creation
* Set db connection string in appsettings.json
* Open Package Manager Console
* Set project to Entity.
* Set Startup project to WebApi.
* Run command:
```bash
update-database
```
This should set up database according to the Entity Migrations. Once the database and tables are created, We are all set to run the application.
## Example of POSTMAN call
<ins> Note: </ins> 44374 is the port number
### POST
## Example 1
Call URL https://localhost:44374/api/pay with POST with JSON:

```json
{
    "creditCardNumber": "5555 5555 5555 4444",
    "cardHolder": "Rajesh G",
    "expirationDate": "2021-06-02",
    "securityCode": "123",
    "amount": 600
}
```
**Response:**
```json
{
    "isProcessed": true,
    "paymentState": {
        "paymentState": 1,
        "paymentStateDate": "2021-05-17T03:12:16.861417Z"
    }
}
```
### POST
## Example 2
Call URL https://localhost:44374/api/pay with POST with JSON:

```json
{
    "creditCardNumber": "4012 8888 8888 1881",
    "cardHolder": "Rajesh G",
    "expirationDate": "2021-06-02",
    "securityCode": "125",
    "amount": 15
}
```
The post call should return a response like this if it is **successful**:
```json
{
    "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
    "title": "An error occured while processing your request.",
    "status": 500,
    "traceId": "|c699ec21-4e0dd363dcff01cb."
}
```
If it is **faild**:
```json
{
    "error": "Payment could not be processed"
}
```
### GET
Call URL https://localhost:44374/api/pay with GET  
This Should return the following response if the application is online
```
Payment Processor is online
```
