- [Laravel Paket Yayınlama](#laravel-paket-yayınlama)
- [Gereklilikler](#gereklilikler)
  - [composer](#composer)
- [Başlangıç](#başlangıç)


# Laravel Paket Yayınlama
Laravel'de bir paket nasıl yazılır belki çoğunlukla belki merak etmişsinizdir. Burda en ince ayrıntısına kadar anlatmaya çalışacağım.

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
2.   mkdir development-package
3.   cd development-package
```
1. `laravel-paket` dizinine girdim.
2. `laravel-paket` dizini içinde, `development-package` diye bir klasör oluşturdum bu klasör içinde paketlerimizi geliştireceğiz. İsteyen istediği adı verebilir.
3. `development-package` klasörüne girdim.
   
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
