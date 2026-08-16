# GoPay Inline reference

The root README covers the test-mode first payment. Source-backed detailed variants are maintained as runnable examples:

- [authenticate](../examples/authenticate.php)
- [create a payment](../examples/create.php)
- [client modes](../examples/modes.php)
- [verify a payment](../examples/verify.php)

For Nette applications, register `Contributte\GopayInline\Bridges\Nette\DI\GopayExtension` and configure `goId`, `clientId`, `clientSecret`, and `test` under `gopay`.
