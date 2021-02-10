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
On your front-end, import the Session.js library into your to create the payment input HTML page. You can copy and paste the demo code below in an empty HTML file for testing.
```HTML
<html>

<head>
    <!-- INCLUDE SESSION.JS JAVASCRIPT LIBRARY -->
    <script src="https://test-gateway.mastercard.com/form/version/57/merchant/TEST42069/session.js"></script>
    <!-- APPLY CLICK-JACKING STYLING AND HIDE CONTENTS OF THE PAGE -->
    <style id="antiClickjack">
        body {
            display: none !important;
        }
    </style>
</head>

<body>
    <!-- CREATE THE HTML FOR THE PAYMENT PAGE -->
    <div>Please enter your payment details:</div>
    <h3>Credit Card</h3>
    <div>Card Number: <input type="text" id="card-number" class="input-field" title="card number"
            aria-label="enter your card number" value="" tabindex="1" readonly></div>
    <div>Expiry Month:<input type="text" id="expiry-month" class="input-field" title="expiry month"
            aria-label="two digit expiry month" value="" tabindex="2" readonly></div>
    <div>Expiry Year:<input type="text" id="expiry-year" class="input-field" title="expiry year"
            aria-label="two digit expiry year" value="" tabindex="3" readonly></div>
    <div>Security Code:<input type="text" id="security-code" class="input-field" title="security code"
            aria-label="three digit CCV security code" value="" tabindex="4" readonly></div>
    <div>Cardholder Name:<input type="text" id="cardholder-name" class="input-field" title="cardholder name"
            aria-label="enter name on card" value="" tabindex="5" readonly></div>
    <div><button id="payButton" onclick="pay('card');">Pay Now</button></div>

    <!-- JAVASCRIPT FRAME-BREAKER CODE TO PROVIDE PROTECTION AGAINST IFRAME CLICK-JACKING -->
    <script type="text/javascript">
        if (self === top) {
            var antiClickjack = document.getElementById("antiClickjack");
            antiClickjack.parentNode.removeChild(antiClickjack);
        } else {
            top.location = self.location;
        }

        PaymentSession.configure({
            fields: {
                // Attach hosted fields to your payment page
                card: {
                    number: "#card-number",
                    securityCode: "#security-code",
                    expiryMonth: "#expiry-month",
                    expiryYear: "#expiry-year",
                    nameOnCard: "#cardholder-name"
                },
            },
            frameEmbeddingMitigation: ["javascript"],
            callbacks: {
                initialized: function (response) {
                    // HANDLE INITIALIZATION RESPONSE
                    if (response.status === "ok") {
                        console.log("Ready for payment")
                    }
                },
                formSessionUpdate: function (response) {
                    // HANDLE RESPONSE FOR UPDATE SESSION
                    if (response.status) {
                        if ("ok" == response.status) {
                            console.log("Session updated with data: " + response.session.id);
                            console.log("Card Entered: " + response.sourceOfFunds.provided.card.number)
                            console.log("Card Type: " + response.sourceOfFunds.provided.card.scheme)

                            //check if the security code was provided by the user
                            if (response.sourceOfFunds.provided.card.securityCode) {
                                console.log("Security code was provided.");
                            }

                            //check if the user entered a MasterCard credit card
                            if (response.sourceOfFunds.provided.card.scheme == 'MASTERCARD') {
                                console.log("The user entered a MasterCard credit card.")
                            }
                        } else if ("fields_in_error" == response.status) {
                            // HANDLE FIELD ERROR RESPONSES
                            console.log("Session update failed with field errors.");
                            if (response.errors.cardNumber) {
                                console.log("Card number invalid or missing.");
                            }
                            if (response.errors.expiryYear) {
                                console.log("Expiry year invalid or missing.");
                            }
                            if (response.errors.expiryMonth) {
                                console.log("Expiry month invalid or missing.");
                            }
                            if (response.errors.securityCode) {
                                console.log("Security code invalid.");
                            }
                        } else if ("request_timeout" == response.status) {
                            console.log("Session update failed with request timeout: " + response.errors.message);
                        } else if ("system_error" == response.status) {
                            console.log("Session update failed with system error: " + response.errors.message);
                        }
                    } else {
                        console.log("Session update failed: " + response);
                    }
                }
            },
            interaction: {
                displayControl: {
                    formatCard: "EMBOSSED",
                    invalidFieldCharacters: "REJECT"
                }
            }
        });

        function pay() {
            PaymentSession.updateSessionFromForm('card');
        }
    </script>
</body>

</html>
```



## Second Step: Initiate 3DS Authentication

## Third Step: Authenticate Payer with 3DS

## Fourth Step: Make A Payment Transaction

## Bonus: Saving the Card Infomration for Express Checkout
You can tokenize the card information and save the token to provide allow your customers to pay using a previously saved card. This method does not store the card information directly on your system, but saves a token referncing the card instead. This way, you save yourself PCI-Compliance costs.

### 1. Tokenzing the Card
To tokenize the card, send the following request after the card information was collected into a session as in Step One, or after the Payment is Complete in:

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
