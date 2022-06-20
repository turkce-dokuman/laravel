# Routing (Yönlendirme)

* ### [Temel Yönlendirme](6-yonlendirme.md#temel-yonlendirme)

## Temel Yönlendirme (Basic Routing)

Laravel'in en güzel yanlarından biriside basit bir yönlendirme bileşeni sunmasıdır. Kullanımı oldukça basit ve kolaydır. Ayrıca sözdizimi, kolay ve çok kapsamlıdır.

Yönlendirme demek yerine bölüm boyunca rota diyeceğim.

Laravel varsayılan yönlendirmeleri; `routes/` dizini içinde yaptırmaktadır. `Rota` dosyaları `app/Providers/RouteServiceProviders.php` dosyası içinde yüklenir, yani siz dilerseniz dosyaların yerlerini değiştirebilir veya birden fazla dosya kullanabilirsiniz.

Rotaları tanımlayabilmek için, laravel `Illuminate\Support\Facades\Route` cephesini kullanır(Facades Pattern).

Basit bir rotadan başlayalım:

```php
    use Illuminate\Support\Facades\Route;

    Route::get('/ilk-rotamiz', function () {
        return 'Merhaba Dünya !';
    });
```

Rotaları `controller` ile kullanma:

```php
    use Illuminate\Support\Facades\Route;
    use App\Http\Controllers\BlogController;

    Route::get('/', [BlogController::class, 'index']);
```

localhost'ta çalıştığınızı varsayıyorum. `http://localhost:8000/ilk-rotamiz` urline girdiğinizde `Merhaba Dünya !` mesajı ile karşılaşacaksınız. İşte bu kadar! Laravel de rota tanımladınız.

## Varsayılan Yönlendirme Dosyaları (Default Route Files)

Web arayüzü için `web.php` kullanılıyor. web arayüzünde, `web` ara katman yazılım(middleware) grubunu kullanıyor. Middleware kafanızı karıştırdıysa sonraki bölümde ondan bahsetmiş olacağım. Web ara katmanı grubuna dahil olanlar, tarayıcıdan uygulamamızı ziyaret ettiklerinde gereken bazı güvenlik önlemlerini alır. Web grubunda kullanılan ara katmanlar:

```php
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        // \Illuminate\Session\Middleware\AuthenticateSession::class,The router allows you to register routes that respond to any HTTP verb:
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
```

## Kullanılabilir Yönlendirme Yöntemleri (Available Route Methods)

Laravel rotalarını kullanırken aşağıdaki HTTP yöntemlerini kullanabilmenize izin verir.

```php
    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);
```

Belirli HTTP isteklerine yanıt veren bir rota kullanmak isteyebilirsiniz bunun için laravel'in bize sağlamış olduğu `match` yöntemini kullanabiliriz veya gelen bütün http isteklerine tek bir rotadan yanıt verebilirsiniz, bunun için laravel bize `any` yöntemini sağlıyor.

```php
    Route::match(['get', 'post'], '/get-post', function(){
        return "Sadece GET veya POST isteklerine yanıt verecek";
    });
    Route::any('/any-method', function(){
        return "Herhangi bir isteklere yanıt verecek";
    });
```

## Bağımlılık Enjeksiyonu (Dependency Injection)

Laravel rotalarından bahsederken çok güzel özellikleri olduğundan bahsetmişim. En güzel özelliklerinden bir tanesi de `bağımlılık enjeksiyonudur`. Laravel bağımlılık enjeksiyonlarını çok iyi ve geliştiricilerin işini oldukça kolaylaşıtrmaktadır.

```php
use Illuminate\Http\Request;

Route::get('/', function (Request $request) {
    dd($request);
});
```

Şimdi bu kullanımı açıklamaya çalışayım; Servis sağlayıcılarından epeyce bahsettik, laravel'in daha önceden kendi içinde `Illuminate\Http\Request` nesnesini kapsayıcıya kaydetti, rota içinde bunu kullandığımızda laravel bunu kapsayıcıdan getirir ve ilgili değişkene atar. Tabi bu sadece başlangıç, bu bölümün ilerisinde daha fazlası yer alacaktır.

## CSRF Koruması

