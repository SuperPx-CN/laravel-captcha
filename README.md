# Laravel 验证码
支持 Laravel 5/6/7/8/9

一个简单的Laravel 图形验证码服务提供商。

## 预览
![Preview](https://image.ibb.co/kZxMLm/image.png)

- [Laravel Captcha](#captcha-for-laravel-5-6-7)
  * [预览](#preview)
  * [Installation](#installation)
  * [Usage](#usage)
  * [Configuration](#configuration)
  * [Example Usage](#example-usage)
    + [Session Mode:](#session-mode-)
    + [Stateless Mode:](#stateless-mode-)
- [Return Image](#return-image)
- [Return URL](#return-url)
- [Return HTML](#return-html)
- [To use different configurations](#to-use-different-configurations)
  * [Links](#links)
  
## 安装

使用 composer 安装
```
composer require superpx-cn/laravel-captcha
```

## 使用

To use the Captcha Service Provider, you must register the provider when bootstrapping your Laravel application. There are
essentially two ways to do this.

Find the `providers` key in `config/app.php` and register the Captcha Service Provider.

```php
    'providers' => [
        // ...
        'Mews\Captcha\CaptchaServiceProvider',
    ]
```
for Laravel 5.1+
```php
    'providers' => [
        // ...
        Mews\Captcha\CaptchaServiceProvider::class,
    ]
```

Find the `aliases` key in `config/app.php`.

```php
    'aliases' => [
        // ...
        'Captcha' => 'Mews\Captcha\Facades\Captcha',
    ]
```
for Laravel 5.1+
```php
    'aliases' => [
        // ...
        'Captcha' => Mews\Captcha\Facades\Captcha::class,
    ]
```

## Configuration

To use your own settings, publish config.

```$ php artisan vendor:publish```

`config/captcha.php`

```php
return [
    'default'   => [
        'length'    => 5,
        'width'     => 120,
        'height'    => 36,
        'quality'   => 90,
        'math'      => true,  //Enable Math Captcha
        'expire'    => 60,    //Stateless/API captcha expiration
    ],
    // ...
];
```

## Example Usage
### Session Mode:
```php

    // [your site path]/Http/routes.php
    Route::any('captcha-test', function() {
        if (request()->getMethod() == 'POST') {
            $rules = ['captcha' => 'required|captcha'];
            $validator = validator()->make(request()->all(), $rules);
            if ($validator->fails()) {
                echo '<p style="color: #ff0000;">Incorrect!</p>';
            } else {
                echo '<p style="color: #00ff30;">Matched :)</p>';
            }
        }
    
        $form = '<form method="post" action="captcha-test">';
        $form .= '<input type="hidden" name="_token" value="' . csrf_token() . '">';
        $form .= '<p>' . captcha_img() . '</p>';
        $form .= '<p><input type="text" name="captcha"></p>';
        $form .= '<p><button type="submit" name="check">Check</button></p>';
        $form .= '</form>';
        return $form;
    });
```
### 无状态模式:
你可以使用 助手函数 `captcha()` 来生成一个验证码 
`captcha()` 方法将会返回一个数组，其中包含了：
- sensitive
- key: 验证码的标识
- img: 验证码的 `base64`编码

并且使用次方法检查验证码
```php
    // key is the one that you got from json response
    // fix validator
    // $rules = ['captcha' => 'required|captcha_api:'. request('key')];
    $rules = ['captcha' => 'required|captcha_api:'. request('key') . ',math'];
    $validator = validator()->make(request()->all(), $rules);
    if ($validator->fails()) {
        return response()->json([
            'message' => 'invalid captcha',
        ]);

    } else {
        //do the job
    }
```

# Return Image
```php
captcha();
```
or
```php
Captcha::create();
```


# Return URL
```php
captcha_src();
```
or
```
Captcha::src('default');
```

# Return HTML
```php
captcha_img();
```
or
```php
Captcha::img();
```

# To use different configurations
```php
captcha_img('flat');

Captcha::img('inverse');
```
etc.

Based on [Intervention Image](https://github.com/Intervention/image)

^_^

## Links
* [Intervention Image](https://github.com/Intervention/image)
* [L5 Captcha on Github](https://github.com/mewebstudio/captcha)
* [L5 Captcha on Packagist](https://packagist.org/packages/mews/captcha)
* [For L4 on Github](https://github.com/mewebstudio/captcha/tree/master-l4)
* [License](http://www.opensource.org/licenses/mit-license.php)
* [Laravel website](http://laravel.com)
* [Laravel Turkiye website](http://www.laravel.gen.tr)
* [MeWebStudio website](http://www.mewebstudio.com)
