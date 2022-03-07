#

Laraveli kullanırken nasıl çalıştığını bilmeniz sizin açınızdan çok faydalı. Laravelin belgelerindede söyleneni buraya yazıyorum..

> "Gerçek dünyada" herhangi bir aracı kullanırken, o aracın nasıl çalıştığını anlarsanız kendinize daha çok güvenirsiniz. Uygulama geliştirme farklı değil. Geliştirme araçlarınızın nasıl çalıştığını anladığınızda, bunları kullanırken kendinizi daha rahat ve kendinden emin hissedersiniz.

## Başlangıç

Laravel uygulamasına istek gelince ilk çalışan yer `public/index.php` dosyasıdır. `index.php` dosyasında çok fazla kod bulunmaz, sadece laravel'i başlatmak için gereken bazı kodlar bulunur. Bunları sırasıyla açıklayalım.

Burada ilk önce uygulamanın bakım modu durumunu kontrol eder. Eğer uygulama bakım modunda ise, kullanıcıyı bakım sayfasına yönlendirir.
Uygulamamız bakım moduna alınınca `maintenance.php` dosyası oluşturulur ve `index.php` dosyasının içinde `require` ile çağırılır.

```php
if (file_exists($maintenance = __DIR__.'/../storage/framework/maintenance.php')) {
    require $maintenance;
}
```

Uygulamamız bakım modunda olmadığına göre artık `composer` ile yüklenen bağımlılıkları yükleyebiliriz.

```php
    require __DIR__.'/../vendor/autoload.php';
```

Şimdi de `bootstrap/app.php` dosyasını yükleyip, çerçeveyi başlatabiliriz.

```php
$app = require_once __DIR__.'/../bootstrap/app.php';
```

`app.php` dosyasında,

- `Application` sınıfının bir örneğini oluşturur ve `kapsayıcıya`, `HttpKernel`, `ConsoleKernel` ve `ExceptionHandler` nesnelerini kaydeder.
- Eğer `Http` isteği gelirse, `kapsayıcıya` kaydedilmiş, `HttpKernel` nesnesini çalıştırır ve istekleri işlemek için uygulamayı hazırlamaya başlar. Bir hata oluşursa, `ExceptionHandler` nesnesini çalıştırır ve hata işlemini gerçekleştirir.
- Eğer `Console` isteği gelirse, `kapsayıcıya` kaydedilmiş, `ConsoleKernel` nesnesini çalıştırır ve komutları işlemek için uygulamayı `console`u hazırlar.
- Bir hata oluşursa, `ExceptionHandler` nesnesini çalıştırır ve hata işlemini gerçekleştirir.

Çerçeve için gereken çekirdek dosyaları yüklendiğine göre isteğin durumuna göre, `HttpKernel` veya `ConsoleKernel` nesnesini çalıştırır.

## HttpKernel

Çerçeve Http isteğiyle çalıştırılırsa, kapsayıcıya kaydedilmiş, `HttpKernel` nesnesini getirir. Ardından isteği işlemek üzere `HttpKernel`'e iletir.

```php
    $kernel = $app->make(Kernel::class);

    $response = $kernel->handle(
        $request = Request::capture()
    )->send();
```

Son olarak, `HttpKernel` nesnesinin `handle` metodu, isteği işleyip, geriye bir `Response` nesnesi döndürür. Bu nesne, `Response` sınıfının bir örneğini döndürür. Eğer bir hata oluşursa, `ExceptionHandler` nesnesini çalıştırır ve hata işlemini gerçekleştirir, ve isteği sonlandırır.

```php
    $kernel->terminate($request, $response);
```

### Hizmet Sağlayıcıları (Service Provider)

Servis sağlayıcıları, çerçevenin çalışabilmesi için gereken servisleri yükler ve kullanıma hazır hale getirir. Servis sağlayıcıları, `config/app.php` dosyasının `providers` dizisinde bulunur. Bazı servislere örnek; `View`, `Routing`.

Servis Sağlayıcılarında; `boot` ve `register` yöntemleri bulunur. Kaydetme işlemi `register` yöntemine yazılır. Çalıştırmma işlemi `boot` yöntemine yazılır.

Tabiiki önce bütün servis sağlayıcılarının `register` yöntemi çalıştırılır, ardından `boot` yöntemleri çalıştırılır.

Hizmet sağlayıcıları `config/app.php` dosyasından yüklenir. Laravelin servis sağlayıcıları ve uygulamanın kendi sağlayıcıları `dizi[]` içinde sıralanmıştır.

Servis sağlayıcılarında `Register` yönteminde önce servis kaydedilir ve `boot` yönteminde yüklenir.

Laravelde sunulan çoğu önemli özellik servis sağlayıcıları tarafından yüklenmektedir. Bunlardan birkaçı: kuyruklama(queue), veritabanı, rotalar gibi bileşenlerdir.

### Yönlendirme(Routing)

Rotalar `RouteServiceProvider.php` servis sağlayıcısında yüklenir. Servis sağlayıcıları kaydedilip yüklendikten sonra isteği(`Request`) yönlendiriciye verilir ve yönlendirici istek işlemini gerçekleştirir. Gelen istek, eşleşiyorsa, iligli yere yönlendirilir, eşleşmeyen istek ise, `RouteNotFoundException` hatası döndürülür.

### Bölüm Sonu

Laravel'de yaşam döngüsü bu şekilde çalışır, yaşam döngüsünü bilmek bir geliştirici için önemlidir.
