


> Written with [StackEdit](https://stackedit.io/).

# Tìm hiểu Eloquent trong Laravel (phần 2): Relationship

This post has been more than  **2 years**  since it was last updated.

Xin chào anh em, lâu lắm rồi mình lại mới ngồi viết series  [Laravel và những điều thú vị](https://viblo.asia/s/laravel-va-nhung-dieu-thu-vi-o754jLreZM6). Thì trong bài viết lần trước về tìm hiểu  [Eloquent](https://viblo.asia/p/tim-hieu-eloquent-trong-laravel-phan-1-eloquent-model-database-QpmleBAo5rd)  , mình đã giới thiệu cho các bạn thế nào là Eloquent và cách sử dụng nó trong project Laravel. Các bạn thấy thú vị khi đọc bài viết đó chứ, bài viết hôm nay mình sẽ đi tìm hiểu về Relationships - nó hỗ trợ rất nhiều khi bạn truy vấn giữa các bảng với nhau. Nào chúng mình cùng bắt tay đi tìm hiểu nào.

![](https://images.viblo.asia/cc28d35e-ecbf-4e33-ba9c-9c51579c45cd.jpg)

# 1 Các mối quan hệ trong Laravel

## 1.1 One To One

Có thể nói đây là mối quan hệ đơn giản nhất trong các mối quan hệ, vì nó rất dễ hiểu ta có một cái này chỉ tưởng ứng với một cái kia duy nhất và ngược lại.

```PHP
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function account()
    {
    	return $this->hasOne(Account::class);
    }
}

```

Khi bạn truy vấn để lấy ra user có role gì thì bạn chỉ cần truy vấn như sau

```PHP
$user = User::findOrFail(1);
$role = $user->account;

```

Ví dụ trên đó là trong trường hợp bảng  `accounts`  nhận khóa ngoại tham chiếu là  `user_id`, nếu như các bạn không đặt theo quy ước khóa ngoại tham chiếu của Laravel quy định:  `<tên_bang_bỏ_s>_id`  thì các bạn muốn lấy được quan hệ đúng các bạn phải thêm các tham số trong lúc định nghĩa quan hệ trong  `model`. Ví dụ khóa ngoại tham chiếu từ bẳng  `users`  đến bảng  `accounts`  là  `user_id`, bây giờ các bạn thích là  `id_user`  thì các bạn phải thêm tham số thứ ba như sau

```PHP
public function account()
{
    return $this->hasOne(Account::class, 'id_user');
}

```

Ngoài ra, nếu bạn muốn relationship sử dụng các column khác với  `id`  trong bảng  `users`  thì chúng ta có thể thêm đối số thứ 3 nhé.

### 1.1.1 Inverse

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Account extends Model
{
    public function user()
    {
    	return $this->belongsTo(User::class);
    }
}

```

Trong ví dụ mình đưa ra ở trên,  `Account`  model sẽ match cột  `user_id`  với giá trị  `id`  của  `User`. Tuy nhiên nếu như khóa ngoại tham chiếu trên  `Account`  không phải là  `user_id`  thì chúng ta có thể tùy chỉnh như sau:

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Account extends Model
{
    public function user()
    {
    	return $this->belongsTo(User::class, 'foreign_key');
    }
}

```

Nếu như đối chiếu với bảng  `users`  không phải là cột  `id`  thì bạn cũng thêm tham số như sau nhé

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Account extends Model
{
    public function user()
    {
    	return $this->belongsTo(User::class, 'foreign_key', 'other_key');
    }
}

```

## 1.2 One To Many

Nhìn cái tên chắc hẳn các bạn cũng đã mường tượng ra như nào rồi đúng không nhi. Có thể nói nôm na như sau: một cái này sẽ có nhiều cái kia, ngược lại một cái kia sẽ thuộc về một cái này  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png)). Nó cũng tương tự như quan hệ One to One ở trên thôi, nào cùng đi vào ví dụ để chúng mình hiểu hơn nhé.

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function posts()
    {
    	return $this->hasMany(Post::class);
    }
}

```

Vậy nhìn vào ví dụ trên ta sẽ thấy một  `user`  sẽ có nhiều  `post`, nếu chúng ta viết như trên thì  `Eloquent`  sẽ tự hiểu khóa ngoại ở bảng  `posts`  đó chính là  `user_id`  nhé. Cũng tương tự như quan hệ  `One to One`  ở trên thì chúng ta cũng có thể truyền được tham số thứ 2 và thứ 3 khi chúng ta không đặt đúng theo convention của Eloquent mà Laravel đã quy định nhé.

```PHP
public function posts()
{
    return $this->hasMany(Post::class, 'foreign_key');
}

public function posts()
{
    return $this->hasMany(Post::class, 'foreign_key', 'local_key');
}

```

Và ví dụ khi chúng ta muốn lấy ra  `user`  có id là  `1`  có những bài  `post`  nào thì chúng ta sẽ dùng câu lệnh sau nhé

```PHP
$posts = User::findOrFail(1)->posts;

```

### 1.2.1 Inverse

Khi chúng ta định nghĩa một bài  `post`  này thuộc về  `user`  nào đã viết thì chúng ta định nghĩa như sau nhé

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}

```

Ở ví dụ trên ,  `Eloquent`  đã match  `user_id`  ở bảng  `posts`  với  `id`  ở bảng  `users`. Tuy nhiên nếu như khóa ngoại ở  `posts`  không phải là  `user_id`  thì chúng ta cũng truyền tham số thứ 2 và nếu như ở bảng  `users`  không lấy trường  `id`  làm khóa chính thì bạn truyền thêm tham số thứ 3 vào function định nghĩa quan hệ nhé.

```PHP
public function user()
{
    return $this->belongsTo(User::class, 'foreign_key', 'other_key');
}

```

## 1.3 Many To Many

Quan hệ này thì trông cái tên cũng có vẻ phức tạp hơn hai quan hệ trên mình đề cập, thôi thì cứ vào ví dụ cho nó dễ hiểu nhé. Bài toán đặt ra là như này một  `product`  thì sẽ nằm trong có nhiều  `order`, ngược lại một  `order`  có thể có nhiều  `product`. Chúng mình cần có 3 bảng để biểu diễn cho quan hệ này:  `orders`,  `products`,  `order_product`. Bây giờ mình sẽ đi định nghĩa quan hệ nhé.

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    public function products()
    {
        return $this->belongsToMany(Product::class);
    }
}

```

Khi chúng ta muốn lấy ra dữ liệu một  `order`  xem có bao nhiêu  `product`  thì chúng ta sẽ viết như sau:

```PHP
$products = Order::findOrFail(1)->products;

```

Các bạn chú ý, nếu như bảng trung gian trong trường hợp này nó sẽ đặt theo quy ước của Laravel đó chính là  `order_product`, đặt theo alpha nhé. Nếu như trong trường hợp bảng trung gian có tên là khác vd như  `product_order`  thì các bạn phải thêm tham số thứ 2 trong function định nghĩa quan hệ nhé

```PHP
public function products()
{
    return $this->belongsToMany(Product::class, 'product_order');
}

```

Và nếu như hai cột liên kết khác với quy ước của Laravel thì chúng ta có thể thêm tham số thứ 3 và thứ 4. Trong đó tham số thứ 3 là tên khóa ngoại của model mà chúng ta đang định nghĩa quan hệ, tham số thứ 4 là tên khóa ngoại của model mà chúng ta đang joining

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    public function products()
    {
        return $this->belongsToMany(Product::class, 'product_order', 'order_id', 'product_id');
    }
}

```

### 1.3.1 Inverse

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    public function orders()
    {
        return $this->belongsToMany(Order::class);
    }
}

```

Tương tự bạn cũng có thể truyền tham số thứ 2, 3, 4 vào trong hàm định nghĩa quan hệ như sau

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    public function orders()
    {
        return $this->belongsToMany(Order::class, 'product_order', 'product_id', 'order_id');
    }
}

