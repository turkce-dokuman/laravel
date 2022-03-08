# Route

- ## [Temel Yönlendirme](#temel-yonlendirme)

## Temel Yönlendirme

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

localhost'ta çalıştığınızı varsayıyorum. `http://localhost:8000/ilk-rotamiz` urline girdiğinizde `Merhaba Dünya !` mesajı ile karşılaşacaksınız. İşte bu kadar! Laravel de rota tanımladınız.

## Varsayılan Yönlendirme Dosyaları

Web arayüzü için `web.php` kullanılıyor. web arayüzünde, `web` ara katman yazılım(middleware) grubunu kullanıyor.
Middleware kafanızı karıştırdıysa sonraki bölümde ondan bahsetmiş olacağım. Web ara katmanı grubuna dahil olanlar, tarayıcıdan uygulamamızı ziyaret ettiklerinde gereken bazı güvenlik önlemlerini alır.
Web grubunda kullanılan ara katmanlar:

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

## Kullanılabilir Yönlendirme Yöntemleri

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

## Bağımlılık Enjeksiyonu

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

## Rotaları Yönlendirme

Bazı durumlarda bir rotayı başka bir rotaya yönlendirmemiz gerekebiliyor. Örneğin kullanıcı sayfayı ziyaret ettiğinde kullanıcıyı `/anasayfa` rotasına yönlendirmek istiyoruz bu durumda ek kontrol yapmak yerine, laravel bize `Route::redirect` yöntemini sunuyor:

```php
    Route::redirect('/','/anasayfa', 301);

    Route::get('/ansayfa', function(){
        //
    });
```

`Route::redirect` yöntemi, 3. parametrede varsayılan olarak `302` HTTP durum kodunu döndürür. Bunu isteğe bağlı olarak değiştirebilirsiniz.

## Görünüm(View) Yönlendirme

Sadece görünüm yönlendirmemizi gerektiren durumlar olabilir. `Route::view`, yöntemi bunun için yeterlidir.
`Route::view`, 3 parametre alır, 1. parametre rota, 2. parametre görünüm adı`(resources/views/anasayfa.blade.php)`, 3. parametre ise görünüme veri göndereceksek, gönderilecek veri içeriği.

```php
Route::view('/anasayfa', 'anasayfa', ['mesaj' => 'merhaba dünya!']);

```

> Yönlendirme rotalarında rota parametreleri kullanılırken, aşağıdaki parametreler Laravel tarafından rezerve edilir ve kullanılamaz: `destination` ve `status`.

## Rota Parametreleri

Rotada parametre göndermeniz gerekebilir veya gelen parametreleri okumak. Laravel de parametre belirlemek için tırnaklar içinde süslü parantez `{}` kullanılır. Parametre tanımalamaları alt çizgi `_` ile başlayabilir. Url de verilen parametre adı ne ise yöntem içinden alırken aynı isimle almak zorundayız. Örn:

### Zorunlu Parametreler

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

### Opsiyonel Parametreler

Bazen parametre boş gelebilir laravel rotalarında parametre tanımladıktan sonra `{parametre?}` parametre isminin souna soru işareti `?` yazılır ve parametreye karşılık gelen değişkene varsayılan değer atanmalıdır aksi takdirde hatayla karşılaşırsınız:

```php
    Route::get('/kullanici/{id}/gonderi/{slug?}', function ($id, $slug = null) {
    //
});
```

## Parametre Kısıtlama

Bazı durumlarda parametreler için kısıtlama koymak isteyebilirsiniz, mesela kullanıcıdan gelen `kullanıcı adı` parametresinde sadece harfler kullanmak isteyebilirsiniz, bunun için `where` yöntemini kullanabilirsiniz:

```php
    Route::get('/kullanici/{kullaniciAdi}', function ($kullaniciAdi) {
    //
})->where('kullaniciAdi', '[a-z]+');
```

Gelen istekte belirtilen kurala uymuyorsa, 404 sayfası döndürülür.

### Küresel kısıtlama

Rotalanıza atadığınız kuralı her seferinde tanımlaka istemeyebilirsiniz, bunun için `app/Providers/RouteServiceProviders.php` dosyasının `boot` yönteminde tanımlayabilirsiniz. Bunun için `Route::pattern` yöntemini kullanabilirsiniz:

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

## Adlandırılmış Rotalar

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

### Rotalarda Query String Parametre Kullanmak

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

### Rotalara Yönlendirme

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

## Rota Grupları

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

### Bağımlılık Enjeksiyonu Ve Parametreler

Laravel Rota bileşeninin diğer en güzel özelliğinden biriside, parametrelerin bağımlılık enjeksiyonu olarak kullanabilmemizi sağlamasıdır, en güzel özelliklerden birisi de bu.
