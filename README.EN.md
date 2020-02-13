[**Wersja polska**][ext8]

# PayU module for Magento 2 version 2.3
`The module is issued under GPL license.`

**If you have any questions or if you want to report an error, please contact our support at the address: tech@payu.pl.**

* If you are using Magento version 1.x, please use a [plugin for version 1.x][ext0]
* If you are using Magento version >2.0.6, 2.1, 2.2, please use a [plugin for version >2.0.6, 2.1, 2.2 ][ext7]

## Table of contents

1. [Properties](#properties)
1. [Requirements](#requirements)
1. [Installation](#installation)
1. [Configuration](#configuration)
    * [Parameters](#parameters)
1. [Information about properties](#information-about-properties)
    * [Order of payment methods](#order-of-payment-methods)
    * [Repeat payment](#repeat-payment)
    * [Saving card](#saving-cards)
    * [Conversion](#conversion)


## Properties
PayU payment module adds a PayU payment option to Magento 2. The module works together with Magento 2 version 2.3

The following operations are possible:
* Creation of payment in the PayU system
* Automatic receipt of notifications and change of order status
* Receipt or rejection of payment (in case of switched off automatic receipt)
* Display of payment method and selection of the method on the order summary page
* Payment by card directly on the order summary page
* Remembering of cards and payment with the remembered card
* Repeat payment
* Creation of online refund (full or partial)


The module adds two payment methods:

![methods][img0]
  * **PayU payment** - selection of payment method and redirection a bank or card form
  * **Card payment** - entry of the card number directly on the store's website and payment by card

## Requirements

**Important:** The module works only with the REST API (Checkout) POS, if you don't have an account in the PayU system yet, [**register yourself in the production system**][ext1] or [**in the sandbox system**][ext5]

* PHP compliant with the requirements of the installed version of Magento 2
* PHP extension: [cURL][ext2] and [hash][ext3].

## Installation

#### Using Composer
`composer require payu/magento23-payment-gateway`

#### By copying files to a server
1. Download the latest version of the module from the [GitHub repository][ext4]
1. Unzip the downloaded file
1. Connect to the ftp server and copy the unzipped files to the folder `app/code/PayU/PaymentGateway` of your Magento 2 store. If there is no such folder, create it.

After installation using Composer or copying files from the console's level, run:
   * php bin/magento module:enable PayU_PaymentGateway
   * php bin/magento setup:upgrade
   * php bin/magento setup:di:compile
   * php bin/magento setup:static-content:deploy

## Configuration

1. Go to the administration page of your Magento 2 store [http://adres-sklepu/admin_xxx].
1. Go to **Stores** > **Configuration**.
1. On the **Configuration** page in the menu on the left-hand side, in the section **Sales** choose **Payment Methods**.
1. On the list of available payment methods choose  **PayU** or **PayU - Cards** to configure the plugin's parameters.
1. After changing the parameters click `Save config`.

### Parameters

#### Main parameters

| Parameter | Description |
|---------|-----------|
| Activate the plugin? | Determines whether the payment method will be available in the store on the list of payments. |
| Sandbox mode | Determines whether payments will be performed in PayU (sandbox) test environment. |
| Order of payment methods | Determines the order of the payment methods being displayed (available only for `Płatność PayU`) [more information](#order-of-payment-methods). |

#### Point of sale (POS) parameters

| Parameter | Descripction |
|---------|-----------|
| POS IdD | POS ID from PayU system |
| Second MD5 key | Second MD5 key from PayU system |
| OAuth - client_id | client_id for OAuth protocol from PayU system |
| OAuth - client_secret | client_secret for OAuth from PayU system |

#### POS parameters - Test mode (Sandbox)
Available when the parameter `Test Mode (Sandbox)` is set for `Yes`.

| Parameter | Description |
|---------|-----------|
| POS ID | POS ID from PayU system |
| Second MD5 key | Second MD5 key from PayU system |
| OAuth - client_id | client_id for OAuth protocol from PayU system |
| OAuth - client_secret | client_secret for OAuth from PayU system |

#### IOther parameters

| Parameter | Description |
|---------|-----------|
| Activate repeat payment? | [more information](#repeat-payment) |
| Activate remembering of cards? | Available only for  `Card payment` [more information](#saving-cards) |
| Czy uaktywnić moduł przewalutowania? | Available only for `Card payment` [more information](#conversion) |


## Information about properties

### Order of payment methods
To determine the order of display of payment method icons indicate the payment method symbols, separating them with a comma. [List of payment methods][ext6].

### Repeat payment
To use this option the POS should be properly configured in PayU and automatic receipt of payments should be disabled (auto-receipt is on by default). 
To do that log in to the PayU panel, go to the tab "Electronic payments", then click "My stores" and POS in the given store. 
The "Automatic payment receipt" option can be found at the bottom under the list of payment methods.

Repeat payment makes it possible to activate multiple payments in PayU to a single order in Magento. 
The plugin will automatically take receipt of the first successful payment while the other ones will be cancelled. 
Repeat payment from the buyer's point of view is also possible through the list of orders in Magento (a link "Pay again" will appear there). 
The buyer will also automatically receive an e-mail with such link. 
Thus, the buyer is able to successfully pay for his order even if the first payment was unsuccessful (for instance, no funds on the card, problems logging in to the bank, etc.).

### Saving cards
Saving card allows logged in users to remember the card for future payments. 
Each such remembered card is "tokenized", however, Magento does not process the card's full details in any way (they are entered using an embedded widget hosted by PayU) and does not save card tokens in its database (before use, current tokens for the given user are always downloaded from PayU).

To ensure proper functioning of the service additional configuration in PayU, consisting in creating and receiving tokens, is required. 
Additionally, the principles of authenticating payments with the remembered card can also be determined (by default every payment with a saved card requires providing a CVV code and being authenticated by 3DS but, for instance, an amount limit up to which this will not be necessary can be defined).

The buyer may save the card while making a payment, using the option "Use and save" on PayU widget while entering the card's details. 
Each card being remembered is subject to strong authentication during first payment (CVV and 3DS). 
A saved card will appear after choosing to pay with a card through PayU for the order and is visible in the user's account (tab "My saved cards"), where an option to delete it is also available.

### Conversion
Conversion, in other words Multi-Currency Pricing (MCP), makes it possible to charge the users' cards in a currency other than the currency of reconciliation with PayU. 
For instance, it is possible to charge the card in EUR but receive PLN from PayU. 
Conversion is based on Magento's functionality which makes it possible for "store-view" to define "display currency" other than "base currency". 
This option is more convenient for the buyer than DCC (Dynamic Currency Conversion) because the price in the currency of his card is displayed for each product and makes it easier to take a decision to make the purchase (in case of DCC the amount in the foreign currency is known only after payment is commenced). 
To activate this service you must:
* obtain the `mcpPartnerId` parameter PayU (which makes it possible to obtain FX tables from PayU with appropriate currency pairs),
* configure cyclical downloads of FX tables from PayU in Magento.
To activate and configure the service please contact PayU's account manager.


<!--external links:-->
[ext0]: https://github.com/PayU/plugin_magento_160
[ext1]: https://www.payu.pl/en/commercial-offer
[ext2]: http://php.net/manual/en/book.curl.php
[ext3]: http://php.net/manual/en/book.hash.php
[ext4]: https://github.com/PayU/plugin_magento_2/releases/latest
[ext5]: https://secure.snd.payu.com/boarding/?pk_campaign=Plugin-Github&pk_kwd=Magento2#/form
[ext6]: http://developers.payu.com/pl/overview.html#paymethods
[ext7]: https://github.com/PayU/plugin_magento_2
[ext8]: README.md

<!--images:-->
[img0]: readme_images/methods.png
