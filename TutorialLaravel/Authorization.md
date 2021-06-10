


> Written with [StackEdit](https://stackedit.io/).

# Tìm hiểu Authorization trong Laravel

This post has been more than  **2 years**  since it was last updated.

Xin chào anh em , gặp lại anh em trong series  **Laravel và những điều thú vị**  lần này, mình sẽ chia sẻ phần kiến thức  **Authorization**. Như các bạn biết đấy, ở bài trước mình cũng đã chia sẻ cho các bạn về phần kiến thức  [Authentication](https://viblo.asia/p/tim-hieu-ve-authentication-trong-laravel-Ljy5VoG3Kra)  - tức là phần login/logout/register trong hệ thống. Lấy một ví dụ như khi chúng ta đăng nhập thành công mà admin lại cho chúng ta một số quyền nhất định. Ví dụ như user này có quyền edit - delete, user kia lại chỉ có quyền xem thôi mà không được phép thay dổi bất cứ gì cả.  `Authorization`  sẽ giải quyết vấn đè này cho chúng ta.

# 1. Gate

Như cũng giới thiệu qua ở trên là  `Authorization`  là việc điều khiển việc được truy cập của người dùng. Hay nói một cách dễ hiểu hơn đó chính là phân quyền trong hệ thống. Thường thường thì chúng ta thấy, nếu làm một trang web quy mô nhỏ chỉ có 2 quyền đó là admin và user thì chúng ta chỉ cần viết middleware là xong. Nhưng do nhu cầu của hệ thống cần phân quyền một cách phức tạp hơn nên Laravel sinh ra  `Authorization`  để hỗ trợ chúng ta trong việc phân quyền đó. Có 2 cách để  `authorize`  trong Laravel:  `Gates`  và  `Policies`. Đầu tiên chúng ta sẽ đi tìm hiểu về  `Gates`  trong Laravel

## Define

`Gate`  theo mình hiểu thì hầu hết dùng trong việc chúng ta authorize (xác thực) các action riêng lẻ. Ta sẽ định nghĩa các rằng buộc trong method  `boot`  của  `AuthServiceProvider`  và sử dụng facade  `Gate`. Do là chúng ta xác thực xem người dùng này có được làm hành động hay truy cập vào trang này trang nọ trong hệ thống của chúng ta hay không nên đối số đầu tiên trong hàm rằng buộc sẽ là một user instance, còn đối số thứ hai sẽ là một instance khác. Ví dụ như người dùng sẽ có quyền thay đổi cập nhật comment của mình chẳng hạn

```PHP
<?php

namespace App\Providers;

use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use Auth;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy',
    ];

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();
       
        Gate::define('edit-comment', function ($user, $commment) {
            return $user->id == $comment->user_id;
        });
    }
}

```

`Gate::define()`  sẽ định nghĩa một điều kiện giữa người dùng và thông tin commment, tức là nếu nó trả về  `true`  thì người dùng được phép sửa comment và ngược lại thì không được sửa.

## alows - denies - forUser

  
Và để cho phép authorized thì trong controller chúng ta sẽ dùng câu lệnh sau :

```PHP
public function index()
{
    $comment = Comment::findOrFail(1); // comment có id = 1 có user_id = 1
    if (Gate::allows('edit-comment', $comment)) {
        echo "Ban co quyen chinh sua comment";
    } else {
        echo "Ban khong co quyen chinh sua comment";
    }
}

```

Trên chỉ là một ví dụ minh họa cho việc xem bạn có quyền chỉnh sửa comment không. Các bạn sẽ không phải truyền vào argument $user đầu tiên vào, Laravel sẽ tự động truyền user đang truy cập hệ thống vào trong Gate Closure.  `Gate::denies()`  ngược lại với  `Gate::allows()`.  
Nếu bạn muốn xem một người dùng bất kỳ có được xác thực để thực hiện 1 action trong hệ thống của các bạn hay không thì các bạn có thể dùng phương thức  `forUser`  trong  `Gate`  facade

```PHP
if (Gate::forUser($user)->allows('edit-comment', $comment)) {
    echo "Người dùng này được phép chỉnh sửa comment";
}

if (Gate::forUser($user)->denies('edit-comment', $comment)) {
    echo "Người dùng này không được phép chỉnh sửa comment";
}

```

## before

Ở phiên bản Laravel 5.6 thì chúng ta có thêm  `Gate::before()`  để ta có thể cấp tất cả các quyền cho một người dùng nào đó trước khi các authorization được check. Thường thường thì đó chính là người quản trị website có tất cả các quyền trong hệ thống.

```PHP
Gate::before(function ($user, $ability) {
    if ($user->isSuperAdmin()) {
        return true;
    }
});

```

Và bạn có thể sử dụng  `Gate::after()`  để định nghĩa 1 callback để được thực thi sau mỗi lẩn kiểm tra authorization. Tuy nhiên, các bạn sẽ không thay đổi được kết quả của sự kiểm tra phân quyền.

```PHP
Gate::after(function ($user, $ability, $result, $arguments) {
    //
});

```

# 2. Policies

`Policies`  là các class quản lý logic trong phân quyền liên quan đến Model hoặc tài nguyên nào đó. Ví dụ, các bạn có model Comment và một policy CommentPolicy để phân quyền xem người dùng nào sẽ được chỉnh sửa hay cập nhật comment của mình.  
Các bạn có thể tạo ra Policy đơn giản bằng command line

```PHP
php artisan make:policy CommentPolicy

```

Câu lệnh trên sẽ sinh ra một policy rỗng , nếu bạn muốn sinh ra một CRUD policy thì chúng ta cần thêm tham số  `--model=Comment`  khi thực thi câu lệnh artisan

```PHP
php artisan make:policy CommentPolicy --model=Comment

```

Nhìn trông có vẻ giống cách tạo resource controller đúng không các bạn. Vì thế mà người ta ví Gate và Policy như là Route và Controller.Mình sẽ nói rõ phần này hơn nhé, khi chúng ta sử dụng

```PHP
Gate::resource('comments', 'CommentPolicy');

```

thì với phương thức resource như vậy thì nó sẽ định nghĩa ra thủ công như sau:

```PHP
Gate::define('comments.view', 'CommentPolicy@view');
Gate::define('comments.create', 'CommentPolicy@create');
Gate::define('comments.update', 'CommentPolicy@update');
Gate::define('comments.delete', 'CommentPolicy@delete');

```

## Register Policy

Tiếp theo để đăng ký 1 policy thì ta sẽ đăng ký trong file  `AuthServiceProvider.php`. Đăng ký một policy sẽ chỉ dẫn cho Laravel biết policy nào sẽ được sử dụng để phân quyền hành động cho model nào:

```PHP
<?php

namespace App\Providers;

use App\Comment;
use App\Policies\CommentPolicy;
use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        Comment::class => CommentPolicy::class,
    ];

    /**
     * Register any application authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();
    }
}

```

## Writing Policy

Nếu trên mình đã nói  `Gate`  nó dùng để authorize những hành động riêng lẻ. Bây giờ viết trong Policy cũng không khác gì cả nhé, thay vì viết

```PHP
Gate::define('edit-comment', function ($user, $commment) {
            return $user->id == $comment->user_id;
});

```

ở trong function  `boot()`  bây giờ chúng ta sẽ viết trong policy như sau

```PHP
<?php

namespace App\Policies;

use App\User;
use App\Comment;

class PostPolicy
{
    /**
     * Determine if the given post can be updated by the user.
     * @return  bool
     */
    public function update(User $user, Comment $comment)
    {
        return $user->id === $comment->user_id;
    }
}

```

Các bạn chú ý phương thức  `create`  trong policy chỉ nhận đối số đầu tiên là Model user chú không nhận thêm đối số thứ hai.

```PHP
public function create(User $user)
{
    //
}

```

## Policy Filters

Đôi với một người dùng nhất định trong hệ thống giả dụ như admin, người đó có tất cả các quyền mà không cần trải qua các authorize riêng lẻ. Vì vậy chúng ta sẽ định nghĩa một phương thức  `before`  trong policy. Phương thức này sẽ thực thi trước bất kỳ phương thức nào trong policy.

```PHP
public function before($user, $ability)
{
    if ($user->isAdmin()) {
        return true;
    }
}

```

Nếu bạn muốn một user không được phép thực hiện bất kỳ gì bạn chỉ cần trả về false trong phương thức  `before`. Nếu giá trị được trả về là  `null`, việc cấp quyền sẽ được tiếp tục diễn ra trong phương thức policy.

## Authorizing Actions Using Policy

  
Rồi tiếp theo để thực hiện hành động authorize sử dụng Policy thế nào? Thì chúng ta sẽ thông qua 2 phương thức  `can`  và  `cant`  được tạo sẵn trong Model User. Phương thức  `can`  nhận hành động bạn muốn cấp phép và model liên quan và ngược lại là phương thức  `cant`.

```PHP
if ($user->can('update', $comment)) {
    //
}

```

Các bạn chú ý , function  `create`  trong policy không yêu cầu tham số thứ 2 là instance của model nào. Trong trường hợp này, chúng ta có thể pass tên class vào phương thức  `can`. Tên lớp sẽ được sử dụng để xác định Policy nào sẽ sử dụng khi authorize

```PHP
use App\Comment;

if ($user->can('create', Comment::class)) {

}

```

## Via Middleware

Laravel thêm một middleware có thể authorize action trước khi có request gửi đến ngay cả đến với routes hoặc controller. Mặc định, middleware  `Illuminate\Auth\Middleware\Authorize`  được gán key  `can`  trong class  `App\Http\Kernel`

```PHP
use App\Post;

Route::put('/post/{post}', function (Post $post) {
    // The current user may update the post...
})->middleware('can:update,post');

```

Chú ý, vấn là phương thức  `create`  không require tham số thứ hai. Trong trường hợp này chúng ta sẽ pass tên class vào middleware. Tên class sẽ quyết định policy nào để sử dụng khi authorize hành động.

```PHP
Route::post('/post', function () {
    
})->middleware('can:create,App\Post');

```

## Via Controller Helpers

Như ta đã biết, ngoài phương thức  `can`  ra thì Laravel còn hỗ trợ phương thức  `authorize`  do controller làm việc của chúng ta kế thừa từ  `App\Http\Controlers\Controller`

```PHP
<?php

namespace App\Http\Controllers;

use App\Comment;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class CommentController extends Controller
{
    
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function update(Request $request, Comment $comment)
    {
        $this->authorize('update', $comment);
        // Người dùng có thể update commmet của mình
    }
}

```

Ta lại chú ý đến phương thức  `create`  trong policy

```PHP
<?php

namespace App\Http\Controllers;

use App\Comment;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class CommentController extends Controller
{
    
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function update(Request $request)
    {
        $this->authorize('create', Comment::class);
        // Người dùng có thể create commnet của mình
    }
}

```

## Via Blade Template

Khi viết Blade template, bạn chỉ muốn những người dùng chỉ có thể xem được những hành động mà người dùng có quyền truy cập thì chúng ta sẽ sử dụng  `@can`  và  `@cannot`.

```PHP
@can('update', $post)
    // hiển thị button edit
@endcan

@cannot('update', $post)
    // Khong hiển thị button edit
@endcannot

```

Chú ý phương thức  `create`

```PHP
@can('create', Post::class)
    
@endcan

@cannot('create', Post::class)
    
@endcannot

```

# 3. Compare Authentication - Authorization

Từ những tìm hiểu của mình ở trên thì mình rút ra được vài điều về sự khác nhau giữa Authentication và Authorization như sau. ak nói về điểm giống nhau trước  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png))) Chúng đều cho phép người dùng có hoặc không thực thi một hành động trong hệ thống(cả 2 đều sử dụng được middleware). Cón sự khác nhau là bạn cứ tưởng tượng khi bạn vào một ngôi nhà, nếu bạn là chủ của ngôi nhà đó thì phải có chìa khóa để mở cửa cổng ra để vào nhà(Authentication). Còn vào được trong ngồi nhà rồi, có các phòng thì nếu bạn muốn vào phòng ngủ của bố mẹ thì bạn phải được sự đồng ý của bố mẹ. Có những phòng bạn được vào , có những phòng bạn không được phép vào(Authorization).

# 4. Kết luận

Qua nhưng thứ mình tìm hiểu và chia sẻ trên, mong phần nào bạn cũng hiểu được về các khái niệm về  `Gate`  hay  `Policy`  , và cách chúng hoạt động ra sao. Cảm ơn các bạn đã đọc những dòng chia sẻ của mình.

# 5. Tham khảo

[https://laravel.com/docs/5.6/authorization](https://laravel.com/docs/5.6/authorization)
