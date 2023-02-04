# Middleware

Middleware(Ara Katman)lar, uygulamaya gelen `HTTP` isteklerini filtrelememizi sağlar. Örnek verecek olursak, sitemize giren botları engellemek istiyoruz, laravelde bunu middleware ile yakalayabiliriz. Laravel ile kimlik doğrulama için bir `middleware` ile gelir, burada kimliği doğrulanmayan kullanacıyı giriş sayfasına yönlendirir. Kimliği doğrulanmış kullanıcı ise isteğe devam eder.

Laravel içinde `CSRF` token koruması içinde bir middleware sağlar. Tanımlanan bütün `middleware`lar, `app/Http/Kernel.php` dosyasındadır.

## Middleware Tanımlama

Laravel de `middleware`lar, rota bazlı, grup ve global olarak 3'e ayrılır.
En basitten tanımlamaya başlayalım.

Öncelikle bir `middleware` oluşturalım ve ardından tanımlamalara başlayalım.

Middleware oluşturmak için terminali kullanacağız.

```shell
  php artisan make:middleware CheckAdmin
```

Bu komutu çalıştırdığımızda, `app/Http/Middleware` klasörüne bir `CheckAdmin.php` dosyası oluşturulacaktır.

Şimdi `CheckAdmin.php` dosyasına girelim ve istek atan kullanıcının rolüne bakıp, rolü admin değilse `401` hatasını verelim.

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckAdmin
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
        if ($request->user()->role != 'admin') {
            abort(401, 'Buraya erişiminiz yok.');
        }

        return $next($request);
    }
}
```

`handle` yöntemine 2 parametre iletilmiş durumdadır 1. `Illuminate\Http\Request` 2. `Closure`.

Middleware'ler `servis sağlayıcılar` ile çözümlendiğinden, uygulamamız yüklenirken hazırlanırlar ve kullandığımızda `kapsayıcıdan` isteniyor.

Burada `abort()` metodunu kullanarak, kullanıcının rolü admin değilse `401` hatasını vereceğiz, kullanıcı admin ise isteği devam eder.

## Middleware Kaydetme ve Kullanmak

### Rota Bazlı Kaydetme

Yukarda tanımladığımız `middleware`'i kaydetmek için `app/Http/Kernel.php` dosyasına girelim. `$routeMiddleware` değişkenine `check.admin` adında bir değer atayıp, yazdığımız `middleware`'i atayalım. Biz bu `middleware`'i kullandığımızda çalışacak ve işlevini yerine getirecektir.

```php
    ...
      protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
        'check.admin' => \App\Http\Middleware\CheckAdmin::class,
    ];
```

### Rotada Kullanma

Middleware'i kullanmak için tanımlamak istediğimiz rotaya zincirleme olarak `middleware` yöntemini kullanarak tanımlayalım. Bundan sonra bu rotanın kontrolünü `CheckAdmin` middleware'i ile yapılacaktır.

```php
Route::get('/administrator', function () {
    return view('administrator.index');
})
->middleware('check.admin');
```

## Küresel(global) Middleware Tanımlama ve Kullanma

Şimdi diyelim ki sitemize gelen, giriş yapmayan kullanıcıların kaydını tutmak istiyoruz ve bunu bütün rotalarımıza atamak istiyoruz ama hepsini tek tek rotalara kaydetmek zahmetlidir bunun için `global` middleware'i kullanıyoruz.

Öncelikle `middleware`'i yazalım.

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class GuestLogger
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
        if (auth()->guest()) {
            Log::info('guser : ' .  $request->path() . ' : ' . $request->ip());
        }

        return $next($request);
    }
}
```

Ve `app/Http/Kernel.php` dosyasına girelim. `$middleware` değişkenine `GuestLogger` sınfını atayalım. Artık `GuestLogger` sınfının işlevini yerine getirecektir ve misafir kullanıcıları `log` dosyasına yazacaktır.

```php
protected $middleware = [
    // \App\Http\Middleware\TrustHosts::class,
    \App\Http\Middleware\TrustProxies::class,
    \Illuminate\Http\Middleware\HandleCors::class,
    \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
    \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
    \App\Http\Middleware\TrimStrings::class,
    \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
    \App\Http\Middleware\GuestLogger::class,
];
```

### Hariç Middleware Kullanma

Birden fazla rotanıza aynı `middleware`'i atadığınızı varsayalım ve bir veya birden fazla rotada bu `middleware`'i kullanmak istemiyorsunuz burada laravel bize `withoutMiddleware` yöntemini sunmaktadır. Bu yöntem ile ilgili rotalarda parametre olarak iletilen `middleware`'i hariç tutarak kullanmaz.

Şimdi biz yukarıda `check.admin` middleware tanımladık ve anasayfamıza gelen kullanıcıların administrator şartını kaldıralım.

```php
Route::get('/', function () {
    return view('index');
})
->withoutMiddleware(['check.admin']);
```

> `withoutMiddleware` yöntemiyle, `global` middleware'i hariç tutamaz.
> Ayrıca sadece belirli rotalara değil, rota gruplarında da istediğiniz `middleware`'i hariç tutabilirsiniz.

## Middleware Grupları

Birden fazla middleware'i belirli rota gruplarına atamak isteyebiliriz, bunun örneği `app/Http/Kernel.php` dosyasında `$middlewareGroups` dizisinde, `web` ve `api` gruplardır. Bu gruplardaki `middleware`ler farklıdır.

Bizimde admin grubu gibi bir rotamız olabilir. Admin grubundaki bütün kullanıcıların hem admin paneline girmelerini sağlamak, hemde girdikleri sayfaların loglanmasını sağlamak için `admin` grubu `middleware`'i tanımlayalım.

```php
protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            // \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
            'throttle:api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'admin' => [
            \App\Http\Middleware\CheckAdmin::class,
            \App\Http\Middleware\Logger::class,
        ],
    ];
```

Şimdi admin panelindeki kullanıcıların hareketlerini artık takip edebiliriz.

```php
Route::middleware('admin')->group(function () {
    Route::get('/', function () {
        return view('index');
    });
    Route::get('/posts', function () {
        return view('posts');
    });
})
```

## Middleware Parametreleri

Admin paneli için yetkisi olan kullanıcılar için ayrı `middleware`'ler tanımlayalım. Örneğin kullanıcı yönetimini bir kullanıcı yapsın, başka bir kullanıcı da `editör` olsun bunun için `middleware` parametrelerini kullanabiliriz.

```php
    // app/Http/Kernel.php
    protected $routeMiddleware = [
        ...
        'role' => \App\Http\Middleware\Role::class,
    ];
```

```php
    // app/Http/Middleware/Role.php
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            abort(403);
        }

        return $next($request);
    }
```

```php
Route::middleware('admin')->group(function () {
    Route::get('/', function () {
        return view('users')->middleware('role:user_manager');
    });
    Route::get('/posts', function () {
        return view('posts');
    })->middleware('role:editor');
})
```

Bu şekilde rotalara rol belirtip kontrolünü yapabiliriz.