`web.php` dosyasında tanımlanan rotalar da formlar ile veri gönderilirken form içine `csrf` token göndermeniz gereklidir, aksi taktirde `419` HTTP mesajıyla kaşılaşırsınız. Formlarda bu hatadan kaçınmak için, oluşturduğunuz form içinde `@csrf` direktifini kullanarak bunu düzeltebilirsiniz.

```html
<form method="POST" action="/profile">@csrf ...</form>
```

## Rotaları Yönlendirme (Routing)

Bazı durumlarda bir rotayı başka bir rotaya yönlendirmemiz gerekebiliyor. Örneğin kullanıcı sayfayı ziyaret ettiğinde kullanıcıyı `/anasayfa` rotasına yönlendirmek istiyoruz bu durumda ek kontrol yapmak yerine, laravel bize `Route::redirect` yöntemini sunuyor:

```php
    Route::redirect('/','/anasayfa', 301);

    Route::get('/ansayfa', function(){
        //
    });
```

`Route::redirect` yöntemi, 3. parametrede varsayılan olarak `302` HTTP durum kodunu döndürür. Bunu isteğe bağlı olarak değiştirebilirsiniz.

## Görünüm Yönlendirme (View Routing)

Sadece görünüm yönlendirmemizi gerektiren durumlar olabilir. `Route::view`, yöntemi bunun için yeterlidir. `Route::view`, 3 parametre alır, 1. parametre rota, 2. parametre görünüm adı`(resources/views/anasayfa.blade.php)`, 3. parametre ise görünüme veri göndereceksek, gönderilecek veri içeriği.

```php
Route::view('/anasayfa', 'anasayfa', ['mesaj' => 'merhaba dünya!']);
```

> Yönlendirme rotalarında rota parametreleri kullanılırken, aşağıdaki parametreler Laravel tarafından rezerve edilir ve kullanılamaz: `destination` ve `status`.

## Rota Parametreleri (Route Parameters)

Rotada parametre göndermeniz gerekebilir veya gelen parametreleri okumak. Laravel de parametre belirlemek için tırnaklar içinde süslü parantez `{}` kullanılır. Parametre tanımalamaları alt çizgi `_` ile başlayabilir. Url de verilen parametre adı ne ise yöntem içinden alırken aynı isimle almak zorundayız. Örn:

### Zorunlu Parametreler (Required Parameters)

```php
    Route::get('/kullanici/{id}', function ($id) {
    return 'Kullanici '.$id;
});
```

Laravel rotasında, parametre kısıtlılığı yoktur, istediğiniz kadar parametre ekleyebilirsiniz.

```php
    Route::get('/kullanici/{id}/gonderi/{slug}', function ($id, $slug) {
    //
    });
```

> Rotalar sıralarına göre enjekte edilir.

### Opsiyonel Parametreler (Optional Parameters)

Bazen parametre boş gelebilir laravel rotalarında parametre tanımladıktan sonra `{parametre?}` parametre isminin souna soru işareti `?` yazılır ve parametreye karşılık gelen değişkene varsayılan değer atanmalıdır aksi takdirde hatayla karşılaşırsınız:

```php
    Route::get('/kullanici/{id}/gonderi/{slug?}', function ($id, $slug = null) {
    //
});
```

## Parametre Kısıtlama (Parameter Restrictions)

Bazı durumlarda parametreler için kısıtlama koymak isteyebilirsiniz, mesela kullanıcıdan gelen `kullanıcı adı` parametresinde sadece harfler kullanmak isteyebilirsiniz, bunun için `where` yöntemini kullanabilirsiniz:

```php
    Route::get('/kullanici/{kullaniciAdi}', function ($kullaniciAdi) {
    //
})->where('kullaniciAdi', '[a-z]+');
```

Gelen istekte belirtilen kurala uymuyorsa, 404 sayfası döndürülür.

### Küresel kısıtlama (Global Restrictions)

Rotalanıza atadığınız kuralı her seferinde tanımlamak istemeyebilirsiniz, bunun için `app/Providers/RouteServiceProviders.php` dosyasının `boot` yönteminde tanımlayabilirsiniz. Bunun için `Route::pattern` yöntemini kullanabilirsiniz:

```php
    use Illuminate\Support\Facades\Route;
/**
 * Define your route model bindings, pattern filters, etc.
 *
 * @return void
 */
public function boot()
{
    Route::pattern('kullaniciAdi', '[a-z]+');
}
```

