# Hosted Checkout Integration Guide & Samples
A quick guide with samples using the Hosted Checkout method in Mastercard Gateway. This is the easiest and fastest method to start accepting payments. It is also fully PCI-Complied as you don't have to to handle any payment details. The drawback for this method is that you will have limited control over your payment page look and feel.


## Disclaimer
The following guide contains an explanation of using the Hosted Checkout Integration Model. This guide is by no means an exhaustive one and only represents a scenario where the merchant is enabled to use some of the most common features of the Gateway. Additional features may need to be configured separately.
Below samples are examples for reference only, while configuring your actual live merchants on the platform, always consider the risk and financial impact, as well as your policies and scheme and local rule and regulation. Follow this guide at your own responsibility. Make sure to read [Full References](#Full-Official-References)

## Who is this method best for?
Merchants that would like to start accepting basic payments in the shortest time possible and without much development needed, and don't mind the limited branding on the payment page.

## What do i need to use this method?
- You need to be onboarded with a Payment Gateway Provider that support Mastercard's Gateway, and be issued a **"Merchant ID"** and Gateway Credentials which you need to integrate. If you are not sure who provides Mastercard Gateway in your region, and you would like to be connected with one directly, reach out to customer_support@mastercard.com and ask them to connect you with a Mastercard Gateway (MPGS) provider for your country.
- Your Payment Gateway provider will also provide with your merchant portal credentials and the gateway URL, Which from hereinafter we will refer to as <Bank URL>.

## What technical knowledge i should have before starting?
- You need to be familiar with how APIs work (e.g. POST, GET, PUT ..etc) and how to use them.
- You need to know how to implment API requests in whatever programming langauage you are planning to use.
- Javascript.
- HTML. 
- Basic understanding of payments (Authorization Response, Transaction Types, etc)

## First Step: Create a Checkout Session
Perform the following POST API call:

**Resource URL:**
```
POST  https://<Bank URL>/api/rest/version/<API Version>/merchant/<Merchant ID>/session/
```
**Body:**
```js
{
	"apiOperation": "CREATE_CHECKOUT_SESSION",
	"interaction": {
		"operation": "PURCHASE",	// Other operations available are "AUTHORIZE", "VERIFY", and "NONE".
		"returnUrl": "https://www.yourwebsite.com/someResultPage"	// Page where the customer will be redirected to after payment
	},
	"order": {
		"amount": 1.00,	// The amount for the order
		"currency": "<Currency>",	// The currency in ISO 4217 alpha code, e.g. USD, SGD, AUD, SAR, AED etc..
		"id": "<Order Id>",	// An order is the array of transactions. This is the identifier for this order. Can be Any alphanumerical value you assign.
		"reference": "<Order Reference>",	// This is the reference for this order. Can be Any alphanumerical value you assign.
		"description": "Shoes and flipflops"
	},
	"transaction": {
		"reference": "<Transaction Reference>"	// The transaction reference for this attempt.
	}
}
```
**Authorization:** Basic HTTP authentication as described [Here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization). Provide 'merchant.<your gateway merchant ID>' in the Username portion and your API password in the Password portion. The API Password is obtained from your Merchant Administration portal > Admin > Integration Settings.
  
**Response:**
```js
{
    "merchant": "<Merchant ID>",
    "result": "SUCCESS",
    "session": {
        "id": "SESSION0002561575526H6901242G59",
        "updateStatus": "SUCCESS",
        "version": "0bd6923c01"
    },
    "successIndicator": "2a149e4aed1d4818"
}
```

Take note of the *session.id* generated. Store that value for our next step.

## Second Step: Configure the Payment Page
Now you need to create and configure the payment page on the client side. First, import the *Checkout.js* library into your HTML page:
On your client side, import the Checkout.js library, and reference your callbacks (e.g. data-error) which will be set in the next step:
```HTML
<script src="https://<Bank URL>/checkout/version/<API Version>/checkout.js">
</script>
```

Then, call `Checkout.configure({ .. })` to set how the payment page will appear, the logo it will contain, and the page elements it will have, like the following example:
```js
	Checkout.configure({
                merchant: '<Merchant ID>',  // Your merchant Id
                session: {
                    id: "SESSION0002561575526H6901242G59",   // This is the session.id value that was generated in the first step
                },
                interaction: {
                    merchant: {
                        name: 'Merchant Name',	// This is your merchant name that will be display on the checkout page to your customer, typically it will be a brand name.
                        email: "info@merchant.com",	// The merchant email, the will be displayed on the checkout page.
                        phone: "+9661234567890",	// The merchant phone, the will be displayed on the checkout page.
                        logo: "https://ap-gateway.mastercard.com/api/documentation/resource/footer.png",   // The merchant logo on the checkout page.
                        url: "https://www.mastercard.com",  // the merchant website
                        address: {	// Merchant Address, the will be displayed on the checkout page.
                            line1: 'Jeddah',   		
                            line2: 'Saudi Arabia'    	     
                        }    
                    },
                    displayControl: {
                        billingAddress: "HIDE",		// Prompt the user to enter the billing address. Possible options are "HIDE", "MANDATORY", "OPTIONAL", or "READ_ONLY".
                        customerEmail: "HIDE",		// Prompt the user to enter their email. Possible options are "HIDE", "MANDATORY", "OPTIONAL", or "READ_ONLY".
                        orderSummary: "SHOW",		// Display the order summary to in the checkout page. Possible options are "HIDE", "SHOW", or "SHOW_PARTIAL".
                        paymentConfirmation: "HIDE",	// Prompt the user to confirm the payment before submitting. Possible options are "HIDE", or "SHOW".
                        shipping: "HIDE"		// Display the shipping address to the user. Possible options are "HIDE", or "READ_ONLY".
                    },
                },
            });

```
Then, after you have configured the payment page, display the payment page. There are two modes for displaying the payment page:
- Call `Checkout.showLightbox()` to display the payment page in a modal.
- Call `Checkout.showPaymentPage()` to redirect to redirect to the payment page.

From here, the user will interact with the payment page, submit the payment, and perform 3DS authentication if necessary. If the payment is successful, the customer will be redirected to the url you configured in the First Step. If the payment fails, the customer will stay in the payment page and be asked to try again.

## Step Three: Retrieve the Order Result
After the user enter the card information and click submit, if they payment is successful, the user will be redirected to the URL you specified in the First Step, in `interaction.returnUrl`. When the user reach the result page, retrieve the payment result using the following request:

**Request:**
```
GET  https://<Bank URL>/api/rest/version/<API Version>/merchant/<Merchant ID>/order/<Order Id>
```
**Authorization:** Basic HTTP authentication as described [Here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization). Provide 'merchant.<your gateway merchant ID>' in the Username portion and your API password in the Password portion. The API Password is obtained from your Merchant Administration portal > Admin > Integration Settings.
	
**Response:**
This will return the order status, you can find a sample of the full response [here](https://github.com/MentalN/Mastercard-Gateway-Hosted-Checkout-Sample/blob/main/OrderResponseSample.json). You can find all other possible results [here](https://ap-gateway.mastercard.com/api/documentation/apiDocumentation/rest-json/version/latest/operation/Transaction%3a%20%20Retrieve%20Order.html?locale=en_US). 

To confirm that the order was successfull captured, you need to look at the paramter "status":
```js
"status": "CAPTURED",
"totalAuthorizedAmount": 8000,
"totalCapturedAmount": 8000,
"totalRefundedAmount": 0
```

## Bonus: Saving the Card Infomration for Express Checkout
You can tokenize the card information and save the token to provide allow your customers to pay using a previously saved card. This method does not store the card information directly on your system, but saves a token referncing the card instead. This way, you save yourself PCI-Compliance costs.


### 1. Tokenzing the Card
To tokenize the card, send the following request after the payment was completed successfully:
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
[You can find the guide for payment using a token here.](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/pay-with-token.md)

## Testing
You can find the official test cards and the guide to use the Gateway Simulator [here](https://ap-gateway.mastercard.com/api/documentation/integrationGuidelines/supportedFeatures/testAndGoLive.html?locale=en_US).

## Postman Samples Download
You can download the Postman API collection for this integration method [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/postman/Hosted%20Checkout.postman_collection.json).
You can also find the Postman envionement for the collections [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/postman/MPGS.postman_environment.json).

## Full Official References
This guide is unofficial, it is meant to simplify the official guides here:

* [Official Hosted Checkout Guide](https://ap-gateway.mastercard.com/api/documentation/integrationGuidelines/hostedCheckout/integrationModelHostedCheckout.html)
* [Official Checkout.js Library Reference](https://ap-gateway.mastercard.com/api/documentation/apiDocumentation/checkout/version/latest/api.html?locale=en_US)
* [Official API Reference](https://ap-gateway.mastercard.com/api/documentation/apiDocumentation/rest-json/version/latest/api.html?locale=en_US)

For any issues with your integration, you need to reach out to your payment provider. 
