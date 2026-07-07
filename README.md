![](https://heatbadger.now.sh/github/readme/contributte/gopay-inline/)

<p align=center>
  <a href="https://github.com/contributte/gopay-inline/actions"><img src="https://badgen.net/github/checks/contributte/gopay-inline/master"></a>
  <a href="https://coveralls.io/r/contributte/gopay-inline"><img src="https://badgen.net/coveralls/c/github/contributte/gopay-inline"></a>
  <a href="https://packagist.org/packages/markette/gopay-inline"><img src="https://badgen.net/packagist/dm/markette/gopay-inline"></a>
  <a href="https://packagist.org/packages/markette/gopay-inline"><img src="https://badgen.net/packagist/v/markette/gopay-inline"></a>
</p>
<p align=center>
  <a href="https://packagist.org/packages/markette/gopay-inline"><img src="https://badgen.net/packagist/php/markette/gopay-inline"></a>
  <a href="https://github.com/contributte/gopay-inline"><img src="https://badgen.net/github/license/contributte/gopay-inline"></a>
  <a href="https://bit.ly/ctteg"><img src="https://badgen.net/badge/support/gitter/cyan"></a>
  <a href="https://bit.ly/cttfo"><img src="https://badgen.net/badge/support/forum/yellow"></a>
  <a href="https://contributte.org/partners.html"><img src="https://badgen.net/badge/sponsor/donations/F96854"></a>
</p>

<p align=center>
Website 🚀 <a href="https://contributte.org">contributte.org</a> | Contact 👨🏻‍💻 <a href="https://f3l1x.io">f3l1x.io</a> | Twitter 🐦 <a href="https://twitter.com/contributte">@contributte</a>
</p>

This library provides an easy-to-use API for communication with GoPay REST API v3, known as GoPay Inline.

## Versions

| State       | Version | Branch   | Nette        | PHP     |
|-------------|---------|----------|--------------|---------|
| dev         | `^2.2`  | `master` | 2.4 / ^3.0   | `^7.3`  |
| stable      | `^2.1`  | `master` | 2.4 / ^3.0   | `^7.2`  |

## Installation

Install `markette/gopay-inline` using [Composer](https://getcomposer.org).

```bash
composer require markette/gopay-inline
```

Why is the package still called Markette? Because we don't want to break other people's projects (for now).

## Requirements

From GoPay you need:

* **GoID**
* **ClientID**
* **ClientSecret**

On server you need:

* PHP >= 7.2
* cURL
* JSON

## Resources and documentation

* GoPay website ([https://www.gopay.com](https://www.gopay.com))
* Official documentation in English ([https://doc.gopay.com/en/](https://doc.gopay.com/en/))
* Official documentation in Czech ([https://doc.gopay.com/cs/](https://doc.gopay.com/cs/))

## Examples

See the [examples directory](examples) for complete examples.

## Library

There are 3 main parts of this library.

### 1) Client

A core class holding credentials, token, authenticator and http client. It could make authentication and requests to endpoints.

### 2) HttpClient

Delegates all requests / responses to IO. All requests go over `cURL`. Other implementations can be added there.

### 3) Services

Services provide easy-to-use API for creating and verifying payments.

## Supported API

* Verify payments (`$client->payments->verify(..)`)
* Standard payments (`$client->payments->createPayment(..)`)
* Recurrent payments (`$client->payments->createRecurrentPayment(..)`)
* Preauthorized payments (`$client->payments->createPreauthorizedPayment(..)`)

## Usage

### Authentication

First you need set up client with credentials.

```php
use Contributte\GopayInline\Client;
use Contributte\GopayInline\Config;

$goId = 'GoID';
$clientId = 'ClientID';
$clientSecret = 'ClientSecret';

// TEST MODE
$client = new Client(new Config($goId, $clientId, $clientSecret));
$client = new Client(new Config($goId, $clientId, $clientSecret, $mode = Config::TEST));

// PROD MODE
$client = new Client(new Config($goId, $clientId, $clientSecret, $mode = Config::PROD));
```

Then you have to authenticate with oauth2 authority server on GoPay.

For only creating payments use `Scope::PAYMENT_CREATE`, for the rest `Scope::PAYMENT_ALL`.

```php
use Contributte\GopayInline\Api\Lists\Scope;

$token = $client->authenticate(['scope' => Scope::PAYMENT_CREATE]);
```

The token can now be used to make API requests.

### Creating payment request

This example of payment data was copied from official documentation.

```php
use Contributte\GopayInline\Api\Entity\PaymentFactory;
use Contributte\GopayInline\Api\Lists\Currency;
use Contributte\GopayInline\Api\Lists\Language;
use Contributte\GopayInline\Api\Lists\PaymentInstrument;
use Contributte\GopayInline\Api\Lists\SwiftCode;

$payment = [
	'payer' => [
		'default_payment_instrument' => PaymentInstrument::BANK_ACCOUNT,
		'allowed_payment_instruments' => [PaymentInstrument::BANK_ACCOUNT],
		'default_swift' => SwiftCode::FIO_BANKA,
		'allowed_swifts' => [SwiftCode::FIO_BANKA, SwiftCode::MBANK],
		'contact' => [
			'first_name' => 'John',
			'last_name' => 'Doe',
			'email' => 'johndoe@contributte.org',
			'phone_number' => '+420123456789',
			'city' => 'Prague',
			'street' => 'Contributte 123',
			'postal_code' => '123 45',
			'country_code' => 'CZE',
		],
	],
	'amount' => \Money\Money::CZK(50000),
	'order_number' => '001',
	'order_description' => 'some order',
	'items' => [
		['name' => 'item01', 'amount' => \Money\Money::CZK(40000)],
		['name' => 'item02', 'amount' => \Money\Money::CZK(13000)],
		['name' => 'item03', 'amount' => \Money\Money::CZK(7000)],
	],
	'eet' => [
		'celk_trzba' => \Money\Money::CZK(50000),
		'zakl_dan1' => \Money\Money::CZK(35000),
		'dan1' => \Money\Money::CZK(5000),
		'zakl_dan2' => \Money\Money::CZK(8000),
		'dan2' => \Money\Money::CZK(2000),
		'mena' => Currency::CZK,
	],
	'additional_params' => [
		['name' => 'invoicenumber', 'value' => '2017001'],
	],
	'callback' => [
		'return_url' => 'http://www.myeshop.cz/api/gopay/return',
		'notify_url' => 'http://www.myeshop.cz/api/gopay/notify',
	],
	'lang' => Language::CZ,
];
```

```php
// Create payment request
$response = $client->payments->createPayment(PaymentFactory::create($payment));
$data = $response->getData();
```

`$client->payments` returns `PaymentsService`, you can create this service also by `$client->createPaymentsService()`.

`PaymentsService::createPayment` need object of `Payment`, you can set-up it manually by yourself or via `PaymentFactory`.
But over PaymentFactory, there is parameters validation and price validation.

#### Tips

You cannot combine more **payment instruments** (according to GoPay Gateway implementation). So, you should create payment
only with one **payment instrument**, for example only with `BANK_ACCOUNT` or `PAYMENT_CARD`.

#### For ALL payment instruments

```php
use Contributte\GopayInline\Api\Lists\PaymentInstrument;

$payment['payer']['allowed_payment_instruments']= PaymentInstrument::all();
```

#### For ALL / CZ / SK swift codes

Use `allowed_swifts` and `default_swift` only with `BANK_ACCOUNT`.

```php
use Contributte\GopayInline\Api\Lists\SwiftCode;

$payment['payer']['allowed_swifts']= SwiftCode::all();
// or
$payment['payer']['allowed_swifts']= SwiftCode::cz();
// or
$payment['payer']['allowed_swifts']= SwiftCode::sk();
```

### Process payment

Now we have a response with payment information. There's same data as we send it before and also **new** `$gw_url`. It's in response data.

```php
if ($response->isSuccess()) {
	// ...
}
```

```php
$data = $response->getData();
$url = $data['gw_url'];

$url = $response->data['gw_url'];
$url = $response->gw_url;
$url = $response['gw_url'];

// Redirect to URL
// ...

// Send over AJAX to inline variant
// ...
```

For the inline variant, you can use the prepared [client-side JavaScript](client-side).

### Verify payment (check state)

All you need is `$paymentId`. Response is always the same.

```php
// Verify payment
$response = $client->payments->verify($paymentId);
```

## Bridges

### Nette

Fill your credentials in config.

```neon
extensions:
	gopay: Contributte\GopayInline\Bridges\Nette\DI\GopayExtension

gopay:
	goId: ***
	clientId: ***
	clientSecret: ***
	test: on / off
```

Inject `Client` into your services / presenters;

```php
use Contributte\GopayInline\Client;

/** @var Client @inject */
public $gopay;
```

## Class model

### Request

It contains information for cURL.

* `$url`
* `$headers`
* `$options`
* `$data`

### Response

It contains information after execution request. It could be success or failed.

* `$data`
* `$headers`
* `$code`
* `$error`

## Development

See [how to contribute](https://contributte.org/contributing.html) to this package.

This package is currently maintaining by these authors.

<a href="https://github.com/f3l1x">
  <img width="80" height="80" src="https://avatars2.githubusercontent.com/u/538058?v=3&s=80">
</a>

<a href="https://github.com/paveljurasek">
  <img width="80" height="80" src="https://avatars2.githubusercontent.com/u/1270132?v=3&s=80">
</a>

-----

Consider to [support](https://contributte.org/partners.html) **contributte** development team.
Also thank you for using this package.