```

### 1.3.2 Truy cập vào bảng trung gian của quan hệ Many to Many

Như mình đã nói ở trên thì khi làm việc với quan hệ  `Many to Many`  thì chúng ta cần phải định nghĩa ra bảng trung gian. Eloquent cũng cấp một vài cách để tương tác với bảng trung gian đó. Chúng ta cùng xem qua ví dụ sau đây nhé

```PHP
$order = Order::findOrFail(1);

foreach ($order->products as $item) {
        // Truy cập vào các trường trong bảng trung gian
        echo $item->pivot->product_id;
}

```

Các bạn chú ý nhé theo mặc định trong Laravel, bảng trung gian  `product_order`  này sẽ chỉ lấy ra được các trường  `order_id, product_id, created_at, updated_at`. Nếu như trong bảng trung gian này các bạn định nghĩa thêm trường  `amount`  chẳng hạn, nếu muốn lấy ra được trong khi truy vấn các bạn phải dùng cú phap  `withPivot('column1', 'column2')`

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    public function products()
    {
        return $this->belongsToMany(Product::class)->withPivot('amount');
    }
}

```

Nếu bạn muốn khi thêm bản ghi mới trong bảng trung gian này thì 2 trường  `created_at, updated_at`  tự động fill giá trị thì các bạn định nghĩa thêm  `withTimestamps()`

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    public function products()
    {
        return $this->belongsToMany(Product::class)->withTimestamps();
    }
}

