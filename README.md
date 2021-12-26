<br/><br/>
<p align="center" style="margin-bottom: 30px;"><a href="https://sandbox.jazzcash.com.pk/Sandbox/Home/GettingStarted" target="_blank"><img src="https://sandbox.jazzcash.com.pk/Sandbox/Content/images/logo_JazzCash.png" alt="Jazzcash" width="200"></a></p>

<p align="center">
<a href="https://travis-ci.org/akcybex/laravel-jazzcash"><img src="https://travis-ci.org/akcybex/laravel-jazzcash.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/akcybex/laravel-jazzcash"><img src="https://poser.pugx.org/akcybex/laravel-jazzcash/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/akcybex/laravel-jazzcash"><img src="https://poser.pugx.org/akcybex/laravel-jazzcash/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/akcybex/laravel-jazzcash"><img src="https://poser.pugx.org/akcybex/laravel-jazzcash/license.svg" alt="License"></a>
</p>

## Installation Steps

### 1. Require the Package
After creating your new Laravel application you can include the ***Laravel Jazzcash*** package with the following command:
```composer
composer require akcybex/laravel-jazzcash 
```

### 2. Add the Jazzcash Merchant Sandbox or Live Credentials

Next make sure to Jazzcash Merchant Sandbox or Live credentials to your .env file & their environment to use relevant credentials:
```dotenv
# Jazzcash Merchant
JAZZCASH_ENVIRONMENT=sandbox #You can set sandbox or live

SANDBOX_JAZZCASH_MERCHANT_ID=
SANDBOX_JAZZCASH_PASSWORD=
SANDBOX_JAZZCASH_INTEGERITY_SALT=
SANDBOX_JAZZCASH_RETURN_URL=
SANDBOX_JAZZCASH_ENDPOINT=https://sandbox.jazzcash.com.pk/CustomerPortal/transactionmanagement/merchantform

JAZZCASH_MERCHANT_ID=
JAZZCASH_PASSWORD=
JAZZCASH_INTEGERITY_SALT=
JAZZCASH_RETURN_URL=
JAZZCASH_ENDPOINT=https://payments.jazzcash.com.pk/CustomerPortal/transactionmanagement/merchantform
```

### 3. Run Migration
Next to run migration command to migrate jazzcash transaction table.
```
php artisan migrate 
```

### 4. Generate MWALLET Request Form Fields, Generate form & Send Payment Request
Next to generate MWALLET request form fields from your checkout details and set amount as well as send request:
> ***Note:*** Currently this package is only supporting MWALLET request soon we will add other features.
```php
// ...

// Get request data after validation while submitting checkout
$i = $request->all();

// will return form fields
$data = \AKCybex\JazzCash\Facades\JazzCash::request()->setAmount($i['amount'])->toArray($i);

// ...
```

Pass generated fields data to view and then paste this code in view:
```php
{{-- ... --}}

@php
    $jazzcash_environment = config('jazzcash.environment');
@endphp
<form name="redirect-to-payment-gateway" method="POST" action="{{ config("jazzcash.$jazzcash_environment.endpoint") }}">
    @foreach($data as $key => $value)
        <input type="hidden" name="{{ $key }}" value="{{ $value }}">
    @endforeach
</form>
<script>
    setTimeout(function () {
        document.forms['redirect-to-payment-gateway'].submit();
    }, 1000);
</script>

{{-- ... --}}

```
this will submit your request to jazzcash merchant and redirect automatically.

### 5. Handle Jazzcash response on transaction complete
Lastly, you just need to check code and get checkout details from jazzcash object which was submitted while checkout.
```php
// ...

Route::post('/jazzcash/payment', function (\Illuminate\Http\Request $request, $gateway) {
    $jazzcash = \AKCybex\JazzCash\Facades\JazzCash::response();
    if ($jazzcash->code() == 000) {
        // Checkout form details you can get here
        $order = $jazzcash->order();
        
        // ...
    } else {
        $error = $jazzcash->message();
        // ...
    }

});

// ...
```

###Support
[Contact Us](https://akcybex.com/contact)
### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
