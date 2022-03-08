# Servis Sağlayıcıları

Servis Sağlayıcıları, laravel'in merkezi yeridir. Merkezi yeri denmesinin nedeni, laravel'deki bileşenler; `routing`, `veritabanı`, `view` gibi bileşenleri yükler.

Laravel'i ilk kurduğunuzda, tanımlı olarak gelen servis sağlayıcıları, `config/app.php` dosyasında `providers` dizisinde tanımlanmıştır. Laravel'de kullanmadığınız bileşenler, her istekte yüklenmez.

Servis sağlayıcıları, yüklenmeye başlarken; `register` ve `boot` yöntemleri çalışır.

## Servis Sağlayıcı Yazma

Eğer uygulamanız için bir servis sağlayıcısı yazmak istiyorsanız, terminalde `php artisan make:provider ServisSağlayıcıAdı` şeklinde komutunu kullanabilirsiniz.

Servis Sağlayıcısını oluşturduktan sonra içine girerseniz, `register` ve `boot` yöntemlerini göreceksiniz. Laravel'de önce bütün servis sağlayıcıların `register` yöntemlerini çalıştırır, sonra `boot` yöntemlerini çalıştırır. Yani bu yöntemler her seferinde birlikte çalışmaz atıyorum 15 tane servis sağlayıcısı olduğunu farz edelim, bu 15 servisin bütün `register` yöntemleri çalışır ardından `boot` yöntemleri çalışır.

Gerçek hayattan örnek verecek olursak, bir arabamız olduğunu düşünelim ve bu arabanın yürüyebilmesi için öncelikle kontağı açıp çevirmemiz gerekiyor, ardından motor ateşlenip arabayı sürmeye başlayabiliyoruz. Şimdi, arabamızda yakıt olmadığını varsayalım ve kontağı çevirdiğimizde motor ateşlenmediği için arabamız çalışmıyor. Farkettiyseniz burda da benzer bir yaşam döngüsü var.
Laravel'in servis sağlayıcılarında da iki yöntem var. `register` ve `boot` yöntemleri. Bu yöntemlerden biri servisleri kaydederken, diğeri servisleri kullanabilmemizi sağlar. Burada `register` yöntemini şu şekilde düşünebilirsiniz; kontağı çevirdiğimizde arabanın çalışması için motora bir komut gönderiliyor, komut gittikten sonra motor yakıt bekliyor(öncesinde biz arabaya yakıtı koyduk, yakıt olmadan çalıştırmayı deneseydik çalışmazdı), ardından motor çalışıyor.

`boot` yönteminde ise arabayı sürmemiz kalıyor yani araba yürüyebilir vaziyette.

### register

`register` yöntemi, kaydetmek istediğiniz sınıfı, kapsayıcıya eklemeyi sağlar. `register` yöntemi içinde başka bir bileşeni çağırmaya çalışmayın, çağırdığınız bileşen, henüz kaydedilmemiş olabilir ve bu uygulamanızın çalışmamasına sebep verebilir. Bunun sonuçlarını görmek istiyorsanız, herhangi bir `config` dosyası içinde çevirici kullanmaya çalışın, Laravel size `Tranlations` bileşeni yüklenmediği için hata döndürecektir.

Servis sağlayıcılarında her zamana `$app` özelliğine erişebiliriz. Bu özellik içinde laravel'de kullanabileceğimiz çoğu özelliğe erişebilmemizi ve servislere bileşenlerimizi kaydetmemizi sağlar.

```php
<?php

namespace App\Providers;

use App\Services\Github\Connect;
use Illuminate\Support\ServiceProvider;

class GithubServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton(Connect::class, function ($app) {
            return new Connect(config('github'));
        });
    }
}
```

Biz bu şekilde `Github` servisimizi kapsayıcıya kaydettik. Artık `Github` servisimizi kullanabiliriz.

### boot

Şimdi servisimizi nasıl kullanabileceğimizi görelim. `boot` yönteminde servisimizi kullanabiliriz.

```php
<?php

namespace App\Providers;

use App\Services\Github\Connect;
use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Cache;

class GithubServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function boot()
    {
        Cache::put('github.repositories', $this->app->make(Connect::class)->repositories(), now()->addHour());
    }
}
```

Servis sağlayıcımızı yazdıkatan sonra bunu laravel'e kaydetmemiz gerekiyor. Bunun için `config/app.php` dosyasına `providers` dizisine ekleyelim. Bu şekilde servisimizi kaydetmiş olduk.

```php
// config/app.php
'providers' => [
    ...
    App\Providers\GithubServiceProvider::class,
    ...
],
```
