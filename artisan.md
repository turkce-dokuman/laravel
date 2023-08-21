# Artisan Konsolu

## Giriş
Artisan, Laravel ile birlikte gelen komut satırı arabirimidir. Artisan, uygulamanızın kök dizininde `artisan` betiği olarak bulunur ve uygulamanızı oluştururken size yardımcı olabilecek bir dizi yararlı komut sunar. Tüm mevcut Artisan komutlarının bir listesini görmek için `list` komutunu kullanabilirsiniz:

```sh
php artisan list
```

Her komut ayrıca "yardım" ekranını da içerir. Bu ekran, komutun mevcut argümanlarını ve seçeneklerini görüntüler ve açıklar. Yardım ekranını görmek için komut adının önüne `help` ekleyin:

```sh
php artisan help migrate
```

## Laravel Sail
Yerel geliştirme ortamınız olarak Laravel Sail'i kullanıyorsanız, Artisan komutlarını çağırmak için `sail` komutunu kullanmayı unutmayın. Sail, Artisan komutlarını uygulamanızın Docker konteynerleri içinde yürütecektir:

```sh
./vendor/bin/sail artisan list
```

## Tinker (REPL)
Laravel Tinker, Laravel çerçevesi için güçlü bir REPL (Read-Eval-Print Loop) aracıdır ve PsySH paketi tarafından desteklenmektedir.

### Kurulum
Tinker, tüm Laravel uygulamalarıyla varsayılan olarak gelir. Ancak, daha önce uygulamanızdan kaldırdıysanız Tinker'ı Composer aracılığıyla yükleyebilirsiniz:

```sh
composer require laravel/tinker
```

### Kullanım
Tinker, Eloquent modellerinizden, işlerden, etkinliklerden ve daha fazlasından dahil olmak üzere tüm Laravel uygulamanızla komut satırında etkileşimde bulunmanıza olanak tanır. Tinker ortamına girmek için tinker Artisan komutunu çalıştırın:

```sh
php artisan tinker
```

Tinker'ın yapılandırma dosyasını yayınlamak için `vendor:publish` komutunu kullanabilirsiniz:

```sh
php artisan vendor:publish --provider="Laravel\Tinker\TinkerServiceProvider"
```

dispatch Yardımcı Fonksiyonu ve dispatch Yöntemi
Dispatchable sınıfının dispatch yardımcı fonksiyonu ve dispatch yöntemi işi sıraya koymak için çöp toplamayı kullanır. Bu nedenle, tinker kullanırken işi sıraya koymak için Bus::dispatch veya Queue::push kullanmanız gerekmektedir.

## Komut İzin Listesi
Tinker, kabuk içinde çalıştırılabilecek Artisan komutlarını belirlemek için bir "izin" listesi kullanır. Varsayılan olarak, clear-compiled, down, env, inspire, migrate, optimize ve up komutlarını çalıştırabilirsiniz. Daha fazla komut çalıştırmak isterseniz, bunları tinker.php yapılandırma dosyanızdaki komutlar dizisine ekleyebilirsiniz:

```php
'commands' => [
    // App\Console\Commands\ExampleCommand::class,
],
```

## Takma Adlandırılmaması Gereken Sınıflar
Genellikle, Tinker sınıfları otomatik olarak takma adlandırırken, bazı sınıfları asla takma adlandırmak istemeyebilirsiniz. Bu durumda, sınıfları tinker.php yapılandırma dosyanızın dont_alias dizisine ekleyerek bunu başarabilirsiniz:

```php
'dont_alias' => [
    App\Models\User::class,
],
```

## Komut Yazma
Artisan ile sağlanan komutların yanı sıra, kendi özel komutlarınızı da oluşturabilirsiniz. Komutlar genellikle `app/Console/Commands` dizininde saklanır; ancak, Composer tarafından yüklenebilecek komutlarınızın yüklenebileceğiniz kendi depolama konumunuzu seçme özgürlüğüne sahipsiniz.

### Komut Oluşturma
Yeni bir komut oluşturmak için `make:command` Artisan komutunu kullanabilirsiniz. Bu komut, `app/Console/Commands` dizininde yeni bir komut sınıfı oluşturacaktır. Bu dizinin uygulamanızda bulunmadığından endişe etmeyin - `make:command` Artisan komutunu ilk kez çalıştırdığınızda otomatik olarak oluşturulur:

```sh
php artisan make:command SendEmails
```

### Komut Yapısı
Komutunuzu oluşturduktan sonra, sınıfın `signature` ve `description` özellikleri için uygun değerleri tanımlamalısınız. Bu özellikler, komutunuzu listeleme ekranında görüntülenirken kullanılır. `signature` özelliği ayrıca komutunuzun giriş beklentilerini tanımlamanıza olanak tanır. `handle` yöntemi komutunuz çalıştırıldığında çağrılır. Komut mantığınızı bu yönteme yerleştirebilirsiniz.

Bir örnek komut inceleyelim. Aşağıdaki örnekte, bu yöntemin imzalarına tüm bağımlılıkları istediğimiz gibi talep edebiliriz. Laravel hizmet konteyneri, bu yöntemin imzasında tür belirtilen tüm bağımlılıkları otomatik olarak enjekte eder:

```php
<?php
 
namespace App\Console\Commands;
 
use App\Models\User;
use App\Support\DripEmailer;
use Illuminate\Console\Command;
 
class SendEmails extends Command
{
    /**
     * Konsol komutunun adı ve imzası.
     *
     * @var string
     */
    protected $signature = 'mail:send {user}';
 
    /**
     * Konsol komutunun açıklaması.
     *
     * @var string
     */
    protected $description = 'Belirtilen kullanıcıya e-posta gönderir';
 
    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct(DripEmailer $drip)
    {
        parent::__construct();
 
        $this->drip = $drip;
    }
 
    /**
     * Konsol komutunu yürüt.
     *
     * @return mixed
     */
    public function handle()
    {
        $this->drip->send(User::find($this->argument('user')));
    }
}
```

Bu belge, Laravel Artisan Konsolu hakkında genel bir genel bakış sunar ve temel kullanımı ile özel komutların nasıl oluşturulacağını açıklar. Artisan'ı daha fazla incelemek için resmi Laravel belgelerine başvurmanızı tavsiye ederim.