```php
    Route::get('/kullanici/{kullaniciAdi}', function ($kullaniciAdi) {
    //
})->where('kullaniciAdi', '[a-z]+');
```

Artık `kullaniciAdi` parametresi, bütün rotalar için sadece harfler içerebilir.

## Adlandırılmış Rotalar (Named Routes)

Rotalarınızı kullanırken sürekli uzun uzun rota isimleri yazmak ve en ufak değişiklikte bunları değiştirmek zorunda kalmamız gerçekten çok bunaltıcı olabilir. Bu durumda laravel bize `name` yöntemini sunar ve bu yönteme tanımlanan rota isminden rotaya ulaşabiliriz.

```php
Route::get('/kullanicilar', function () {
    //
})->name('kullanicilar');
```

Biz bu rotayı kullanmak istediğimizde ise `route` yardımcı fonksiyonunu kullanarak rotamıza erişebiliriz:

```php
    route('kullanicilar')

    // http://localhost/kullanicilar
```

> Rota adları her zaman benzersiz olmalıdır.

### Adlandırılmış Rotalarda Parametre Kullanmak

```php
    Route::get('/kullanicilar/{sayfa}', function ($sayfa) {
    //
})->name('kullanicilar');
```

```php
    route('kullanicilar', ['sayfa' => 2])

    // http://localhost/kullanicilar/2
```

şeklinde çalışır.

### Rotalarda Query String Parametre Kullanmak (Query String Parameters)

Rotada sadece parametre değil, query string paramterleri de kullanmak isteyebiliriz.

```php
    Route::get('/kullanicilar/{sayfa}', function ($sayfa) {
    //
})->name('kullanicilar');
```

```php
    route('kullanicilar', ['sayfa' => 2, 'durum' => 'aktif'])

    // http://localhost/kullanicilar/2?durum=aktif
```

### Rotalara Yönlendirme (Redirecting)

Bazı durumlarda çoğu yerde rotalara yönlendirme yaparak kodu yazmışsınızdır, sürekli bütün url'i yazmak ve en ufak değişiklikte değiştirmek zorunda kalabiliriz. Bu durumda :

```php
Route::get('/kullanicilar', function () {
    //
})->name('kullanicilar');
```

```php
    return redirect()->route('kullanicilar');

    // http://localhost/kullanicilar
```

Bu şekilde `/kullanicilar` sayfasına yönlendirme yapmış olduk.

## Rota Grupları (Route Groups)

Birden fazla rotamız var ve bu rotalara tek seferde ara katman veya ön ek koymak isteyebiliriz, bu durumda `group` yöntemini kullanabiliriz.

```php
Route::prefix('kullanicilar')->group(function () {
    Route::get('/', function () {
        // http://localhost/kullanicilar
    });

    Route::get('gonderiler/{sayfa}', function ($sayfa) {
        // http://localhost/kullanicilar/gonderiler/2
    });
});
```

Laravel rota bileşeni, eğik çizgilerle birleştirilmiş rotaların gruplandırılmasını sağlar. Yani sizin tanımladığınız rotanın başına veya sonuna eğik çizgi koymanıza gerek yok.

### Rotalara Ara Katman Atamak (Routes Middleware)

Ara katmanları merak ediyorsanız, `middleware` bölümüne gidiniz.

Gruplara giriş yaparken, birden fazla rotaya tek seferde bazı atamaları yapabileceğimizi söylemiştik, gruplara `middleware` ataması yapabiliriz.

```php
Route::prefix('kullanicilar')->middleware('auth')->group(function () {
    Route::get('/', function () {
        // http://localhost/kullanicilar
    });

    Route::get('gonderiler/{sayfa}', function ($sayfa) {
        // http://localhost/kullanicilar/gonderiler/2
    });
});
```

Şimdi biz bu gruba `auth` middleware'ını atadık. Bu middleware, bu rotalara erişmeye çalışacak olan kullanıcılar arasında kimlik doğrulamasını yapanların erişmesine izin verecektir.

`middleware` yöntemini kullanmadan rotalara `prefix`, `name`, `prefix` ve `as` yöntemlerini kullanabiliriz.