```

### 1.3.3 Customizing thuộc tính pivot

Nếu như các bạn đã quá nhàm chán khi chúng ta muốn truy cập vào bảng tạm thông qua thuộc tính  `pivot`  thì giờ đây chúng ta có thể thay đổi cái tên đó bằng cứ pháp như sau

```PHP
public function products()
{
    return $this->belongsToMany(Product::class)->as('detail')->withTimestamps();
}

```

Lúc đó chúng ta sẽ truy cập được như sau

```PHP
$order = Order::findOrFail(1);

foreach ($order->products as $item) {
        // Truy cập vào các trường trong bảng trung gian
        echo $item->detail->product_id;
}

```

### 1.3.4 Filter quan hệ thông qua bảng trung gian

Chúng ta có thể filter được kết quả thộng qua bảng trung gian. Ví dụ như chúng ta muốn lấy ra nhưng order mà có detail  `amount`  lớn hơn 3 chẳng hạn thì chúng ta sẽ định nghĩa trong hàm quan hệ như sau

```PHP
public function products()
{
    return $this->belongsToMany(Product::class)->wherePivot('amount', '>', 3);
}

// hoặc

public function products()
{
    return $this->belongsToMany(Product::class)->wherePivotIn('amount', [2,5]);
}

```

### 1.3.5 Custom bảng trung gian

Nếu như chúng ta muốn quan hệ  `Many to Many`  dùng theo một model mà ta tự định nghĩa nào đó, không theo quy tắc mà Laravel định nghĩa ra thì chúng ta sẽ làm như sau

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    public function products()
    {
        return $this->belongsToMany(Product::class)->using(Detail::class);
    }
}

```

Và khi định nghĩa  `Detail`  model thì chúng ta nhớ extend  `Illuminate\Database\Eloquent\Relations\Pivot`  vào nhé

```PHP
<?php

namespace App;

use Illuminate\Database\Eloquent\Relations\Pivot;

class Detail extends Pivot
{
    //
}

```

Thật là dễ dàng đúng không nào  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png)

## 1.4 Has One Through

Đây là quan hệ kết nối các model thông qua một model trung gian. Ví dụ luôn cho dễ hiểu nè

```PHP
users: id, supplier_id
suppliers: id
history: id, user_id

```

Chúng ta miêu tả quan hệ như sau:  `Supplier`  model có thể truy cập vào  `History`  thông qua  `User`  model.

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Supplier extends Model
{
    public function userHistory()
    {
        return $this->hasOneThrough(History::class, User::class);
    }
}

```

Tham số đầu tiên đó chính là model mà chúng ta muốn truy cập đến. Tham số thứ 2 là tên của model trung gian. Và cũng như vậy nếu như chúng ta muốn custom key của quan hệ thì chúng ta phải truyền tham số thứ 3, 4 vào hàm định nghĩa quan hệ nhé

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Supplier extends Model
{
    public function userHistory()
    {
        return $this->hasOneThrough(
                History::class,
                User::class,
                'supplier_id', // khóa ngoại ở bảng trung gian users
                'user_id', // khóa ngoại ở bảng đích histories
                'id', // khóa chính ở bảng suppliers
                'id' // khóa chính ở bảng users
        );
    }
}

```

Và trong model  `User`  và model  `History`  ta cũng định nghĩa bình thường như sau

```PHP
// User.php
namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function supplier()
    {
         return $this->belongsTo(Supplier::class);
    }
}

```

```PHP
// Supplier.php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Supplier extends Model
{
    public function user()
    {
         return $this->hasOne(User::class);
    }
}

```

## 1.5 Has Many Through

