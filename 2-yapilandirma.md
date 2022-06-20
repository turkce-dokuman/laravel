# Configuration

Laravel'in en güzel yanlarından biride kutudan çıkar çıkmaz kullanıma hazır olmasıdır. Laravel'i kurduktan sonra çok fazla bir yapılandırmaya ihtiyaç duymaz.

Laravel'in bütün yapılandırma dosyaları, kök dizindeki `config` klasöründe bulunur.

Laravel, yapılandırmaları kolaylaştırmak için `DotEnv` kütüphanesini kullanır ve bu sayede yapılandırmaları oldukça kolay bir şekilde düzenlememizi sağlar.

Laravel kurulduktan hemen sonra `.env.example` dosyasını, `.env` olarak kopyalar. `.env` dosyası içinde kritik bilgiler bulunduğundan dolayı, ziyaretçilere hiçbir şekilde açık olmamalıdır. Açık olması çok büyük güvenlik riski yaratabilir, nedeni bu dosya içinde veritabanı bilgileri, mail bilgileri, ödeme sistemi kullanıyorsanız; onun bilgileri vs.

Yapılandırmalar kök dizindeki `.env` dosyasında tutulmaktadır. Uygulamamız için hızlı bir şekilde yapılandırmak için `.env` dosyasını kullanabiliriz.

Şimdilik işimize yarayacak birkaç `.env` anahtarın bilgisini vermek istiyorum

```env
    APP_NAME=ilk-uygulamam # Uygulamamızın adıdır bunu istediğinizle değiştirebilirsiniz.
    APP_ENV=local # uygulamanın ortamını belirtmeniz içindir *development, local, product*
    APP_KEY=base64:... # Uygulamanızın şifreler bu çok önemlidir.
    APP_DEBUG=true # Eğer geliştirmedeyseniz ve hataları görmek istiyorsanız bunu ture yapın eğer hataları gizlemek istiyorsanız bunu false yapın hataları tamamen kapatır son kullanıcıya http hataları döndürür. bunu denemelisiniz.
    APP_URL=http://localhost:8000 # uygulamanın varsayılan url'i bunu kendiniz değiştirmelisiniz

    DB_CONNECTION=mysql # kullanacağınız veritabanı sunucusu laravel şuanda 4 veritabanını destekler bunlar; mysql, postgresql, sqlite ve sql server
    DB_HOST=127.0.0.1 # veritabanı bağlantı adresi bunlar genelde eğer veritabanı yereldeyse bu şekilde bırakılır ama uzak veritabanıysa buraya sunucu ip adresi verilir.
    DB_PORT=3306 # her veritabanı sunucusu portu
    DB_DATABASE= # veritabanı adı
    DB_USERNAME= # veritabanı kullanıcı adı
    DB_PASSWORD= # veritabanı kullanıcısının parolası
```

## .env ortam değişkenlerine erişme

Laravel'in bize sağladığı `env` yardımcı fonksiyonunu kullanabiliriz. Bu fonksiyonu kullanarak, `.env` dosyasında tanımlanan tüm değişkenleri kullanabiliriz. İki parametresi vardır, 1. parametre ise değişken adı, 2. parametre ise değişken boş veya yoksa tanımlanacak olan değer.

Örnek kullanım

```php
    env('APP_NAME', 'ilk-uygulamam');
```

Laravel uygulamasına istek atılınca; `.env`dosyasındaki bütün değişkenler, `$_ENV` küresel değişkenine atanır.

Mümkün olduğunca, `env`yöntemiyle uygulama içinden ortam değişkenlerine erişmeye çalışmayın, eğer böyle birşeye ihtiyacınız varsa, yapılandırma dosyası tanımlayıp, yapılandırma üzerinden erişin. Bunun nedenlerinden birisi; laravel yüklenince; `config`dizini içindeki yapılandırmalar yüklenir.

## Yapılandırma dosyaları

Yukarıda bahsedildiği gibi, laravel'in bütün yapılandırma dosyaları kök dizindeki `config` klasöründe bulunur. Bu dosyaların içinde yapılandırma ayarlarını yapılandırabiliriz.

`config/app.php` içinden bir değeri `config` fonksiyonu ile okuyalım. `config` fonksiyonu, `config` dizinindeki dosyaları okuyarak, yapılandırma ayarlarını bir dizi olarak döndürür.

```php
  $app_name = config('app.name');
```

`config` fonksiyonu, nokta sözdizimini kullanır;

```php
// config/database.php
'connections' => [

        'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],
]
```

`mysql` dizisini çekelim

```php
    config('database.connections.mysql');
```
