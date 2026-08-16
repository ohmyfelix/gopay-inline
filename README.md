![](https://heatbadger.now.sh/github/readme/contributte/gopay-inline/)

<p align=center>
  <a href="https://github.com/contributte/gopay-inline/actions"><img src="https://badgen.net/github/checks/contributte/gopay-inline/master"></a>
  <a href="https://packagist.org/packages/markette/gopay-inline"><img src="https://badgen.net/packagist/v/markette/gopay-inline"></a>
</p>

GoPay REST API v3 (GoPay Inline) client.

## Quick start

```bash
composer require markette/gopay-inline
```

Use **test-only** GoPay credentials while developing. Create a test-mode client, authenticate it for creating payments, and submit one payment:

```php
use Contributte\GopayInline\Api\Entity\PaymentFactory;
use Contributte\GopayInline\Api\Lists\Scope;
use Contributte\GopayInline\Client;
use Contributte\GopayInline\Config;
use Money\Money;

$client = new Client(new Config('test-go-id', 'test-client-id', 'test-client-secret', Config::TEST));
$client->authenticate(['scope' => Scope::PAYMENT_CREATE]);

$response = $client->payments->createPayment(PaymentFactory::create([
	'amount' => Money::CZK(100),
	'order_number' => 'test-001',
	'items' => [['name' => 'Test item', 'amount' => Money::CZK(100)]],
	'callback' => ['return_url' => 'https://example.test/return', 'notify_url' => 'https://example.test/notify'],
]));

$gatewayUrl = $response->getData()['gw_url'];
```

On a successful payment creation, the response data contains `gw_url`; redirect the buyer to that URL. Production credentials and detailed payment variants are in [examples](examples).

## Development

See [how to contribute](https://contributte.org/contributing.html) to this package.