```php
Route::group(
    [
        'prefix' => 'kullanicilar',
        'middleware' => 'auth',
        'as' => 'kullanicilar.'
    ]
    ,function () {
    Route::get('/', function () {
        // http://localhost/kullanicilar
    });

    Route::get('gonderiler/{sayfa}', function ($sayfa) {
        // http://localhost/kullanicilar/gonderiler/2
    });
});
```

### Controller Ataması (Controllers)

Bu özellik laravel 9 ile geldi. Bu özellik, bir rotaya controller ataması yapmak için kullanılır. Rotaya `controller` yöntemin kullanarak atama yaparsanız, 2. parametre olarak `controller` belirtmek zorunda kalmayacaksınız.

```php
    use App\Http\Controllers\KullanicilarController;

    Route::controller(KullanicilarController::class)->group(function ()
    {
        Route::get('/', 'index');
        Route::get('/{id}', 'show');
    });
```

### Alt Alan Adı Yönlendirmesi (domain alias)

Laravel'de `subdomain` yöntemi ile bir rotaya alt alan adı yönlendirmesi yapabilirsiniz. Bunun için sunucu yapılandırmanızın buna müsade etmesi gereklidir.

```php
    use Illuminate\Support\Facades\Route;

    Route::domain('app.localhost')->group(function () {
        Route::get('/', function () {
            return url()->current();
        });
    });
```

Bu rotamıza; `http://app.localhost/` adresinden erişebiliriz.

### Rota isimleri öneki

Birden fazla rotanız olabilir ve bu rotalarınıza aynı ismi vermek istiyorsunuz, bunun için atadığınız rota gruplarına `name` atayarak önek ekleyebilirsiniz.

```php
Route::name('kullanicilar.')->group(function () {
    Route::get('gonderiler/{sayfa}', function ($sayfa) {
        // http://localhost/kullanicilar/gonderiler/2
    })->name('gonderiler');
});

// kullanicilar.gonderiler
```

`kullanicilar.gonderiler` adıyla bir rota ismi oluşturulmuştur.

## Rotalara Model Bağlama (Routes Model Binding)

Laravel Route bileşeninin en güzel özelliklerinden bir tanesine daha geldik, bu özellik, bir rotaya model bağlaması yapmak için kullanılır. Buna `model binding` denir. Bu bölüm için `eloquent` bölümünü birazda olsa bilmeniz gereklidir, aksi takdirde sizin için anlamsız olabilir.

### Örtük Bağlama (Implicit Binding)

Diyelim bizim bir rotamız var ve kullanıcı bu rotaya parametre girdiğinde veritabanı sorgusunu yapsın, kayıt varsa bize kaydı dönsün, yoksa bir hata dönsün.

Bu örneğimizde gönderileri sorgulayacağız.

```php
    use App\Models\Post;

    Route::get('/gonderi/{post}', function (Post $post) {
        return $post;
    });
```

Burada laravel rota bileşeninde `bağımlılık enjeksiyonu` kullanılmıştır, burada istenen parametredeki `nesne` model olduğunu anlayarak arkaplanda bir sorgu çalışacak ve ilgili kayıt varsa dönecek yoksa `404` hatası dönecektir.

Arkaplanda yapılan aksiyonu biraz daha açıklamaya çalışayım:

Larvel Route bileşenine iletilen parametrenin türüne bakıyor(bunları daha iyi öğrenmek isterseniz: `https://www.php.net/manual/en/class.reflection.php` bu adresi ziyaret edin), parametre eğer bir model ise, bu model için arkaplanda sorgu üretmek için `eloquent` bileşenini kullanır. Sorgunun yapıldığı kodu aşağıda:

```php
    public function resolveRouteBindingQuery($query, $value, $field = null)
    {
        return $query->where($field ?? $this->getRouteKeyName(), $value);
    }
```

Tabikide bu kodla işiniz olmaz amacım daha iyi anlatabilmektir, sakın ola kodu bulup değiştirmeyin.

Şimdi en sonda bu yönteme 3 parametre iletiliyor, 1. parametre de bizim yazdığımız `Post` modelimizin bir örneği, 2. parametre de istenen rota parametresi, 3. parametre de rota parametresi için sorguda kullanılacak kolon adı(ilk örnekte kolon belirtmedik). `getRouteKeyName` yöntemi, modellerde varsayılan olarak `id` kolonunu döndürür.