Đọc cái tên quan hệ các bạn cũng có thể thấy được là các bảng quan hệ với nhau sẽ phải thông qua một cái gì đó đúng không. Đúng rồi đấy, ví dụ chúng ta có 3 bảng như sau

```PHP
users: id, name, team_id.
teams: id, name.
goals: id, user_id, number_of_goals.

```

Ta thấy như sau:

-   `Team`  có nhiều  `User`
-   `User`  có nhiều  `Goal`

Chúng ta không thể lưu trực tiếp  `goal_id`  trong bảng  `teams`  được bởi vì chúng ta đã lưu  `team_id`  ở trong bảng  `users`. Vậy bây giờ câu hỏi đặt ra là có bao nhiêu  `Goal`  của một  `Team`, chúng ta phải thông qua (through)  `User`  model thì chúng ta mới biết được. Chúng ta sẽ định nghĩa như sau:

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Team extends Model
{
    public function goals()
    {
        return $this->hasManyThrough(Goal::class, User::class);
    }
}

```

Hoặc cụ thể hơn nữa các bạn sẽ truyền các tham số thứ 3, 4, 5,6 vào trong hàm định nghĩa quan hệ như sau

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Team extends Model
{
    public function goals()
    {
        return $this->hasManyThrough(
               Goal::class,
               User::class
               'team_id', // khóa ngoại trên bảng users
               'user_id', // khóa ngoại trên bảng goals
               'id', // khóa chính trên bảng teams
               'id' // khóa chính trên bảng users
        );
    }
}

```

Ở  `User`  model chúng ta sẽ định nghĩa như sau

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function goals()
    {
        return $this->hasMany(Goal::class);
    }

    public function team()
    {
        return $this->belongsTo(Team::class);
    }
}

```

`Goal`  model

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Goal extends Model
{
   public function user()
    {
         return $this->belongsTo(User::class);
    }
}

```

Và khi chúng ta muốn truy vấn thì chúng ta vẫn làm như bình thường thôi

```PHP
$team = Team::findOrFail(1);
$goals = $team->goals()->get();

...

@foreach($goals as $goal)
    <p>{{ $goal->number_of_goals }}</p>
@endforeach

```

## 1.6 Polymorphic Relationships

Mình sẽ lấy ví dụ một chút nhé. Các bạn thử tưởng tượng rằng bây giờ chúng ta có bảng  `posts`  và bảng  `pages`. Bây giờ ta muốn tạo chức năng comment cho post hoặc comment cho page thì chúng ta sẽ lại phải tạo thêm bảng  `posts_comments`  để lưu các comment của  `posts`  và  `pages_comments`  để lưu các comment của bảng  `pages`. Điều đó gây phức tạp và chúng ta phải tạo rất nhiều bảng. Vậy Laravel đã hỗ trợ chúng ta bằng quan hệ  `Polymorphic`  khiến giản lược và tiện lợi hơn với chúng ta rất nhiều.

### 1.6.1 One to One

Quan hệ này nó cũng giống như quan hệ  `One to One`  cơ bản ở trên. Mình sẽ lấy ví dụ để các bạn hiểu thêm nhé. Bây giờ chúng ta có các bảng sau:

```PHP
posts: id, name
videos: id, name
images: id, url, imageable_id, imageable_type

```

Mình giải thích một chút nhé:

-   Tại sao lại sinh ra 2 trường  `imageable_id`  và  `imageable_type`, thì theo convention của Laravel thì bảng lưu trung gian sẽ bắt buộc phải có 2 trường id và type nhưng để rõ ràng hơn thì sẽ lưu thêm tiền tố  `tên_bảng_bỏ_s +able_`.
-   `imageable_id`  là lưu id của các bài post hoặc video.
-   `imageable_type`  là lưu tên class model của Post và User. Ví dụ như  `App\Post`  hoặc  `App\Video`.

Chúng ta sẽ định nghĩa các model trong ví dụ trên như sau

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Image extends Model
{
    public function imageable()
    {
        return $this->morphTo();
    }
}

class Post extends Model
{
    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}

class Video extends Model
{
    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}

```

Và trong khi truy vấn dữ liệu chúng ta cứ lấy model Post hoặc Video trỏ đến image là ok.

```PHP
$video = Video::find(1);

$image = $video->image;

