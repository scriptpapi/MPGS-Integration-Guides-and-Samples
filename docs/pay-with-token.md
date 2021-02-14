# Pay with Token Guide & Samples
A quick guide to pay to pay using a previously generated token.

## Disclaimer
The following guide contains an explanation of using the Direct Integration Model. This guide is by no means an exhaustive one and only represents a scenario where the merchant is enabled to use some of the most common features of the Gateway. Additional features may need to be configured separately.
Below samples are examples for reference only, while configuring your actual live merchants on the platform, always consider the risk and financial impact, as well as your policies and scheme and local rule and regulation. Follow this guide at your own responsibility. Make sure to read [Full References](#Full-Official-References)

## First Step: Generating the Token
To tokenize the card, send the following request after the card information was collected into a payment session:

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
A sample response can be found [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/response-sample/tokenize-api.json).

Take note of the parameter `"token": "5123458154060008"`, this is the token number referenceing the saved card. Store this value in your system for future payments.


## Second Step: Initiate 3DS Authentication
Now we will perform 3DS authentication on the transaction. If 3DS Authentication is not required for you, you can just skip to the fifth step.

**Request:**
```
PUT	https://{{MsoUrl}}/api/rest/version/{{ApiVersion}}/merchant/{{MerchantId}}/order/OrdID_{{AttemptNum}}/transaction/TxnID_{{AttemptNum}}
```
**Body:**
```js
{
	"apiOperation":"INITIATE_AUTHENTICATION",
	"authentication":{ 
		"acceptVersions":"3DS1,3DS2",
	    "channel":"PAYER_BROWSER",
	    "purpose":"PAYMENT_TRANSACTION"
	},
	"correlationId":"test",
	"order":{
		"reference": "OrdRef_{{AttemptNum}}",
    	"currency":"SAR"
	},
	"sourceOfFunds": {
		"token":"{{Token}}"
	},
	"transaction": {
		"reference": "TrxRef_{{AttemptNum}}"
	}
}
```
**Response**
A sample response can be found [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/response-sample/initiate-authentication-api.json).


## Third Step: Authenticate Payer with 3DS
After you have sent the API request for the Second Step, just simply make this request next:

**Request:**
```
PUT	https://{{MsoUrl}}/api/rest/version/{{ApiVersion}}/merchant/{{MerchantId}}/order/OrdID_{{AttemptNum}}/transaction/TxnID_{{AttemptNum}}
```
**Body:**
```js
{
	"apiOperation": "AUTHENTICATE_PAYER",
	"authentication":{
		"redirectResponseUrl":	"https://localhost:8000/"
	},
	"correlationId":"test",
	"device": {
		    "browser": "MOZILLA",
		    "browserDetails": {
				"3DSecureChallengeWindowSize": "FULL_SCREEN",
			    "acceptHeaders": "application/json",
			    "colorDepth": 24,
			    "javaEnabled": true,
			    "language": "en-US",
			    "screenHeight": 640,
			    "screenWidth": 480,
			    "timeZone": 273
		    },
		   "ipAddress": "127.0.0.1"
	},
	"order":{
		"amount":"1.00",
	    	"currency":"SAR"
	},
	"sourceOfFunds": {
		"token":"{{Token}}"
	}
}
```
**Response:**
A sample response can be found [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/response-sample/authenticate-payer-api.json).

An HTML will be returned to the user in the parameter `authentication.redirectHtml`. That HTML will containt the 3DS interaction page. You need to display the contents of that HTML to the user in order for them to complete the authentication. This part of the flow is completely between the cardholder and their issuing bank.


## Fourth Step: Make A Payment Transaction
After the authentication is complete, you can now send the payment request:

**Request:**
```
PUT	https://{{MsoUrl}}/api/rest/version/{{ApiVersion}}/merchant/{{MerchantId}}/order/OrdID_{{AttemptNum}}/transaction/1
```
**Body:**
```js
{
	"apiOperation": "PAY",
	"authentication":{
		"transactionId": "TxnID_{{AttemptNum}}"
	},
	"order": {
		"amount": 1.00,
		"currency": "SAR",
		"reference": "OrdRef_{{AttemptNum}}"
	},
	"transaction": {
		"reference": "TrxRef_{{AttemptNum}}",
		"source": "INTERNET"
    	},
	"sourceOfFunds": {
		"token":"{{Token}}",
		"provided": {
			"card": {
				"securityCode": "100"
			}
		}
	}
}
```
**Response:**
A sample of a successful payment with token response can be found [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/response-sample/pay-with-token-api.json).

A successful payment is only when the acquirerCode for your transaction 

## Postman Samples Download
You can download the Postman API collection for this integration method [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/postman/Token%20Payment%20with%203DS%20Authentication.postman_collection.json).
You can also find the Postman envionement for the collections [here](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/postman/MPGS.postman_environment.json).

## Full Official References
This guide is unofficial, it is meant to simplify the official guides here:
* [Official Tokenization Guide](https://ap-gateway.mastercard.com/api/documentation/integrationGuidelines/supportedFeatures/pickAdditionalFunctionality/tokenization/tokenization.html?locale=en_US)
* [Official API Reference](https://ap-gateway.mastercard.com/api/documentation/apiDocumentation/rest-json/version/latest/api.html?locale=en_US)

For any issues with your integration, you need to reach out to your payment provider. 
