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
