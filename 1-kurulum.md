#

- [Neden Laravel](#neden-laravel)
- [Laravel'i Kurmaya Başlayalım](#laraveli-kurmaya-başlayalım)
  - [composer ile kurulum](#composer-ile-kurulum)
  - [Laravel installer ile Kurulum](#laravel-installer-ile-kurulum)

## Neden Laravel

Laravel, son zamanlarda oldukça gelişen ve web geliştirme için çok fazla kolaylıklar sağlaması tercih sebeplerinden biri.
Eğer daha önce web geliştirmeyi ileriye taşıdıysanız, sayacağım birkaç özelliği bilirsiniz.
Laravel çerçevesi, kutudan çıkar çıkmaz; cron, mail, kuyruklama, veritabanı desteği ve en önemlisi dokümanı çok iyi.
Ayrıca Laravel'in topluluğu oldukça büyük ve sorunları çözmek veya hazır paketleri kullanmak çok kolay ve laravel popüler javascript çerçeveleriyle oldukça iyi çalışır.

## Laravel'i Kurmaya Başlayalım

Bu başlık altında, iki kurulum yönteminden bahsedeceğim.

1. composer
2. laravel installer

### composer ile kurulum

Composer, `php`'nin bağımlılıklarını yönetmek için kullanılan bir paket yöneticisi.
Composerin bilgisayarınızda kurulu olması gerekiyor detaylı bilgi için `https://getcomposer.org`
Composer'i kurduktan sonra kullanabilmek için komut satırına ihtiyacınız olacak.

Komut satırına `composer`diyerek çalıştıramıyorsanız;

- Mac os işletim sistemi: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
- GNU / Linux Dağıtımları: `$HOME/.config/composer/vendor/bin` veya `$HOME/.composer/vendor/bin`

Laravel'i kuralım

```shell
  composer create-project laravel/laravel ilk-uygulamam
```

yazarak kurulumu başlatabilirsiniz hızlı bir şekilde gereken paketleri kuracaktır. `ilk-uygulamam` adını kendinize göre değiştirebilirsiniz.

ardından uygulamamızın klasörüne girip uygulamayı ayağa kaldıralım.

```bash
  cd ilk-uygulamam
  php artisan serve
```

diyerek uygulamayı başlatabilirsiniz varsayılan olarak `http://localhost:8000` adresinden erişim sağlayabilirsiniz,ayrıca hostu veya portu değiştirmek isterseniz

```bash
    php artisan serve --host=192.168.159.196 --port=1234
```

Bu tamamen size kalmış.

### Laravel installer ile Kurulum

Laravel installeri kurmak için yine composer kullanmamız gereklidir. Laravel installeri kurmak için

```shell
  composer global require laravel/installer
```

Laravel installer kurulduktan sonra;

```shell
  laravel new ilk-uygulamam
```

Sonraki adım yapılandırma
