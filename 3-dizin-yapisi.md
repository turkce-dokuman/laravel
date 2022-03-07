# Dizin Yapısı

- Giriş
- Kök Dizin
  - [`app` Dizini](#app-dizini)
  - [`boostrap` Dizini](#bootstrap-dizini)
  - [`config` Dizini](#config-dizini)
  - [`database` Dizini](#database-dizini)
  - [`public` Dizini](#public-dizini)
  - [`lang` Dizini](#lang-dizini)
  - [`resources`Dizini](#resoruces-dizini)
  - [`storage`Dizini](#storage-dizini)
  - [`tests`Dizini](#tests-dizini)
  - [`vendor`Dizini](#vendor-dizini)
- `app` Dizini
  - [`Console` Dizini](#console-dizini)
  - [`Events`](#events-dizini) Dizini
  - [`Exceptions`](#exceptions-dizini) Dizini
  - [`Http`](#http-dizini) Dizini
  - [`Jobs`](#jobs-dizini) Dizini
  - [`Listeners`](#listeners-dizini) Dizini
  - [`Mail`](#mail-dizini) Dizini
  - [`Models`](#models-dizini) Dizini
  - [`Notifications`](#notifications-dizini) Dizini
  - [`Policies`](#policies-dizini) Dizini
  - [`Observers`](#observers-dizini) Dizini
  - [`Providers`](#providers-dizini) Dizini
  - [`Rules`](#rules-dizini) Dizini
- `database` Dizini
  - [`Migrations`](#migrations-dizini) Dizini
  - [`Seeds`](#seeds-dizini) Dizini
- `resources` Dizini
  - [`js ve css dizini`](#js-ve-css-dizini) Dizini
  - [`Views`](#views-dizini) Dizini

## Giriş

Laravel, kurulduktan sonra birden fazla dizinle gelir ve bunlar, ilk bakışta göz korkutucu olabilir, ama endişelenmeyin hepsinin amacı var ve hepsi çok basit.
Yani bütün dizin/dosyalarla sürekli çalışmak gibi bir durumunuz yok, en çok `app` dizini içerisinde çalışıyor olacaksınız.

Laravel, `psr-4` otomatik yükleme standartını ve `psr-2` yazım standartını kullanır. Eğer bunları bilmiyorsanız;

- `https://www.php-fig.org/psr/psr-2/`
- `https://www.php-fig.org/psr/psr-4/`

adreslerini ziyaret edin

## App Dizini

`app` dizini, uygulamanın çekirdek kodlarını içeren dizindir. Uygulama geliştirme boyunca, en çok çalışacağımız dizinlerden bir tanesi burası.

## Bootstrap Dizini

`bootstrap` dizini, bootstrap'in Türkçesi önyükleme demek. Önyükleme dizini framework'ü yükleyen `app.php` dosyasını bulundurur. `app.php` dosyasında, `Application` sınıfının bir örneğini oluşturur ve `kapsayıcıya`, `HttpKernel`, `ConsoleKernel` ve `ExceptionHandler` nesnelerini kaydeder. Eğer `Http` isteği gelirse, `kapsayıcıya` kaydedilmiş, `HttpKernel` nesnesini çalıştırır ve istekleri işlemek için uygulamayı hazırlamaya başlar. Bir hata oluşursa, `ExceptionHandler` nesnesini çalıştırır ve hata işlemini gerçekleştirir. Eğer `Console` isteği gelirse, `kapsayıcıya` kaydedilmiş, `ConsoleKernel` nesnesini çalıştırır ve komutları işlemek için uygulamayı `console`u hazırlar. Bir hata oluşursa, `ExceptionHandler` nesnesini çalıştırır ve hata işlemini gerçekleştirir.

Genellikle bu dizinde değişiklik vb yapmanız istenmez.

## Config Dizini

`config` dizini, uygulama yapılandırmalarını burada tutar. Yani uygulamada bir yapılandırma gerekiyorsa burdan yapılır başka bir yerden yaptırmanızı istemez. Mümkün olduğunca yapılandırmalarınızı burada yapın.

## Database Dizini

`database` dizini, seed, migration ve factory dizinlerini bulundurur. Ayrıca burayı sqlite deposu olarak kullanabilirsiniz. Bu bölümün sonuna doğru alt dizinlerden bahsedilmiştir.

## Public Dizini

`public` dizini, isteklerin ilk gelidiği `index.php` dosyasını bulundurur. Bu dizin içerisinde herkese açık dosyalar tutulur. Örn : javascript dosyaları, css dosyaları vb.

## Lang Dizini

Lang dizini, dil dosyalarını bulundurur. Uygulamanızda çoklu dil kullanmak isterseniz, buradan yönetebilirsiniz.

## Resoruces Dizini

`resources` dizini, MVC'nin V'si diyebiliriz. Burda şablonlar, derlenmemiş css ve javascript dosyalarını barındırır. Bu dizin dışarıya açık değildir ve açılması kesinlikle önerilmez !

## Routes Dizini

`routes` dizini, uygulama için tanımlanacak rotaları içerir yani gereken sayfa yönlendirmelerini burdan yapılır. `Routes` dizini içerisinde gelen hazırda; `web.php`, `api.php`, `console.php` ve `channels.php` bulunmakta.

`web.php` dosyası, `RouteServiceProvider` tarafından yüklenir. `web` ara katman yazılım grubuna yerleştirilidiği yolları içerir. Uygulamada Restfull api sunmuyorsa muhtemelen sadece `web.php` dosyasını kullanırsınız.

`api.php` dosyası, `RouteServiceProvider` tarafından yüklenir. `api` ara katman yazılım grubuna yerleştirilidiği yolları içerir. Adından da anlaşılacağı gibi bu dosya normal istekleri kabul etmez yani `token` olmadan ve belirtilen ara katman yazılımları kullanılıyorsa istek atılamaz.

`console.php` dosyası, `app/Console/Kernel.php` tarafından yüklenir. Bu dosya HTTP isteklerine kapalıdır. Komut satırından tanımlayacağınız işlemleri yapar. Örn

```phpt
  Artisan::command('hello', function () {
          echo "Hello World";
  });
```

```shell
  php artisan hello
```

> Hello World

`channels.php` dosyası, uygulamanın etkinlik ve yayın kanallarının desteklediği tüm rotaları tanımlamanıza olanak verir.

## Storage Dizini

`storage` dizini, logları, derlenmiş blade(laravel blade motorunu kullanıyor.) şablonları, session(oturum)ları, dosya önbellekleri framework tarafından oluşturulan diğer önbellekleri tutar.
Bu dizin, `app`, `framework` ve `log` dizinlerine ayrılmıştır. `app` dizini uygulama tarafından oluşturulan dosyaları içerir ve bu dizinde siz de dosya depolayabilirsiniz. `framework` dizini, framework tarafından oluşturulan önbellek dosylarını bulundurur. `logs` dizini, uygulamanın loglarını içerir.
`storage/app/public` dizini, kullanıcılar tarafından yüklenen fotoğraflar vb. dosyaları tutması için kullanılabilir fakat direkt erişiminiz yok bunun için `public/storage` adında dizin'e sembolik bağlantı vermeniz gerekiyor. Bunu da `php artisan storage:link` komutu ile yapabilmeniz mümkün.

## Tests Dizini

`tests` dizini, otomatik testlerinizi içerir. Her test sınıfnın sonuna `Test` eklenmelidir.

## Vendor Dizini

`vendor` dizini, composer bağımlılıklarını içerir. Bu dizinde hiçbir şekilde değişiklik yapmamanız gerekir. Hatta hiç yapmayın. composer ile bir paket kurduğunuzda veya güncelleme yaptığınızda yaptığınız değişiklikler silinir.

### App Dizini İçindekiler

Uygulamanın çoğu bu dizin altında bulunur. Bu dizin composer otomatik yükleme standartına göre yapılmışıtr.
[PSR-4 otomatik yükleme standartı](https://www.php-fig.org/psr/psr-4/)

`App` dizini, CLI, HTTP ve Servis sağlayıcıları gibi diğer dizinleri de içerir. Bazı dizinler `artisan make` komutu çalışmadan oluşturlmaz örnek `Mail` dizini `php artisan make:mail Test` diye yazarsanız `Mail` dizinin oluştuğunu göreceksiniz.

### Console Dizini

`Console` dizini, kendiniz tanımlamak istediğiniz artisan komutlarını içerir. Yeni bir komut tanımlamak için `php artisan make:command test` diye çalıştırırsanız `Commands` dizininde `test.php` adında, komut tanımlamanız için bir dosya oluşacaktır.

### Exceptions Dizini

`Exceptions` dizini, hataların işlendiği ve kaydedildiği yerdir. Burdan hataları özelleştirebilirsiniz.

### Http Dizini

`Http` dizini, uygulamanın ara katman yazılımları, form istekleri ve controller'i bulundurur.

### Models Dizini

`Models` dizini, uygulamanın model dosyalarını barındırır. Model dosyaları veritabanı tablolarıyla ilişkilendirir yani her model dosyası bir tabloyla eşleşir.

### Providers Dizini

`Providers` dizini içinde servis sağlayıcıları bulunur. Bizimde `servis sağlayıcılarını` kullanabilmemiz için bu dizin ayrılmıştır. Ayrıca laravel tarafından yüklenen servis sağlayıcılarını ve bizim sonradan eklememiz gereken servis sağlayıcıları olursa bunlar `config/app.php` dosyasında, `Providers` dizisi içinde tanımlanır.

### Events Dizini

`Events` dizini, laravel'de oluşturulan eventlerin varsayılan olarak depolandığı dizin burasıdır.

### Listeners Dizini

`Listeners` dizini, laravel'de oluşturulan Listenerslerin varsayılan olarak depolandığı dizin burasıdır.

### Policies Dizini

`Policies` dizini, model bazlı kullanıcıları kısıtlamak isterseniz bunları kullanırsınız.

### Jobs Dizini

`Jobs` dizini, uygulamanızda eğer kuyrukları kullanacaksanız, oluşan dosyalar buraya gelir.

### Mail Dizini

`Mail` dizini, uygulamada mail kullanımına ihtiyaç duyarsanız burayı kullanacaksınız.

### Observers Dizini

`Observers` dizini, model bazlı olarak uygulamanızda oluşturulan modellere observer ekleyebilirsiniz. Observer'in amacı, modelde birşey yaptığınızda tetiklenir. Bunun ayrı bir yaşam döngüsü var. Eloquent bölümünde detaylıca açıklanmıştır.

### Rules Dizini

`Rules` dizini, yeni form kuralı oluşturursanız burayı kullanacaksınız. Örneğin bir formda bir alanın bir kuralı varsa bunu buraya yazabilirsiniz.

### Notifications Dizini

`Notifications` dizini, Laravel'in bildirimlerinden faydalanmak isterseniz burası kullanılır. Laravel bildirim kanallarında varsayılan olarak mail ve veritabanı bildirimlerini sunar. Fakat sms, telegram vs gibi diğer bildirim kanallarını da kullanmak isterseniz buna da izin veriyor yani bildirimlerin geçtiği yer burası.

### Migrations Dizini

Laravel'de oluşturulan migrations'ların varsayılan olarak depolandığı dizin burasıdır. Migrations dosyaları veritabanı tablolarını oluşturur.

### Seeds Dizini

Hazır kayıtlar ekleme ihtiyacınız varsa bu dizin içinden onları kullanabilirsiniz.

### Factory Dizini

Bazen sahte kayıtlar oluşturarak uygulamanızı test etmek isteyebilirsiniz, işte laravel bu durumda Factory'i bize sağlıyor ve Factory dosyaları burada tutulur.

### js ve css Dizini

Bu dizin içinde derlenmemiş javascript ve css dosyalarının bulunduğu dizin.

### Views Dizini

Bu dizin içinde oluşturulan veya kullanılan view dosyalarının bulunduğu dizin. Laravel varsayılan olarak bu dizini kullanır.
