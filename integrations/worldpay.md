# Worldpay Integration

This document details how to connect the [Worldpay](https://worldpay.com) payment gateway to accept payments and how our integration works.

## Capabilities

The Worldpay payment gateway on Invoiced supports the following features:

Capability | Supported
-----------|------------
[Credit card payments](/resources/docs/payments/card) | &#10003;
[ACH payments](/resources/docs/payments/ach) | &#10003;
Vaulting cards | &#10003;
Vaulting bank accounts | &#10003;
[AutoPay](/resources/docs/payments/autopay) | &#10003;
Level 3 Processing | &#10003;
Apple Pay | &mdash;
Multi-currency | USD only
Refunds | &#10003;

## Setup

Connecting Worldpay is a straightforward process. Follow these steps to start accepting payments through Worldpay in minutes. These steps assume you already have a Worldpay account.

1. From the Invoiced dashboard go to **Settings** &rarr; **Payments**.

2. Click **Setup** on the payment method you want to accept.

3. Select **Worldpay** as the payment gateway.

4. Click **Connect to Worldpay**.

5. Enter in your Worldpay **Account ID**, **Sub ID**, and **Merchant PIN** and click **Save**. Then click **Enable** and the payment method you selected should be enabled.

## Client Workflow

### Credit Cards

Paying with credit or debit card is fairly straightforward for customers. They simply enter in their cardholder information and click **Pay**. We give receipts to your customers after a successful payment.

[![Pay Invoice with Credit Card](/docs/img/pay-invoice-credit-card.png)](/docs/img/pay-invoice-credit-card.png)

### ACH

Customers can pay with ACH just as easily as with credit cards. The key difference is that ACH payments will take several business days to clear. When customers are on a payment form they will select ACH as the payment information and then enter in their bank account and routing number.

[![Pay Invoice with ACH](/docs/img/pay-invoice-ach.png)](/docs/img/pay-invoice-ach.png)

## Support

If you have any questions regarding your Worldpay merchant account you can call support at 1-800-846-4472.