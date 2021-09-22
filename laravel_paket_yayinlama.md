- [Laravel Paket Yayınlama](#laravel-paket-yayınlama)
- [Gereklilikler](#gereklilikler)
  - [composer](#composer)
- [Başlangıç](#başlangıç)
- [Laravel İçin Paket Geliştirme](#laravel-i̇çin-paket-geliştirme)
  - [Paket Yazmaya Başlangıç](#paket-yazmaya-başlangıç)
    - [Service Provider](#service-provider)
    - [Route](#route)
    - [View](#view)
    - [Controller, Model](#controller-model)
      - [Oluşturma](#oluşturma)
      - [Model Ve Migration](#model-ve-migration)
        - [Migrations](#migrations)
- [Yayınlama](#yayınlama)
- [Not](#not)


# Laravel Paket Yayınlama
Laravel'de bir paket nasıl yazılır belki çoğunlukla belki merak etmişsinizdir. Burda en ince ayrıntısına kadar anlatmaya çalışacağım. Bu adım tamamen PHP'ye odaklıdır eğer daha önce hiç paket yayınlamadıysanız bu adımı mutlaka okuyun aksi takdirde [buraya gidin.](#laravel-i̇çin-paket-geliştirme)

# Gereklilikler
Listedekileri bilmiyorsanız öncelikle ne olduğunu birazda olsa öğrenmeniz gerekli çünkü onlar çok önemli, 2 ve 3. adımda 1 harfi yanlış yazsanız çok uğraşabilirsiniz(Çok yaşamıştım)
* [composer](https://getcomposer.org)
* [namespace nedir](https://www.php.net/manual/tr/language.namespaces.php) ? 
* [psr-4 otomatik yükleme standartı nedir](https://www.php-fig.org/psr/psr-4/) ?

## composer 
Composer ile bağımlılıkları yönetmemiz, laravele tanıtmamız ve yayınlayabilmemiz için gereklidir.

# Başlangıç
Başlangıç için bir laravel uygulamamızın olması gerekmez ama biz laravel için geliştirdiğimizden dolayı olması daha iyi.

Ben laravel projemi aşağıdaki komut ile kurdum. Herşeyi sıfırdan anlatacam isteyen var olan proje üzerinden gitsin amacım en temelden ele almaya çalışmak.
```bash
composer create-project --prefer-dist laravel/laravel laravel-paket
```

Projemiz kurulduktan sonra (ben linux kullanıyorum), komut satırından:
```bash 
1.   cd laravel-paket
2.   mkdir laravel-istanbul
3.   cd laravel-istanbul
```
1. `laravel-paket` dizinine girdim.
2. `laravel-paket` dizini içinde, `laravel-istanbul` diye bir klasör oluşturdum bu klasör içinde paketlerimizi geliştireceğiz. İsteyen istediği adı verebilir.
3. `laravel-istanbul` klasörüne girdim.
   
Şimdi ise paketimizin temellerini atmaya başlayalım..
```bash
    composer init
```
Bu komut bize `composer.json` dosyası oluşturacak. Elle de oluşturabiliriz ama komut ile hızlı bir şekilde bütün varsayılanları bize veriyor. Komutu çalıştırdıktan sonra sorular soracak. Onları açıklamaya çalışayım..

```bash
1. Package name (<vendor>/<name>) [ahmetbarut/package-development]: 
2. Description []: Paket yayınlama
3. Author [Ahmet Barut <ahmetbarut588@gmail.com>, n to skip]: 
4. Minimum Stability []: dev
5. Package Type (e.g. library, project, metapackage, composer-plugin) []: package
6. License []: MIT

7. Define your dependencies.

8. Would you like to define your dependencies (require) interactively [yes]? n
9. Would you like to define your dev dependencies (require-dev) interactively [yes]? n
10. Add PSR-4 autoload mapping? Maps namespace "Ahmetbarut\PackageDevelopment" to the entered relative path. [src/, n to skip]: src/

```
1. Paket adı, yani paketi hangi isim/paket-adi şeklinde isim koymanızı istiyor. Burası önemli notkalardan biri.
2. Paketin açıklaması.
3. Paketi yayınlayan kişinin adı soyadı ve email bilgisi
4. Minimum stabilite, paketin stabil mi geliştirmemi olduğunu belirtebilirsiniz. Burda paketin yayınlanmış herhangi bir sürümü olmadığı için `dev` yapın.
5. Paket tipi.
6. Lisansı seçmenizi sağlıyor.
7. Bağımlılık eklemek istediğinizi soruyor, eğer paketinizi başka paketleri kullanarak yazacaksanız burda bağımlılık belirtin sonradan da bağımlılık eklenebilir.
8. Paketin içinde kullanacağınız bağımlılıklar
9. Geliştirme için kurulan bağımlılıklar.
10. Burda psr-4 devreye giriyor. Otomatik yüklenmesi için ilgili namespace ve dizini bu şekilde mi olsun diyor. ben `src` yazdım yani kodlarımı onun içine yazacam. `src` yada belirtiğiniz başka klasör adının dışında yani üzerinde yazdığınız kodlara başka yerden erişemezsiniz çünkü klasör adı ve namespace belirtmişsiniz.

bu komuttan sonra `composer.json`, `vendor` ve `src` klasörü oluşuyor. Vendor ile pek işimiz olmayacak şimdilik. Çünkü bağımlılık kurmadık veya direkt ordaki `autload.php` dosyasına ihtiyacımız yok.
Benim `composer.json` dosyası içeriğim böyle. Composer varsayılan olarak paket yayıncısının adını büyük harfle yazıyor dilerseniz onu küçük harfe çevirin belli standartı olsun(ben kullanıcı adımı küçük harflerle yazıyorum). ondan sonra gelen ise paketin adı yani namespace'i(alan adı) sonunda da `\\` 2 tane ters slash var koymazsanız composer hata verir eşleşmiyor diye. Eğer `composer.json` dosyasında değişiklik yaparsanız `composer dumpautoload` komutunu çalıştırın güncellesin.
```json
{
    "name": "ahmetbarut/package-development",
    "description": "Paket yayınlama",
    "type": "package",
    "license": "MIT",
    "autoload": {
        "psr-4": {
            "ahmetbarut\\PackageDevelopment\\": "src/"
        }
    },
    "authors": [{
        "name": "Ahmet Barut",
        "email": "ahmetbarut588@gmail.com"
    }],
    "minimum-stability": "dev",
    "require": {}
}
```

Şimdi sıra geldi laravele paketimizi tanıtmaya yoksa otomatik olarak görmüyor. Bir üst dizine gidip, laravelin `composer.json` dosyasını açalım ve yerel paketimizi tanıtalım..

`repositories`, yerel paketleri tanıtmamız ve kurmamız için `composer` tarafından belgelerde anlatılan bir özelliktir. [Detaylı bakmak isteyenler](https://getcomposer.org/doc/articles/repository-priorities.md#canonical-repositories)
```json
   ...

   "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    },
    "repositories": {
        "local": {
            "type": "path",
            "url": "./package-development"
        }
    },

    ...
```
Ardından `composer require ahmetbarut/package-development` diyip paketimi kuruyorum. Eğer herşeyi doğru yaptıysanız hata almadan paketin kurulduğunu göreceksiniz. 

Şimdi ben basit bir şekilde `src` içinde `Hello.php` dosyası oluşturup bunu laravel tarafından kullanmaya çalışacam.

namespace, psr-4 içinde belirttiğiniz ile aynı olmalı. 2. olarak sınıf adı ve dosya adı aynı olmalı.
```php
<?php
// Hello.php

namespace ahmetbarut\PackageDevelopment;

class Hello
{
    public function merhabaDe()
    {
        echo "Merhaba Laravel İstanbul Topluluğu !";
    }
}

```


```php
<?php
// routes/web.php
use ahmetbarut\PackageDevelopment\Hello;
use Illuminate\Support\Facades\Route;


Route::get('/', function () {
    $hello = new Hello;
    $hello->merhabaDe();
    // Merhaba Laravel İstanbul Topluluğu !
});
```
Eğer buraya kadar herşey tamamsa ve sorun yoksa artık paketimiz için gereken kodları yazabiliriz ve buraya kadar olan kısım, sadece laravel için değil genel için paket oluşturmayı kapsıyor.

# Laravel İçin Paket Geliştirme
Şimdi `router`, `view`, `migration`,`model` ve `controller` dosyası olan bir paket yazalım. Bu paketimiz eklenmiş gönderileri listeleyen bir paket olsun. Biliyorum onun için pakete gerek yok ama neyin nasıl kullanılması gerektiğini göstermenin en iyi yolu bu olduğunu düşünüyorum. 
[Yayınladığım Paket](https://github.com/ahmetbarut/laravel-paket-yayinlama)

## Paket Yazmaya Başlangıç
Yukarda bahsetmiştim, direk bu bölüme gelenler için birdaha söyleyeyim. `src` veya namespace'te işaretlediğiniz dizinin içindeki dosyalar dışardan erişilir yani atıyorum paketi yayınladık `src` dışında bizim `config.php` diye bir dosyamız var ve buna `Controller` üzerinden erişmek istiyoruz burda ilk namespace gelir ama orası kapsam dışı olduğu için oraya erişemeyiz bu yüzden erişime açık, erişilmesi istediğimiz dosyaları `src` içine yazarız. Erişilmesini istiyorsanız da yine `src` içinden dahil etmelisiniz. 

### Service Provider
Şimdi bize ilk lazım olan şey `servis provider`. Paket içinde yazacağımız `router`ler, `view` şablonlarını laravel tarafından yüklenmesini sağlamamız gerekli.

`src` dizini içinde `provider` diye klasör oluşturup içine `LaravelIstanbulServiceProvider.php` diye bir dosya oluşturup içeriğini aşağıya yazacağım. Şimdi bu niye `src` içinde yazılıyor diyebilirsiniz nedeni dışardan erişilebilsin diye. 
İçeriği:
```php
<?php

namespace ahmetbarut\PackageDevelopment\Provider;

use Illuminate\Support\ServiceProvider;

class LaravelIstanbulServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
    }
}
```
Daha sonra `composer.json` dosyasına `provider`in yüklenmesi gerektiğini belirtelim yani kullanıcı paketi kurarken, `php artisan vendor:publis --provider=LaravelIsanbul...` demeden kendisi otomatik yapılsın istiyorsanız bu adımı izleyin, ben burdan gidecem.
```json
...
"license": "MIT",
    "extra": {
        "laravel": {
            "providers": [
                "ahmetbarut\\PackageDevelopment\\Provider\\LaravelIstanbulServiceProvider"
            ]
        }
    },
    "autoload": {
        ...
```
`composer.json` dosyasını güncelledik diye şu komutu çalıştırın.
```bash
    composer dumpautoload
```
### Route

Şimdi bize `route` dosyası lazım. `package-development` içinde `route/web.php` diye bir dosya oluşturup içeriği altta. `src` içinde değil !
```php
<?php
// laravel-istanbul/package-development/routes/web.php

use Illuminate\Support\Facades\Route;

Route::prefix('posts/')->group(function ()
{
   Route::get('all', function ()
   {

   });
});

```
Şimdi bu dosyayı servis sağlayıcısına söylememiz gerekli. Oluşturduğumuz service provider'in `boot` methodu içine bunları ekliyorum. `loadRoutesFrom()` yöntemi rota dosyalarını yüklememizi sağlayan bir yöntemdir. 
```php
    // LaravelIstanbulServiceProvider.php
    ...
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        // Burda da web.php dosyasını burdan yüklemesine yarayan bir yöntem.
        // Burda 2 kere üst dizine çıkıp routes/web.php dosyasını yükleyecektir.
        $this->loadRoutesFrom(__DIR__.'/../../routes/web.php');
    }
    ...
```
Ve yukarda tanımladığım rotayı deniyorum.
```php
<?php
// laravel-istanbul/package-development/routes/web.php

use Illuminate\Support\Facades\Route;

Route::prefix('posts')->group(function () {
    Route::get('all', function () {
        return "Hello World";
    });
});
```
Eğer herşeyi doğru yaptıysanız çalışacaktır aksi takdirde hatanız var demektir.

### View
Görünümleri'de yüklemek için yine `provider`lardan faydalanacağız. Paket dizini içinde `views` diye bir tane klasörü oluşturdum ve içinde `index.blade.php`  diye bir tane de dosya oluşturdum. Yine provider'in `boot` yöntemi içinden yüklemeyi sağlayacağız. `loadViewsFrom` yöntemi de `view` dosyalarımızı yüklememizi sağlar. `loadViewsFrom` yöntemi 2 parametre alır ve 2'side zorunlu. 1. parametresinde, yüklenecek olan `view` dosyalarının bulunduğu klasör, 2. parametre ise bunlara vereceğimiz isim. Çünkü yüklemeyi yapacağımız zaman, hangi `view` yüklenecek onu bilmiyor. İsim vererek bu sorunu aşabiliyoruz. Ben `laravelIstanbul` diye isim vereceğim, sizde isterseniz paketinizin adı veya paketinizin adıyla birlikte adınız size kalmış. 
```php
    // LaravelIstanbulServiceProvider.php
    ...
   public function boot()
    {
        // Burda 2 kere üst dizine çıkıp routes/web.php dosyasını yükleyecektir.
        $this->loadRoutesFrom(__DIR__.'/../../routes/web.php');

        $this->loadViewsFrom(__DIR__ . '/../../view', 'laravelIstanbul');
    }
    ...
```
View Dosyasını çağırmak için:
```php
// laravel-istanbul/package-development/routes/web.php

use Illuminate\Support\Facades\Route;

Route::prefix('posts')->group(function () {
    Route::get('all', function () {
        return view('laravelIstanbul::index');
    });
});
```

### Controller, Model 
Controller, Model, Policy vb. gibi diğer şeylerde provider gerekmiyor yani `app` içindekiler desem daha doğru olur. Çünkü bunlara `namespace` üzerinden erişim sağlanabiliyor. [Örnek paket TCG/Voyager](https://github.com/the-control-group/voyager) bunu incelemenizi tavsiye ederim.

#### Oluşturma
`src` içinde `Http/Controllers` klasörlerini oluşturdum ve içine örnek `PostController.php` dosyası oluşturup ekledim. 
`App\Http\Controllers\Controller` sınfını genişlettim yani extend ettim. Şimdi burdan view dosyası döndürelim.
```php
<?php

// laravel-istanbul/src/Http/Controllers/PostController.php

namespace ahmetbarut\PackageDevelopment\Http\Controllers;

use App\Http\Controllers\Controller;

class PostController extends Controller
{
    public function index()
    {
        return view('laravelIstanbul::index');
    }
}
```
```php
<?php
// laravel-istanbul/package-development/routes/web.php

use Illuminate\Support\Facades\Route;

Route::prefix('posts')->group(function () {
    Route::get('all', [\ahmetbarut\PackageDevelopment\Http\Controllers\PostController::class, 'index']);
});
```
Şimdi `model` ve `migration` sırası geldi öncelikle model oluşturup ardından migration ve paketi `packagistte` yayınlama.

#### Model Ve Migration
Komut satırından modeli ve migration'u oluşturup o şekilde pakete manuel olarak ekleyecem.
```bash
    php artisan make:model Post -m
```
Sonra paket dizini içinde `migrations` ve `src` içinde de `Models` diye klasörler oluşturacağım. Ardından oluşturulan model ve migrations dosyalarını alıp oraya koyuyorum ondan sonra model'in namespace'ini değiştirecem.
```php
    <?php
    // laravel-istanbul/src/Models/Post.php
    namespace ahmetbarut\PackageDevelopment\Models;

    use Illuminate\Database\Eloquent\Factories\HasFactory;
    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        use HasFactory;
    }
```
```php
<?php
// laravel-istanbul/migrations/2021_09_22_220755_create_posts_table.php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->string('text');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('posts');
    }
}
```
##### Migrations
Şimdi migrations dosyalarını Laravel'e tanıtmaya geldi yine providerin `boot` yönteminde yazacağız. `loadMigrationsFrom` yöntemiyle bunu yapabiliyoruz.

```php
    // laravel-istanbul/src/Provider/LaravelIstanbulServiceProvider.php
    public function boot()
    {
        // Burda 2 kere üst dizine çıkıp routes/web.php dosyasını yükleyecektir.
        $this->loadRoutesFrom(__DIR__.'/../../routes/web.php');

        $this->loadViewsFrom(__DIR__ . '/../../view', 'laravelIstanbul');

        $this->loadMigrationsFrom(__DIR__.'/../../migrations');

    }
```
Şimdi de ilgili `PostController` dosyamda, `Post` modelini çağırıp verileri `view` tarafına verecem.

Controller dosyasının son hali: 
```php
<?php

namespace ahmetbarut\PackageDevelopment\Http\Controllers;

use App\Http\Controllers\Controller;
use ahmetbarut\PackageDevelopment\Models\Post;

class PostController extends Controller
{
    public function index()
    {

        return view('laravelIstanbul::index')->with('posts', Post::all());
    }
}

```

# Yayınlama
Öncelikle paketi yayınlamak için (ben github kullanıyorum) paket için bir repository gerekli. Hızlı oluşturuyorum sizde [burdan](http://repo.new) 

Ardından paket'i açmış olduğunuz repositorye pushlayın.

Onu yaptıktan sonra `packagist` hesabı açmalısınız. Github ile'de oluşturabilirsiniz veya [burdan](https://packagist.org/register/)

onu da açtıktan sonra [`submit`](https://packagist.org/packages/submit) butonuna basın. Orda 1 adet input göreceksiniz oraya paketinizin repository adresini yapıştırıp `check` diyin ardından onay için birdaha basın ve paketiniz yayında oluyor.

# Not
Paketinizi bir etiket ile sabitlemezseniz, Packagist otomatik olarak varsayılan `branch`ten çeker bu da verilen son güncellemeler için sorun çıkarabilir bu nedenle stabil paketlerinizi etiket ile sabitleyin. `git tag` veya github kullanıyorsanız create relase var ordan yapabilirsiniz.
