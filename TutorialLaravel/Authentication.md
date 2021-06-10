


> Written with [StackEdit](https://stackedit.io/).

# Tìm hiểu về Authentication trong Laravel

[Trending](https://viblo.asia/trending)

This post has been more than  **2 years**  since it was last updated.

Xin chào anh em, như anh em cũng biết là một hệ thống nào cũng cần có xác thực khi thực hiện một hoặc nhiều hành vi mà hệ thống cho phép. Để tiếp tục series  **Laravel và những điều thú vị**  thì hôm nay mình sẽ giới thiệu với các bạn  `Authentication`  trong Laravel - nó xây dựng giúp cho việc thực hiện xác thực vô cùng đơn giản. Bạn sẽ không ngờ được là Laravel nó xây dựng sẵn, chúng ta chỉ việc dùng và tùy ý theo chúng ta muốn mà thôi. Nào chúng ta hãy bắt tay đi tìm hiểu nó thôi.

# 1. Giới thiệu

Lướt qua phần config tí nhé, chúng ta sẽ thấy có một file  `config/auth.php`

```PHP
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | Authentication Defaults
    |--------------------------------------------------------------------------
    |
    | This option controls the default authentication "guard" and password
    | reset options for your application. You may change these defaults
    | as required, but they're a perfect start for most applications.
    |
    */

    'defaults' => [
        'guard' => 'web',
        'passwords' => 'users',
    ],

    /*
    |--------------------------------------------------------------------------
    | Authentication Guards
    |--------------------------------------------------------------------------
    |
    | Next, you may define every authentication guard for your application.
    | Of course, a great default configuration has been defined for you
    | here which uses session storage and the Eloquent user provider.
    |
    | All authentication drivers have a user provider. This defines how the
    | users are actually retrieved out of your database or other storage
    | mechanisms used by this application to persist your user's data.
    |
    | Supported: "session", "token"
    |
    */

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'token',
            'provider' => 'users',
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | User Providers
    |--------------------------------------------------------------------------
    |
    | All authentication drivers have a user provider. This defines how the
    | users are actually retrieved out of your database or other storage
    | mechanisms used by this application to persist your user's data.
    |
    | If you have multiple user tables or models you may configure multiple
    | sources which represent each model / table. These sources may then
    | be assigned to any extra authentication guards you have defined.
    |
    | Supported: "database", "eloquent"
    |
    */

    'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => App\User::class,
        ],

        // 'users' => [
        //     'driver' => 'database',
        //     'table' => 'users',
        // ],
    ],

    /*
    |--------------------------------------------------------------------------
    | Resetting Passwords
    |--------------------------------------------------------------------------
    |
    | You may specify multiple password reset configurations if you have more
    | than one user table or model in the application and you want to have
    | separate password reset settings based on the specific user types.
    |
    | The expire time is the number of minutes that the reset token should be
    | considered valid. This security feature keeps tokens short-lived so
    | they have less time to be guessed. You may change this as needed.
    |
    */

    'passwords' => [
        'users' => [
            'provider' => 'users',
            'table' => 'password_resets',
            'expire' => 60,
        ],
    ],
];

```

Hệ thống xác thực Authentication của Laravel được xây dựng dựa trên 2 thành phần cốt lõi - guard và provider.

# Guards

`Guard`  các bạn cứ hiểu như là một cách cung cấp logic được dùng để xác thực người dùng. Trong Laravel, thường hay dùng  `session guard`  hoặc  `token guard`.  `Session`  guard duy trì trạng thái người dùng trong mỗi lần request bằng cookie. Còn  `Token`  guard xác thực người dùng bằng cách kiểm tra token hợp lệ trong mỗi lần request.

Vì vậy, như bạn thấy, guard xác định logic của việc xác thực, và không cần thiết để luôn xác thực bằng cách lấy các thông tin hợp lệ từ phía back-end. Bạn có thể triển khai một guard mà chỉ cần kiểm tra sự có mặt của một thông tin cụ thể trong headers của request và xác thực người dùng dựa trên điều đó.

# Providers

Nếu  `Guards`  hỗ trợ việc định nghĩa logic để xác thực thì  `Providers`  lấy ra dữ liệu người dùng từ phía back-end. Nếu guard yêu cầu người dùng phải hợp lệ với bộ lưu trữ ở back-end thì việc triển khai truy suất người dùng sẽ được providers thực hiện.Laravel hỗ trợ cho việc người dùng truy suất sử dụng Eloquent và Query Buider vào database. Tuy nhiên, chúng ta có thể thêm bất kì thay đổi vào . Ví dụ nhé, các bạn đặt model User trong namespace App nữa mà các bạn muốn đặt trong namespace App\Model thì chúng ta sẽ thay đỏi  `providers`  tronbg file  `app/auth.php`  như sau :

```PHP
'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => App\Model\User::class,
        ],

        // 'users' => [
        //     'driver' => 'database',
        //     'table' => 'users',
        // ],
    ],

```

Chúng ta đăng ký tài khoản với hệ thống thì trường password thì phải tối thiểu 60 ký tự và tối đa 255 ký tự nhé. Chúng ta cũng có thể đổi thay đổi cho trường  `remember_token`  là  `nullable()`  khi migrate bảng  `users`.Ak mình nới thêm chút về cái trường  `remember_token`  - đây là trường sẽ lưu token để giành cho chức năng  `Remember me`  mình sẽ giới thiệu ở phần sau nhé. Đừng lo lắng nhé nếu mà chúng ta cứ sử dụng như default thì chúng ta chẳng cần động vào file  `config/auth.php`  này làm gì !!!!

# 2.Làm quen với Authentication trong Laravel

## Controller

Laravel sẽ có một vài các controller của Authentication có sẵn trong prject Laravel của chúng ta. Nó nằm ở App\Http\Controller\Auth namespace và chúng nằm trong folder  `app/Http/Controllers/Auth`

![](https://images.viblo.asia/929e161a-3bcc-41b6-9d6d-8d8732145afb.png)Chúng ta cùng điểm qua một chút xem các controller này làm gì nhé

`ForgotPasswordController`  dùng để cho điều khiển cho chức năng khi người dùng quên mật khẩu, thì hệ thống sẽ bắt người dùng nhập mail đăng ký với hệ thống để gửi mật khẩu mới.

![](https://images.viblo.asia/2a95429b-8cc6-4e56-94f0-2c4193bec9ec.png)

`LoginController`  làm nhiệm vụ điều khiển khi người dùng đăng nhập vào hệ thống. Người dùng sẽ đăng nhập bằng mail với mật khẩu khi đăng kí với hệ thông.  ![](https://images.viblo.asia/c4300595-2686-4fab-b231-3f5508183fb9.png)

`RegisterController`  làm nhiệm vụ đăng ký một thành viên mới trong hệ thống với các

![](https://images.viblo.asia/04defe1e-bb86-460c-bbeb-c9dac5a31d0b.png)

`ResetPasswordController`  làm nhiệm vụ điều khiển khi một user muốn thay đổi mật khẩu tài khoản đăng nhập hệ thống.

## Thành phần được sinh ra

Để bắt đầu thực hiện công việc xác thực cho hệ thống chúng ta hãy thực hiền 2 câu lệnh sau:

```PHP
php artisan make:auth

php artisan migrate // Dùng để migrate 2 bảng users và password_resets có sẵn trong project khi mới init

```

Khi thực hiện xong thì hệ thống sẽ sinh ra cho chúng ta những thứ sau. Thứ nhất các bạn vào  `routes/web.php`  sẽ thấy những dòng code này sinh ra

```PHP
Auth::routes();

Route::get('/home', 'HomeController@index')->name('home');

```

Vậy chúng ta cũng có thể hiểu, hệ thống sẽ tự sinh ra cho chúng ta những route để phục vụ cho việc login, logout, register, forgot password. Các bạn cũng có thể sử dụng câu lệnh  `php artisan route:list`  để xem được các thông tin như sau :

```PHP
+--------+---------------+------------------------+------------------+------------------------------------------------------------------------+--------------+
| Domain | Method        | URI                    | Name             | Action                                                                 | Middleware   |
+--------+---------------+------------------------+------------------+------------------------------------------------------------------------+--------------+
|        | GET|HEAD      | /                      |                  | Closure                                                                | web          |
|        | GET|HEAD      | api/user               |                  | Closure                                                                | api,auth:api |
|        | GET|POST|HEAD | broadcasting/auth      |                  | Illuminate\Broadcasting\BroadcastController@authenticate               | web          |
|        | GET|HEAD      | home                   | home             | App\Http\Controllers\HomeController@index                              | web,auth     |
|        | GET|HEAD      | home/loginUser         |                  | App\Http\Controllers\HomeController@index                              | web,auth     |
|        | GET|HEAD      | home/logoutUser        |                  | App\Http\Controllers\HomeController@onLogout                           | web,auth     |
|        | GET|HEAD      | login                  | login            | App\Http\Controllers\Auth\LoginController@showLoginForm                | web,guest    |
|        | POST          | login                  |                  | App\Http\Controllers\Auth\LoginController@login                        | web,guest    |
|        | POST          | logout                 | logout           | App\Http\Controllers\Auth\LoginController@logout                       | web          |
|        | POST          | password/email         | password.email   | App\Http\Controllers\Auth\ForgotPasswordController@sendResetLinkEmail  | web,guest    |
|        | GET|HEAD      | password/reset         | password.request | App\Http\Controllers\Auth\ForgotPasswordController@showLinkRequestForm | web,guest    |
|        | POST          | password/reset         |                  | App\Http\Controllers\Auth\ResetPasswordController@reset                | web,guest    |
|        | GET|HEAD      | password/reset/{token} | password.reset   | App\Http\Controllers\Auth\ResetPasswordController@showResetForm        | web,guest    |
|        | GET|HEAD      | register               | register         | App\Http\Controllers\Auth\RegisterController@showRegistrationForm      | web,guest    |
|        | POST          | register               |                  | App\Http\Controllers\Auth\RegisterController@register                  | web,guest    |
+--------+---------------+------------------------+------------------+------------------------------------------------------------------------+--------------+

```

Ngoài ra trong app/Http/Controllers sẽ sinh ra  `HomeController`, và các bạn  **lưu ý**  một điều là khi chúng ta enter câu lệnh  `php artisan make:auth`  thì nếu chúng ta có file  `HomeController.php`  trong folder app/Http/Controllers thì hệ thống sẽ báo là chúng ta có muốn xóa file HomeController.php cũ đó đi để sinh ra một file HomeController.php mới hay không. Nếu không thì vẫn giữ nguyên file HomeController.php cũ ban đầu.

Tiếp nữa các bạn vào folder  `resource/views`  thì ở đây sé sinh ra một folder chứa các template của auth, và một template  `home.blade.php`.

## Tùy biến Path trong controller

Ở trong  `LoginController, RegisterController, ResetPasswordController`  các bạn có thấy biến  `$redirect`  không, biến này giúp chúng ta điều chỉnh lại cái đường dẫn mà khi ta làm xong công việc login, register, reset password sẽ redirect tới đường dẫn mà ta tùy chỉnh.

```PHP
protected $redirectTo = '/home';

```

Khi user không được xác nhận thành công, họ sẽ tự động chuyển hướng quay lại form đăng nhập. Tiếp theo bạn cũng có thể điều chỉnh trong middeware  `RedirectIfAuthenticated`

```PHP
public function handle($request, Closure $next, $guard = null)
    {
        if (Auth::guard($guard)->check()) {
            return redirect('/home');
        }

        return $next($request);
    }

```

## Tùy biến Username, Guard

Theo mặc định của Laravel thì sẽ đăng nhập bằng  `email`. Nếu muốn tùy chỉnh thì chúng ta cũng có thể vào  `LoginController`  để định nghĩa method  `username`

```PHP
public function username()
{
    return 'username';
}

```

Chúng ta cũng có thể tùy biến  `guard`  - sử dụng để xác thực user. Để bắt đầu, định nghĩa một phương thức  `guard`  trong  `LoginController, RegisterController, ResetPasswordController`. Hàm này sẽ trả về một thể hiển guard.

```PHP
use Illuminate\Support\Facades\Auth;

protected function guard()
{
    return Auth::guard('guard-name');
}

```

## Tùy biến Validator, Storage khi register

Trong  `RegisterController`  các bạn cũng có thể customize lại một thành viên mới đăng ký như nào. Các bạn có thể thêm trường cho bảng  `users`  để lưu nhiều thông tin về user khi đang ký hơn. Ví dụ theo bảng  `users`  có sẵn trong Laravel thì chỉ có các trường như này

```PHP
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->rememberToken();
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
        Schema::dropIfExists('users');
    }
}

```

Các bạn có thể tạo 1 file mới migration mới và thêm các trường cho bảng  `users`

```PHP
public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('address');
            $table->string('phone');
            $table->integer('role');
        });
    }

```

Tiếp tục chúng ta sẽ cho hiển thị thêm 2 trường input để nhập address và phone của người dùng, nhớ thêm các trường vừa thêm trong biến  `$fillable`  của model User nhé. Sau đó trong RegisterController chúng ta sẽ thêm như sau :

```PHP
protected function validator(array $data)
    {
        return Validator::make($data, [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:6|confirmed',
            // thêm validate cho các trường vừa thêm 
            'address' => 'required',
            'phone' => 'required',
        ]);
    }

    /**
     * Create a new user instance after a valid registration.
     *
     * @param  array  $data
     * @return \App\User
     */
    protected function create(array $data)
    {
        return User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
            'address' => $data['address'],
            'phone' => $data['phone'],
            'role' => 2, // ý muốn là lưu user nếu hệ thông có role = 1 là admin, 2 là user :)))
        ]);
    }

```

Đây là ví dụ mình muốn customize lại phần register, nhưng trong thực tế thì người dùng ko mong muốn phải nhập nhiều dữ liệu như này, chúng ta có thể cho người dùng đăng ký các trường mặc định như trong Laravel, có điều vào phần chức năng chỉnh sửa Profile thì chúng ta mới cho update cập nhật thông tin người dùng !!!

## Truy suất người dùng đã xác thực

Khi mà các bạn đăng nhập thành công hệ thống thì các bạn có thể truy cập thông tin người dùng đã xác thực ở mọi nơi. Các bạn cần use  `use Illuminate\Support\Facades\Auth`  để sử dụng  `Auth`  nhé.

```PHP
$id = Auth::user()->id; hoặc Auth::id() //Lấy về ID người .
$user = Auth::user() // Lấy về tất cả các thông tin của người dùng.
$email = Auth::user()->email // Lây về email người dùng.
...

```

Để xem người dùng đã xác thực (đăng nhập được vào hệ thống) hay chưa thì dùng

```PHP
if (Auth::check()) {
echo "Nguoi dung da dang nhap he thong";
}

```

## Middleware

Trong Laravel , authentication có middleware  `auth`  , hệ thống muốn chắc chắn rằng người dùng phải đăng nhập xác thực trước khi được làm một số các thao tác đối với hệ thống. Middleware nằm trong ở  `app/Http/Middleware/RedirectIfAuthenticated.php`. Các bạn có thể sử dụng ngay ở  `routes`  hoặc trong hàm  `__contruct()`  ở mỗi Controller.

```PHP
Route::get('profile', function () {
    // Only authenticated users may enter...
})->middleware('auth');

```

```PHP
public function __construct()
{
    $this->middleware('auth');
}

```

Khi bạn gán dùng middleware auth trong route, bạn cũng có thể chỉ định guard nào sẽ được sử dụng để thực hiện công việc xác thực. Nhưng những guard mà bạn có thể lựa chọn chỉ được lựa auth.php

```PHP
'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'token',
            'provider' => 'users',
        ],
    ],

```

Ví dụ

```PHP
public function __construct()
{
    $this->middleware('auth:api');
}

```

# 3.Xác thực người dùng thủ công

Nhiều bạn không muốn dùng authentication controller có sẵn để xác thực người dùng. Đừng lo lắng về vấn đề đấy, chúng ta có thể tự xác thực theo ý muôn của chúng ta bằng cách sử dụng các class có sẵn ở  `Auth`. Hãy chú ý import  `Illuminate\Support\Facades\Auth`

```PHP
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class LoginController extends Controller
{
    /**
     * Handle an authentication attempt.
     *
     * @param  \Illuminate\Http\Request $request
     *
     * @return Response
     */
    public function authenticate(Request $request)
    {
        $credentials = $request->only('email', 'password');

        if (Auth::attempt($credentials)) {
            // Authentication passed...
            return redirect()->intended('dashboard');
        }
    }
}

```

Giải thích một chút nhé: Khi ta sử dụng  `Auth::attempt()`  nó sẽ nhận vào một mảng các key/value , như ví dụ ở trên thì hệ thống sẽ kiểm tra xem email có trong bảng users hay không, nếu có thì trường hash password trong bảng users sẽ được lấy ra để so sánh với hash  `password`. Và giá trị của  `Auth::attempt()`  sẽ tra về false hoặc true.

Chúng ta cũng có thể thêm trường thuộc tính vào  `Auth::attemp()`  để xác thực. Ví dụ chúng ta muốn chỉ admin mới được đăng nhập hệ thống

```PHP
if (Auth::attempt(['email' => $email, 'password' => $password, 'role' => 1])) {
    // email admin mới được xác thực thành công 
}

```

## Chú ý

Ngoài cách sử dụng  `route('logout')`  để logout user ra ngoài khỏi hệ thống thì chúng ta có thể sử dụng  `Auth::logout()`

## Ghi nhớ người dùng (Remember me)

Khi mà người dùng request đăng nhập lên hệ thống thì trên server sẽ sinh ra session, đồng thời server sẽ set header trên response trả về client. Mục đích của header này là lưu cookie ở client. Trong cookie sẽ chứa session id tương ứng session trên server. Nhờ đó mà server sẽ biết được ai request ở lần tiếp theo.

Nhưng bạn biết đấy, Cookie thì lâu lâu thì thời gian expire của nó cũng hết, khi đó người dùng sẽ bị logout ra ngoài. Khi chúng ta tích vào ô check box  `Remember me`  khi đăng nhập thì Session ID của người dùng đó sẽ được băm ra rồi lưu trong bảng users tại trường  `remember_token`, cái này sẽ duy trì đăng nhập lâu dài, user sẽ bị thoát ra ngoài hệ thống khi tự logout thủ công  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png)))

Tiếp nhé, tại sao user lại duy trì đăng nhập lâu dài được khi nhấn vào nút  `Remember me`. Khi nhấn vào nút dó thì khi server set header thì sẽ set thêm  `expired date`. Sau khi user tắt browse thì cookie này không bị mất. Mà khi Cookie còn hạn thì sẽ còn tự đăng nhập  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png)))).

# 4.Kết luận

Bài viết trên mình đã giới thiệu cho các bạn những thứ gì cơ bản nhất về authentication trong Laravel, còn rất nhiều hàm mà  `Auth`  hỗ trợ, các bạn có thể xem trong  [documentation](https://laravel.com/docs/5.6/authentication#other-authentication-methods)  của nó nhé. Mọi thắc mắc gì hãy comment phía dưới bài viết của mình nhé.

# 5.Tham khảo

[https://laravel.com/docs/5.6/authentication](https://laravel.com/docs/5.6/authentication)
