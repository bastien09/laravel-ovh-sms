# Laravel OVH SMS

**Work in progress**

This is an unofficial integration of the [php-ovh-sms](https://github.com/ovh/php-ovh-sms) library for Laravel 5.  

- OVH SMS plans & pricing: [Official site](https://www.ovhtelecom.fr/sms/)
- Getting credentials: [OVH Api Explorer](https://api.ovh.com/createToken/index.cgi?GET=/sms&GET=/sms/*&PUT=/sms/*&DELETE=/sms/*&POST=/sms/*)
- OVH SMS Api doc: [PHP OVH SMS](https://github.com/ovh/php-ovh-sms/blob/master/README.md)

## Installation

Require this package with composer:  
```bash
composer require akibatech/laravel-ovh-sms
```

After updating composer, add the ServiceProvider to the **providers** array in config/app.php:  
```php
Akibatech\Ovhsms\ServiceProvider::class,
```

If you want to use the Facade for rapid message sending, you can add this line to your config/app.php in the **aliases** section:  
```php
'Ovhsms' => Akibatech\Ovhsms\Facade::class,
```

Then, you should publish the **laravel-ovh-sms** to your config folder with the following command.  
```bash
php artisan vendor:publish --provider="Akibatech\Ovhsms\ServiceProvider"
```

## Basic usage

Send a message (using Facade) anywhere in your app:
```php
Ovhsms::sendMessage('+33611223344', 'Hello!');
```

Send a message in a controller using DI:
```php
public function myControllerAction(Akibatech\Ovhsms\OvhSms $client)
{
    $client->sendMessage('+33611223344', 'Hello!');
}
```

### Original API workflow

If you don't want to use ready-to-use helpers, you can follow the original workflow. Here's an example:  
```php
// Retrieve OVH SMS instance
$ovhsms = app('ovhsms'); // Or Ovhsms::getClient();

// Get available SMS accounts
$accounts = $ovhsms->getAccounts();

// Set the account you will use
$ovhsms->setAccount($accounts[0]);

// Create a new message that will allow the recipient to answer (to FR receipients only)
$sms = $ovh->createMessage(true);
$sms->addReceiver("+33601020304");
$sms->setIsMarketing(false);

// Plan to send it in the future
$sms->setDeliveryDate(new DateTime("2018-02-25 18:40:00"));
$sms->send("Hello world!");
```

## Api

This package give you an access to a ready to use **Ovh\Sms\SmsApi** instance with your configured credentials and you default sms account (if given).  
It also offer some helpers over the original Api.  

### Helpers

**newMessage( array|string $to, bool $marketing )** and **newMarketingMessage( array|string $to )** are useful to give a new **Message** instance (Ovh\Sms\Message).  

**sendMessage( array|string $to, string $message )** and **sendMarketingMessage( array|string $to, string $message)** are useful to send directly a message.
  
**getClient( void )** returns the **Ovh\Sms\SmsApi** instance.

### Original Api

Otherwise, you can access the rest of the **SmsApi**.  
You can find the doc on the [official repo](https://github.com/ovh/php-ovh-sms/blob/master/README.md).

All original methods are called dynamically, you don't need to **getClient**.

## Getting credentials

You can generate your credentials from the (official API Explorer site)[https://api.ovh.com/createToken/index.cgi?GET=/sms&GET=/sms/*&PUT=/sms/*&DELETE=/sms/*&POST=/sms/*] at OVH.
Once your credentials in hands, your can put them in **config/laravel-ovh-sms.php**. 
For convenience, you can put them in your .env file.  
Keys are:
- OVHSMS_APP_KEY => your application key
- OVHSMS_APP_SECRET => your application secret
- OVHSMS_OVHSMS_CONSUMER_KEY => your consumer key
- OVHSMS_ENDPOINT => your endpoint (defaults to ovh-eu)

If you want to, you can define your default sms account (formated like "sms-LLXXXXX-X").  
Config key is:
- OVHSMS_ACCOUNT => your sms account ID

## TODO & Ideas

- Events (message sent, ...)
- More helpers
- Tests ?
- Idea: integration of https://github.com/giggsey/libphonenumber-for-php to help parsing phone numbers into international format

## Licence

MIT