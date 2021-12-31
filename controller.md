# Controller

- Başlangıç
- Controller Yazma
  - Basit Controller
  - Tekli Controller
- Middleware

## Başangıç

 `Controllers`(Denetleyiciler), rotalarda yaptıracağımız işlemin karmaşasından kurtarır. Bütün kodlarınızı `routes/web.php` ve `routes/api.php` dosyalarında tanımladığınızı ve sadece `view`(görünüm) renderlemek değil veritabanı işlemleri, yetkilendirme vb. bir sürü işlemi yaptığınızı düşünün bu bir sürü karışıklığa yol açar ve kodun okunabilirliğini ciddi seviyede azaltabilmektedir. Bu durumda controller'i kullanmamız ve daha düzgün bir kod yapısı açısından yararımıza olacaktır.

## Controller Yazma

Controller; app/Http/Controllers dizini içinde kaydedilir. Eğer başka dizinde kaydediyorsanız bu, sonraki yazılımcı için sorun yaratabilir veya belirtmeniz gereklidir.

Komut satırınından

```shell
    php artisan make:controller HomeController
```

Varsayılan olarak yukarda belirtilen klasör içine ekleyerek `Controller` sınıfından genişletilir. `Controller`, bize `middleware` ve `authorization` kullanmamızı sağlar. İsteyen kullanmaz.

Basit bir yönlendirici ve controller.

```php
    // routes/web.php
    use App\Http\Controllers\UserController;

    Route::get('/', [UserController::class, 'index']);
```

```php
// app/Http/Controller/HomeController.php
namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Models\User;

class HomeController extends Controller
{
    /**
     * Show the profile for a given user.
     *
     * @param  int  $id
     * @return \Illuminate\View\View
     */
    public function index()
    {
        return view('welcome');
    }
}
```

Bu şekilde basit bir controller oluşturmuş ve kullanmış olduk.

## Tekli Controller

Tek eylemli controller diye geçiyor, bunun sebebi sadece `__invoke` adında bir yönteme sahip olması, yani siz routing'de `Route::get('/', UserController::class)` şeklinde belirtirseniz, rota filtresi `__invoke` yöntemini arayacaktır. Tek eylemli controller'i komut satırınından oluşturmak için;

```shell
php artisan make:controller TekEylemli --invokable
```

Şeklinde oluşturabilirsiniz.

## Middleware

Önceki videomda [`middleware`](https://www.youtube.com/watch?v=-7SnEDPMTzk)'den bahsetmiştim.
Controller içinde kullanımını anlatayım ve neden kullanalım. 
Şimdi sizin aynı grupta farklı rotalarınız olabilir ama siz bunu `Controller` bazlı yapmak istiyorsunuz burda oluşturduğunuz `controller`'in, `__construct` yönteminde bunu belirtmeniz gerekli. Yani ilgili rota çalıştığında, `controller` yüklenir ve burda ilk çalışan yöntem `__construct` yöntemi, burda middleware'i saptar ve ilgili isteğin buna erişimi varmı diye kontrol edilir. Yani siz bunu yöntem bazlı da yapabilirsiniz.

```php
// app/Http/Controller/HomeController.php

class HomeController extends Controller
{
    /**
     * Instantiate a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth');
    }
}
```
