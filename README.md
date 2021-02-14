# MPGS Integration Guides and Samples
Mastercard Payment Gateway Services integration guide and API samples covering various transactions scenarios.

## Disclaimer
The following <ins>unofficial</ins> guide contains a simplified explanation of MPGS. This guide is by no means an exhaustive one and only represents a scenario where the merchant is enabled to use some of the most common features of the Gateway. Additional features may need to be configured separately. Samples are examples for reference only, while configuring your actual live merchants on the platform, always consider the risk and financial impact, as well as your policies and scheme and local rule and regulation. Follow this guide at your own responsibility, and make sure to read [the full guide here](https://ap-gateway.mastercard.com/api/documentation/integrationGuidelines/index.html?locale=en_US).

## Content
1. [Comparison of Integration Methods](#Comparison-of-Integration-Methods)
2. [Hosted Checkout Integration Method](#Hosted-Checkout-Integration-Method)
3. [Hosted Session Integration Method](#Hosted-Session-Integration-Method)
4. [Direct Integration Method](#Direct-Integration-Method)
5. [Mobile SDKs](#Mobile-SDKs)
6. [Pay with Token](#Pay-with-Token)


## Comparison of Integration Methods
| Hosted Checkout                                                  | Hosted Session                                                                            | Direct                                                                                    |
|------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| Mastercard Hosts the full payment page, you just redirect to it. | Card input fields directly linked to Mastercard, you host and customize the payment page. | You host the full system and have full control of it.                                     |
| No PCI-DSS certificate required.                                 | No PCI-DSS certificate required.                                                          | PCI-DSS certificate required.                                                             |
| Control only logo and merchant name and details branding.        | Full branding control.                                                                    | Full branding and payment experience control.                                             |
| Fastest and simplest to integrate                                | Developing payment page.                                                                  | Developing payment page and more API calls.                                               |
| Rendered via Webview                                             | Rendered via Webview                                                                      | Webview or native experience on any platform.                                             |
| 2 API calls                                                      | 3 API calls for a typical transaction                                                     | Minimum 3 API calls                                                                       |
| Unable to access card information                                | Able to access masked card information (first 6 and last 4 digits).                       | Able to access full card information.                                                     |
| Recommended for Merchants in a hurry                             | Recommended for merchants that have time for development but are not PCI-Compliant        | Recommended for merchant that have time for development and are PCI-Compliant Saving Card |


## Hosted Checkout Integration Method
[Short guide for the Hosted Checkout Integration method can be found here.](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/hosted-checkout.md)

## Hosted Session Integration Method
[Short guide for the Hosted Session Integration method can be found here.](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/hosted-session.md)

## Direct Integration Method
[Short guide for the Direct Integration method can be found here.](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/direct.md)

## Mobile SDKs
- [IOS SDK](https://github.com/Mastercard-Gateway/gateway-ios-sdk)
- [Android SDK](https://github.com/Mastercard-Gateway/gateway-android-sdk)

## Pay with Token
[Short guide for how to pay using a token can be found here.](https://github.com/Mastercard-MEA/MPGS-Integration-Guides-and-Samples/blob/main/docs/pay-with-token.md)

