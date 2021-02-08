# Hosted Session Integration Guide & Samples
A quick guide with samples using the Hosted Session method in Mastercard Gateway. This is the second easiest and fastest method to start accepting payments. It is also fully PCI-Complied as you don't have to to handle any payment details. This method allows the gateway to collect card information using hosted input fields, so that you won't have to worry about directly handling sensitive card information in your system.

## Disclaimer
The following guide contains an explanation of using the Hosted Session Integration Model. This guide is by no means an exhaustive one and only represents a scenario where the merchant is enabled to use some of the most common features of the Gateway. Additional features may need to be configured separately.
Below samples are examples for reference only, while configuring your actual live merchants on the platform, always consider the risk and financial impact, as well as your policies and scheme and local rule and regulation. Follow this guide at your own responsibility. Make sure to read [Full References](#Full-Official-References)

## Who is this method best for?
Merchants that are not PCI-Complied and want to fully customize and host their own page, as opposed to redirecting the user to it.

## What do i need to use this method?
- You need to be onboarded with a Payment Gateway Provider that support Mastercard's Gateway, and be issued a **"Merchant ID"** and Gateway Credentials which you need to integrate. If you are not sure who provides Mastercard Gateway in your region, and you would like to be connected with one directly, reach out to customer_support@mastercard.com and ask them to connect you with a Mastercard Gateway (MPGS) provider for your country.
- Your Payment Gateway provider will also provide with your merchant portal credentials and the gateway URL, Which from hereinafter we will refer to as <Bank URL>.

## What technical knowledge i should have before starting?
- You need to be familiar with how APIs work (e.g. POST, GET, PUT ..etc) and how to use them.
- You need to know how to implment API requests in whatever programming langauage you are planning to use.
- Javascript.
- HTML. 
- Basic understanding of payments (Authorization Response, Transaction Types, etc).

## First Step: Collect the Card Information Into a Payment Session

## Second Step: Initiate 3DS Authentication

## Third Step: Authenticate Payer with 3DS

## Fourth Step: Make A Payment Transaction

## Bonus: Saving the Card Infomration for Express Checkout
You can tokenize the card information and save the token to provide allow your customers to pay using a previously saved card. This method does not store the card information directly on your system, but saves a token referncing the card instead. This way, you save yourself PCI-Compliance costs.

### 1. Tokenzing the Card
To tokenize the card, send the following request after the card information was collected into a session as in Step One, or after the Payment is Complete in :
**Request:**
```
POST  https://{{MsoUrl}}/api/rest/version/{{ApiVersion}}/merchant/{{MerchantId}}/token
```
```js
{
	"session": {
		"id": "{{SessionId}}" // This is the session.id value that was generated in the first step 
	},
	"sourceOfFunds": {
		"type": "CARD"
	}
}
```
**Response:**
```js
{
    "repositoryId": "<Merchant ID>",
    "response": {
        "gatewayCode": "BASIC_VERIFICATION_SUCCESSFUL"
    },
    "result": "SUCCESS",
    "sourceOfFunds": {
        "provided": {
            "card": {
                "brand": "MASTERCARD",
                "expiry": "0521",
                "fundingMethod": "CREDIT",
                "issuer": "AFRILAND FIRST BANK",
                "nameOnCard": "Nawaf",
                "number": "512345xxxxxx0008",
                "scheme": "MASTERCARD"
            }
        },
        "type": "CARD"
    },
    "status": "VALID",
    "token": "5123458154060008", // This is the token number, which references the saved card. This is what you would store in your system for your future payment.
    "usage": {
        "lastUpdated": "2021-01-29T09:09:50.837Z",
        "lastUpdatedBy": "<Merchant ID>",
        "lastUsed": "2020-10-27T03:46:16.089Z"
    },
    "verificationStrategy": "BASIC"
}
```

Take note of the parameter `"token": "5123458154060008"`, this is the token number referenceing the saved card. Store this value in your system for future payments.

### 2. Making a Payment Using Tokenized a Card
You can find the guide for payment using a token here.

## Testing
You can find the official test cards and the guide to use the Gateway Simulator [here](https://ap-gateway.mastercard.com/api/documentation/integrationGuidelines/supportedFeatures/testAndGoLive.html?locale=en_US).

## Full Official References
This guide is unofficial, it is meant to simplify the official guides here:

* [Official Hosted Checkout Guide](https://ap-gateway.mastercard.com/api/documentation/integrationGuidelines/hostedCheckout/integrationModelHostedCheckout.html)
* [Official Checkout.js Library Reference](https://ap-gateway.mastercard.com/api/documentation/apiDocumentation/checkout/version/latest/api.html?locale=en_US)
* [Official API Reference](https://ap-gateway.mastercard.com/api/documentation/apiDocumentation/rest-json/version/latest/api.html?locale=en_US)

For any issues with your integration, you need to reach out to your payment provider. 