Şimdi kolon atayarak yapalım, bu örnekte parametre adından sonra `:` yazdıktan sonra hangi kolon üzerinden sorgu yaptırmak isteyeceğimizi belirteceğiz.

```php
    use App\Models\Post;

    Route::get('/gonderi/{post:slug}', function (Post $post) {
        return $post;
    });
```

Şimdi ise 3. parametreye `slug` değerini iletip, slug üzerinden sorgu yapılacaktır.

### Kolonu Özelleştirme (Explicit Binding)

Yukarıda kolon belirtme örneğini gösterdim, ama her rota için belirtmek zahmetli olabilir, bunun için `Post` modelimize `getRouteKeyName` yöntemi ekleyip hangi kolon üzerinden arama yapmasını söylebiliriz.

```php
    // app\Models\Post.php
    public function getRouteKeyName()
    {
        return 'slug';
    }
```

Bu durumda artık rotalarda `slug` kolonu üzerinden sorgu yapılacaktır.

### Kapsam Belirleme (Scoping)

Şimdi rotalarda denk gelmişsinizdir, örneğin github, mesela githubda bir kullanıcının profiline girdiğinizde burada sadece o kullanıcıya ait repositoriler listeleniyor, /kullanici\_adi/repository, laravel rotalarında normal durumlarda böyle gelmez. Mesela a kullanıcısının profiline girip repo yerine b kullanıcısının repo adını yazdınız diyelim bu rota çalışır(githubda öyle bişey yok sadece örnek :)).

Laravelde bunu engellemek için kapsam belirlenmesi gerekmektedir.

```php
    use App\Models\Post;
    use App\Models\User;
    use Illuminate\Support\Facades\Route;

    Route::get('/{user}/post/{post}', function (User $user, Post $post) {
        return $post;
    })->scopeBindings();
```

`scopeBindings` yöntemiyle kapsam belirleme yapılmıştır, yani `id`'si 1 olan kullanıcı, `user_id`'si 2 olan gönderiye erişemez, eğer `scopeBindings` kullanmamış olsaydık o şekilde görünürdü.

> Scope Binding kullanmak istediğiniz model dosyasında, bir `relationship` ile bağlantı kurduktan sonra `scopeBindings` yöntemini kullanın.

## Rota Hatalarını Özelleştirme (Error Handling)

Normal durumlarda örtük bağlamada kayıt bulamazsa 404 hatası dönecektir, ama bunu değiştirmek istiyorsunuz bunun için `missing` yöntemi kullanılabilir.

```php
    use App\Models\Post;
    use Illuminate\Http\Request;
    use Illuminate\Database\Eloquent\ModelNotFoundException;
    Route::get('/post/{post}', function (Post $post) {
        return $post;
    })->missing(function (Request $request, ModelNotFoundException $exception) {
        return response()->json([
            'message' => 'Not Found'
        ], 404);
    });
```

Veya geriye bir şablon döndürebiliriz:

```php
    use App\Models\Post;
    use Illuminate\Http\Request;
    use Illuminate\Database\Eloquent\ModelNotFoundException;

    Route::get('/post/{post}', function (Post $post) {
            return $post;
        })->missing(function (Request $request, ModelNotFoundException $exception) {
            return view('errors.404', [], 404);
        });
```

## Rota Enum Parametreleri (Route Enum Parameters)

PHP 8.1 ile gelen `enum` özelliği ile, rotalarınızda enum parametreleri kullanılabilir. Laravel rotasında ilgil enum değeri varsa çalışır yoksa 404 döner.

```php
    <?php

    namespace App\Enums;

    enum PostType: string
    {
        case Active = 'active';
        case Disable = 'disable';
    }
```

```php
    use App\Enums\PostType;
    use Illuminate\Support\Facades\Route;

    Route::get('/posts/type/{post}', function (PostType $post) {
        return $post->value;
    });
```

## Yedek Rotalar (Fallback Routes)

Laravel rotalarınızda, bir rota bulunamadığında, yedek rotalarınızı kullanabilirsiniz. Bunun için `fallback` yöntemi kullanılabilir.

```php
    Route::get('/posts/{post}', function (Post $post) {
        return $post;
    })

    Route::fallback(function () {
        return response()->json([
            'message' => 'Not Found'
        ], 404);
    });
```

> Yedek rota, her zaman en sonda olmalıdır.
