# :credit_card: Paystack Plugin for Flutter

[![build status](https://img.shields.io/github/workflow/status/wilburt/flutter_paystack/Build%20and%20Test)](https://github.com/wilburt/flutter_paystack/actions?query=Build+and+test)
[![Coverage Status](https://coveralls.io/repos/github/wilburt/flutter_paystack/badge.svg?branch=master)](https://coveralls.io/github/wilburt/flutter_paystack?branch=master)
[![pub package](https://img.shields.io/pub/v/flutter_paystack.svg)](https://pub.dartlang.org/packages/flutter_paystack)


<p>
    <img src="https://raw.githubusercontent.com/wilburt/flutter_paystack/master/screenshots/card_payment.png" width="200px" height="auto" hspace="20"/>
    <img src="https://raw.githubusercontent.com/wilburt/flutter_paystack/master/screenshots/bank_payment.png" width="200px" height="auto" hspace="20"/>
</p>


A Flutter plugin for making payments via Paystack Payment Gateway. Fully
supports Android and iOS.

## :rocket: Installation
To use this plugin, add `flutter_paystack` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).

Then initialize the plugin preferably in the `initState` of your widget.

``` dart
import 'package:flutter_paystack/flutter_paystack.dart';

class _PaymentPageState extends State<PaymentPage> {
  var publicKey = '[YOUR_PAYSTACK_PUBLIC_KEY]';
  final plugin = PaystackPlugin();

  @override
  void initState() {
    plugin.initialize(publicKey: publicKey);
  }
}
```

No other configuration required&mdash;the plugin works out of the box.

## :heavy_dollar_sign: Making Payments
There are two ways of making payment with the plugin.
1.  **Checkout**: This is the easy way; as the plugin handles all the
    processes involved in making a payment (except transaction
    initialization and verification which should be done from your
    backend).
2.  **Charge Card**: This is a longer approach; you handle all callbacks
    and UI states.

### 1. :star2: Checkout (Recommended)
 You initialize a charge object with an amount, email & accessCode or
 reference. Pass an `accessCode` only when you have
 [initialized the transaction](https://developers.paystack.co/reference#initialize-a-transaction)
 from your backend. Otherwise, pass a `reference`.
 

 ```dart
 Charge charge = Charge()
       ..amount = 10000
       ..reference = _getReference()
        // or ..accessCode = _getAccessCodeFrmInitialization()
       ..email = 'customer@email.com';
     CheckoutResponse response = await plugin.checkout(
       context context,
       method: CheckoutMethod.card, // Defaults to CheckoutMethod.selectable
       charge: charge,
     );
 ```

Please, note that an `accessCode` is required if the method is
`CheckoutMethod.bank` or `CheckoutMethod.selectable`.

 `plugin.checkout()` returns the state and details of the
 payment in an instance of `CheckoutResponse` .
 
 
 It is recommended that when `plugin.checkout()` returns, the
 payment should be
 [verified](https://developers.paystack.co/v2.0/reference#verify-transaction)
 on your backend.

### 2. :star: Charge Card
You can choose to initialize the payment locally or via your backend.

#### A. Initialize Via Your Backend (Recommended)

1.a. This starts by making a HTTP POST request to
[paystack](https://developers.paystack.co/reference#initialize-a-transaction)
on your backend.

1.b If everything goes well, the initialization request returns a response with an `access_code`.
You can then create a `Charge` object with the access code and card details. The `charge` is in turn passed to the `plugin.chargeCard()` function for payment:

```dart
  PaymentCard _getCardFromUI() {
    // Using just the must-required parameters.
    return PaymentCard(
      number: cardNumber,
      cvc: cvv,
      expiryMonth: expiryMonth,
      expiryYear: expiryYear,
    );
  }

  _chargeCard(String accessCode) async {
    var charge = Charge()
      ..accessCode = accessCode
      ..card = _getCardFromUI();

    final response = await plugin.chargeCard(context, charge: charge);
    // Use the response
  }
```
The transaction is successful if `response.status` is true. Please, see the documentation 
of [CheckoutResponse](https://pub.dev/documentation/flutter_paystack/latest/flutter_paystack/CheckoutResponse-class.html)
for more information. 



#### 2. Initialize Locally
Just send the payment details to  `plugin.chargeCard`
```dart
      // Set transaction params directly in app (note that these params
      // are only used if an access_code is not set. In debug mode,
      // setting them after setting an access code would throw an error
      Charge charge = Charge();
      charge.card = _getCardFromUI();
      charge
        ..amount = 2000
        ..email = 'user@email.com'
        ..reference = _getReference()
        ..putCustomField('Charged From', 'Flutter PLUGIN');
      _chargeCard();
```


## :wrench: :nut_and_bolt: Validating Card Details
You are expected but not required to build the UI for your users to enter their payment details.
For easier validation, wrap the **TextFormField**s inside a **Form** widget. Please check this article on
[validating forms on Flutter](https://medium.freecodecamp.org/how-to-validate-forms-and-user-input-the-easy-way-using-flutter-e301a1531165)
if this is new to you.

**NOTE:** You don't have to pass a card object to ``Charge``. The plugin will call-up a UI for the user to input their card.

You can validate the fields with these methods:
#### card.validNumber
This method helps to perform a check if the card number is valid.

#### card.validCVC
Method that checks if the card security code is valid.

#### card.validExpiryDate
Method checks if the expiry date (combination of year and month) is valid.

#### card.isValid
Method to check if the card is valid. Always do this check, before charging the card.


#### card.getType
This method returns an estimate of the string representation of the card type(issuer).


## :heavy_check_mark: Verifying Transactions
This is quite easy. Just send a HTTP GET request to `https://api.paystack.co/transaction/verify/$[TRANSACTION_REFERENCE]`.
Please, check the  [official documentaion](https://developers.paystack.co/reference#verifying-transactions) on verifying transactions.

## :helicopter: Testing your implementation
Paystack provides tons of [payment cards](https://developers.paystack.co/docs/test-cards) for testing.

## :arrow_forward: Running Example project
For help getting started with Flutter, view the online [documentation](https://flutter.io/).

An [example project](https://github.com/wilburt/flutter_paystack/tree/master/example) has been provided in this plugin.
Clone this repo and navigate to the **example** folder. Open it with a supported IDE or execute `flutter run` from that folder in terminal.

## :pencil: Contributing, :disappointed: Issues and :bug: Bug Reports
The project is open to public contribution. Please feel very free to contribute.
Experienced an issue or want to report a bug? Please, [report it here](https://github.com/wilburt/flutter_paystack/issues). Remember to be as descriptive as possible.

## :trophy: Credits
Thanks to the authors of Paystack [iOS](https://github.com/PaystackHQ/paystack-ios) and [Android](https://github.com/PaystackHQ/paystack-android) SDKS. I leveraged on their work to bring this plugin to fruition.
=======
Laravel PayTabs PT2

Description
-----------
This Package provides integration with the PayTabs payment gateway.

CONTENTS OF THIS FILE
---------------------
* Introduction
* Requirements
* Installation
* Configuration
* usage

INTRODUCTION
------------
This Package integrates PayTabs online payments into
the Laravel Framework starts from version 5.8 - 8.x.

REQUIREMENTS
------------
This Package requires no external dependencies.

INSTALLATION
------------
- composer require paytabscom/laravel_paytabs

CONFIGURATION
-------------
* composer dump-autoload

* Go to _config/app.php_ and in the providers array add

        Paytabscom\Laravel_paytabs\PaypageServiceProvider::class,

* Create the package config file:

        php artisan vendor:publish --tag=paytabs

* Go to _config/logging.php_ and in the channels array add
  
      'PayTabs' => [
      'driver' => 'single',
      'path' => storage_path('logs/paytabs.log'),
      'level' => 'info',
      ],
  
* In _config/paytabs.php_ add your merchant info.

**Important Hint:**
  you can pass your merchant info in the environment file with the same key names mentioned in the _config/paytabs.php_ file.
  This value will be returned if no environment variable exists for the given key. 
  

Usage
-------------

* create pay page

        use Paytabscom\Laravel_paytabs\Facades\paypage;

        $pay= paypage::sendPaymentCode('all')
               ->sendTransaction('sale')
                ->sendCart(10,1000,'test')
               ->sendCustomerDetails('Walaa Elsaeed', 'w.elsaeed@paytabs.com', '0101111111', 'test', 'Nasr City', 'Cairo', 'EG', '1234','100.279.20.10')
               ->sendShippingDetails('Walaa Elsaeed', 'w.elsaeed@paytabs.com', '0101111111', 'test', 'Nasr City', 'Cairo', 'EG', '1234','100.279.20.10')
               ->sendURLs('return_url', 'callback_url')
               ->sendLanguage('en')
               ->create_pay_page();
        return $pay;
  
* if you want to pass the shipping address as same as billing address you can use
        
        ->sendShippingDetails('same as billing')

* if you want to hide the shipping address you can use 
  
        ->sendHideShipping(true);

* if you want to use iframe option instead of redirection you can use
  
        ->sendFramed(true);


* refund (you can use this function to both refund and partially refund)

        $refund = Paypage::refund('tran_ref','order_id','amount','refund_reason');
        return $refund;




* Auth

        pay= Paypage::sendPaymentCode('all')
               ->sendTransaction('Auth')
                ->sendCart(10,1000,'test')
               ->sendCustomerDetails('Walaa Elsaeed', 'w.elsaeed@paytabs.com', '0101111111', 'test', 'Nasr City', 'Cairo', 'EG', '1234','100.279.20.10')
               ->sendShippingDetails('Walaa Elsaeed', 'w.elsaeed@paytabs.com', '0101111111', 'test', 'Nasr City', 'Cairo', 'EG', '1234','100.279.20.10')
               ->sendURLs('return_url', 'callback_url')
               ->sendLanguage('en')
               ->create_pay_page();
        return $pay;


* capture (the tran_ref is the tran_ref of the Auth transaction you need to capture it.
  
  you can use this function to both capture and partially capture.)

         $capture = Paypage::capture('tran_ref','order_id','amount','capture description'); 
         return $capture;



* void (the tran_ref is the tran_ref of the Auth transaction you need to void it.
  
  you can use this function to both capture and partially capture)

        $void = Paypage::void('tran_ref','order_id','amount','void description');
        return $void
    

* transaction details

        $transaction = Paypage::queryTransaction('tran_ref');
        return $transaction;

* if you face any error you will find it logged in: _storage/logs/paytabs.log_

PAYMENT RESULT NOTIFICATION
--------------------------------

PayTabs payment gateway provides means to notify your system with payment result once transaction processing was completed so that your system can update the transaction respective cart.

To get use of this feature do the following:


1- Defining a route (Optional)
--------------------------
Laravel PayTabs PT2 package comes with a default route for incoming IPN requests. The route URI is  _/paymentIPN_ ,  if you don't like it this URI just ignore it and define your own. Look at _src/routes.php_ to get a clue.



2- Implementing a means to receive notification
------------------------------------------

To receive notification, do one of the following:
* While creating a pay page, passed a URL as the second argument to _sendURLs_ method, that URL will receive an HTTP Post request with the payment result. For more about callback check: **merchant dashboard** > **Developers** > **Transaction API**.

* Second means is to configure IPN notification from merchant dashboard. For more details about how to configure IPN request and its different formats check: **merchant dashboard** > **Developers** > **Service Types**.


3- Configuring a callback method
--------------------------------
Now, you need to configure the plugin with the class\method that will grab the payment details and perform your custom logic (updating cart in DB, notifying the customer ...etc ).

* In your website _config/paytabs.php_ file, add the following:

        'callback' => env('paytabs_ipn_callback', new namespace\your_class() ),

* In your class add new method, it must named: **updateCartByIPN**

        updateCartByIPN( $requestData){
            $cartId= $requestData->getCartId();
            $status= $requestData->getStatus();
            //your logic .. updating cart in DB, notifying the customer ...etc
        }
you can also get transaction reference number. To get the list of available properties check: _Paytabscom\Laravel__paytabs\IpnRequest_ class.