```

### 1.6.2 One to Many

Lấy luôn ví dụ mở bài cho hiểu này  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png)). Chúng ta có posts và pages, mỗi loại trên đều có rất nhiều comment.

```PHP
posts: id, title, content
pages: id, body
comments: id, commentable_id, commentable_type, date

```

Chúng ta sẽ tạo các model như sau

```PHP
// Post.php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}

```

```PHP
// Page.php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Page extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment, 'commentable');
    }
}

```

```PHP
// Comment.php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    public function commentable()
    {
        return $this->morphTo();
    }
}

```

Chúng ta đã định nghĩa các quan hệ trên bằng các phương thức  `morphMany()`  và  `morphTo`  giúp chúng ta tạo quan hệ  `polymorphic`. Cả  `Page`  và  `Post`  đều có phương thức  `comments`  nó sẽ trả về đúng comment theo model  `Comment`.  `Comment`  model có phương thức  `commentable()`  nó sẽ trả về kết quả phương thức  `morphTo()`  chỉ ra rằng class này có liên quan đến những model khác.

Để truy vấn thì chúng ta sẽ sử dụng phương thức  `comments`  được định nghĩa trong model

```PHP
$page = Page::find(3);

foreach($page->comment as $comment)
{

}

```

Ez đúng không  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png))

### 1.6.3 Custom polymorphic type

Như các bạn thấy Laravel sẽ lưu trương  `_type`  đó là tên đầy đủ của class model. Như ví dụ đề cập trên thì trường  `commentable_type`  sẽ lưu  `App\Post`  và  `App\Page`  khi thực hiện lưu. Tuy nhiên chúng ta có thể custom được những cái tên dài dòng đó bằng cách sử dụng  `Illuminate\Database\Eloquent\Relations\Relation`  và đăng ký nó trong  `boot()`  của  `AppServiceProvider.php`.

```PHP
use Illuminate\Database\Eloquent\Relations\Relation;

Relation::morphMap([
    'posts' => 'App\Post',
    'pages' => 'App\Page',
]);

