# Route

- [Temel Yönlendirme](#temel-yonlendirme)
  -

# Temel Yönlendirme

Laravel, yönlendirmeler için karmaşık bir yapı kullanmıyor aksine çok basit ve kolay bir yapılandırma sunuyor.
`routes/web.php`, dosyası normal istekler için kullanılıyor. Api ve diğer yönlendirmeler için farklı dosyalar kullanılıyor. Bunlar varsayılan olanlar.Dilerseniz burda ayrıntılı anlatımı mevcut [Bakınız](../dizin-yapisi/dizin_yapisi.md#routes-dizini)

Basit bir yönlendirmeden başlayalım:
```php
    use Illuminate\Support\Facades\Route;

    Route::get('/ilk-rotamiz', function () {
        return 'Merhaba Dünya !';
    });
```
localhost'ta çalıştığınızı varsayıyorum. `http://localhost:8000/ilk-rotamiz` urline girdiğinizde `Merhaba Dünya !` mesajı ile karşılaşacaksınız.

# Varsayılan Yönlendirme Dosyaları
Laravel, uygulama için bütün yönlendirmeleri routes dizininde tutar. `App\Providers\RouteServiceProvider` servisi tarafından yüklenir. Web arayüzü için `web.php` kullanılıyor. web arayüzünde, `web` ara katman yazılım(middleware) grubunu kullanıyor. Web grubunda kullanılan ara katmanlar:
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
Bu yöntemler HTTP isteklerine yanıt veren yöntemler ve laravelde buna olanak sağlar. 
```php
    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);
```

Belirli HTTP isteklerine yanıt veren bir rota belirleyebilirsiniz `(post, get..)`, bunun için `match` kullanabilirsiniz veya bütün HTTP isteklerine aynı rotadan yanıt vermek isteyebilirsiniz, bunun için de `any` yöntemini kullanabilirsiniz.

```php
    Route::match(['get', 'post'], '/', function(){
        // 
    });
    Route::any('/', function(){
        // 
    });
```
## CSRF Koruması
`web.php` dosyasında tanımlanan rotalar da formlar ile veri gönderilirken form içine `csrf` göndermeniz gerekecektir, aksi taktirde `419` HTTP mesajıyla kaşılaşırsınız. Göndermek için `form` kapsamında `@csrf` yazmanız yeterlidir.

```html
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```

## Rotaları Yönlendirme
Bazı durumlarda bir rotayı başka yere yönlendirmemiz gerekebiliyor. Örneğin kullanıcı sayfayı ziyaret ettiğinde kullanıcıyı `/anasayfa` rotasına yönlendirmek istiyoruz bu durumda kontrol etmek yerine laravel bize `Route::redirect` yöntemini sunuyor:
```php
    Route::redirect('/','/anasayfa', 301);

    Route::get('/ansayfa', function(){
        //
    });
```
`Route::redirect` yöntemi, 3. parametrede varsayılan olarak `302` HTTP durum kodunu döndürür. Bunu isteğe bağlı olarak değiştirebilirsiniz.


## Görünüm(View) Yönlendirme
Sadece görünüm yönlendirmemizi gerektiren durumlar olabilir. `Route::view`, yöntemi bunun için yeterlidir.
`Route::view`, 3 parametre alır, 1. parametre rota, 2. parametre görünüm adı`(resources/views/anasayfa.blade.php)`, 3. parametre ise görünüme veri göndermeniz gerekebilir ve bunu sağlar. 
```php
Route::view('/anasayfa', 'anasayfa', ['mesaj' => 'merhaba dünya!']);

```  

## Zorunlu Parametreler
Rotada parametre göndermeniz gerekebilir veya gelen parametreleri okumak. Laravel de parametre belirlemek için tırnaklar içinde süslü parantez `{}` kullanılır. Parametre tanımalamaları alt çizgi `_` ile başlayabilir. Url de verilen parametre adı ne ise yöntem(fonksiyon,metod) içinden alırkende aynı isimle almak zorundayız. Örn:
```php
    Route::get('/kullanici/{id}', function ($id) {
    return 'Kullanici '.$id;
});
```

Rotanıza istediğiniz kadar parametre verebilirsiniz. 
```php
    Route::get('/kullanici/{id}/gonderi/{slug}', function ($id, $slug) {
    //
});
```
> Rotalar sıralarına göre enjekte edilir.

## Opsiyonel Parametreler
Bazen parametre boş gelebilir laravel rotalarında parametre tanımladıktan sonra `{parametre?}` parametre isminin souna soru işareti `?` yazılır ve parametreye karşılık gelen değişkene varsayılan değer atanmalıdır aksi takdirde hatayla karşılaşırsınız:

```php
    Route::get('/kullanici/{id}/gonderi/{slug?}', function ($id, $slug = null) {
    //
});
```