```

# 2 Truy vấn trong quan hệ

## 2.1 has | whereHas

Đôi khi chúng ta muốn giới hạn kết quả bản ghi lấy ra, ví dụ như ta muốn lấy ra những bài post mà có comment, không có comment không lấy.

```PHP
$posts = Post::has('comments)->get();

```

Nếu như ví dụ còn có quan hệ vote comment thì chúng ta muốn lấy ra bài post nào có comment được vote thì chúng ta sẽ lấy như sau

```PHP
$posts = Post::has('comments.votes)->get();

```

Hoặc nếu như chúng ta muốn lấy ra những bài post phải có comment và kèm theo điều kiện  `content`  của comment đó phải bắt đầu theo một điều kiện nào đó

```PHP
$posts = Post::whereHas('comments', function ($query) {
           $query->where('content', 'like', '%gì gì đó%');
});

```

## 2.2 doesntHave | orDoesntHave

Ngược lại 2 phương thức ở trên đó chính là  `doesntHave`  và  `whereDoesntHave`. Ví dụ chúng ta muốn lấy ra các bài post mà có không có comment nào  `$posts = Post::doesntHave('comments)->get();`

Hoặc chúng ta muốn lấy ra những bài post mà không có comment nào và kèm theo một điều kiện nữa chẳng hạn

```PHP
$posts = Post::whereDoesntHave('comments', function ($query) {
    $query->where('content', 'like', 'foo%');
})->get();

```

## 2.3 Counting Related Models

Như các bạn biết đấy chẳng hạn mà chúng ta chỉ muốn lấy ra số comment của bài post thì Laravel cung cấp cho ta một phương thức  `withCount()`, nó sẽ thêm thuộc tính  `{relation}_count`  vào kết quả trả về cho mình

```PHP
$post = Post::find(1)->withCount('comments)->get();
// Lấy ra số lượng comment của bài viết
$comments = $post->comment_count;

```

## 2.4 Eager Loading

Laravel mặc định ORM sẽ ở chế độ  `lazy`  khi load lên tất cả các model quan hệ. Mình có ví dụ sau nhé

```PHP
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->user->id;
}

```

Ví dụ trên thể hiện 2 điều

-   Một câu truy vấn để lấy ra tất cả các bản ghi từ bảng posts
-   Bới mỗi bản ghi post chúng ta lại truy vấn để lấy ra tên tác giả bài viết từ mối quan hệ. Vậy giả dụ mà có 100 bài viết thì sẽ phải truy vấn 100 lần để lấy ra tên tác giả mỗi bài viết + 1 lần truy vấn ở đầu để lấy ra tất cả các bài viết.

Hướng giải quyết, Laravel cũng cấp cho ta 2 phương thức  `with()`  và  `load()`

```PHP
$posts = Post::with('user')->get();
foreach ($posts as $post) {
    echo $post->user->id;
}

```

```PHP
$posts = Post::all();
$posts->load('user');
foreach ($posts as $post) {
    echo $post->user->id;
}

```

`Eager Loading`  còn được khai báo trong model

```PHP
namespace App;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    protected $with = ['user'];

    /**
     * Get the author that wrote the book.
     */
    public function author()
    {
        return $this->belongsTo(User::class);
    }
}

```

Đôi khi đặt mặc định trong model như này nhiều trường hợp chúng ta không muốn load user ra thì laravel cung cấp cho ta phương thức  `without()`

```PHP
$posts = Post::without('user)->get();

```

Giả sử chúng ta muốn lấy ra những bài post mà có tác giả có  `id`  lớn hơn 3 chăng hạn thì chúng ta sẽ làm như sau

```PHP
$posts = Post::with(['user' => function ($query) {
        $query->where('id', '>', 3);
}])->get();

```

# 3.Phương thức Save, Create trong Relationship

## 3.1 Save

Giả sử trong một bài post người dùng comment dưới bài post đó, khi submit gửi yêu cầu comment bình thường sẽ làm như sau

```PHP
public function store(Post $post, Request $request)
{
        $content = $request->content;
        
        $comment->create([
                'post_id' => $post->id,
                'content' => $content,
        ]);
        
        return $response(['OK'], 204);
}

```

Bây giờ ta sẽ dùng phương thức  `save`

```PHP
public function store(Post $post, Request $request)
{
        $content = $request->content;
        $comment = new Comment(['content' => $content]);
        $post->comments()->save($content);
        
        return $response(['OK'], 204);
}

```

Nếu như chúng ta muốn save đồng thời 1 lúc nhiều bản ghi thì Laravel hỗ trợ cho chúng ta phương thức  `saveMany`

## 3.2 Create

Phương thức  `create`  sẽ tự động fill  `post_id`  và lưu vào bảng  `comments`  khi chúng ta lưu comment mới.

```PHP
public function store(Post $post, Request $request)
{
        $content = $request->content;
    
        $post->comments()->create(['content' => $content]);
        
        return $response(['OK'], 204);
}

```

Tương tự ta cũng tạo được nhiều comment bằng phương thức  `createMany`.

## 3.3 attach | detach

Laravel hỗ trợ 2 phương thức  `attach`  và  `detach`  để dùng cho việc thêm bản ghi tự động vào bảng trung gian. Giả sử chúng ta có ví dụ như sau: một bài  `post`  thì sẽ có nhiều  `tag`, và một  `tag`  sẽ thuộc về nhiều bài  `post`. Bảng trung gian  `post_tag`  sẽ có các trường như sau : id, posi_id, tag_id.

```PHP
public function (Post $post, Request $request) 
{
        $tag = Tag::create(['name' => $request->nameTag]);
        $post->tags()->attach($tag->id);
}


```

Phương thức  `detach`  cũng tương tự dùng để xóa đi bản ghi trong bảng trung gian.

## 3.4 Syncing Associations

Phương thức  `sync`  goups cho ta loại bỏ được những phần tử không có mặt trong array. Mình lấy ví dụ như này nhé. Ví dụ như này nhé

```PHP
$tagIDs = [1, 2, 3] // ban đầu nó là [1, 2, 3, 4]
$post->tags()->sync($tagIDs);
thì lúc này trong bảng trung gian bản ghi có id bằng 4 đã bị xóa đi

```

Phương thức này rất tiện lợi cho việc xóa giữa quan hệ có bảng trung gian.

# 4.Kết luận

Vậy qua bài tìm hiểu của mình về các quan hệ trong ORM cũng đã phần nào giúp các bạn hiểu thêm một chút về chúng. Mong rằng những gì mình chia sẻ sẽ giúp ích cho các bạn. Cảm ơn các bạn đã đọc bài chia sẻ của mình.

# 5. Tham khảo

[https://laravel.com/docs/5.8/eloquent-relationships#introduction](https://laravel.com/docs/5.8/eloquent-relationships#introduction)
