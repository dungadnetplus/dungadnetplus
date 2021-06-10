


> Written with [StackEdit](https://stackedit.io/).

# Giới thiệu về Unit Testing trong Laravel

# Lời mở đầu

Xin chào mọi người, mình lại trở lại đây. Mình thì từ trước tới nay join cũng mấy dự án của công ty rồi, từ những dự án không cài CI/CD đến dự án tích hợp CI/CD vào project. Một ngày đẹp trời join vào một dự án đang làm bắt đầu phase 2 rồi, tự dưng mình có sửa 1 chút api thôi mà lúc push code lên CI báo lỗi, mở CI ra để check thì thấy không có lỗi convention nào, nhưng lại báo lỗi fail test. Lúc đó bối rối quá, viết test trong Laravel là cái gì đấy, vì từ trước tới nay các dự án mình làm thì thường không viết test nên cũng chả quan tâm viết test làm gì. Và công cuộc viết test bắt đầu  ![😦](https://twemoji.maxcdn.com/2/72x72/1f626.png)  Những rồi mình nhận ra rằng việc viết test là một khâu khá quan trọng, trước đây mình có xem việc viết test này là không cần thiết , code chức năng sao cho chạy được cái đã.

# PHPUnit là gì ?

**PHPUnit**  là một trong những package unit testing được biết đến nhiều nhất và tối ưu hóa cao, nó được coi là sự lựa chọn hàng đầu của rất nhiều các lập trình viên cho việc khắc phục những lỗ hổng bảo mật khác nhau của ứng dụng. Chức năng chính của nó là để thực thi tất cả những unit testing trong ứng dụng. Nó hỗ trợ phần lớn hầu hết những PHP framework bao gồm Laravel.

**PHPUnit**  được phát triển với nhiều các  `assertion`  đơn giản, thêm nữa nó đưa ra những kết quả tối ưu khi bạn testing từng function trong code của bạn. Nhưng quá trình test controller, model và form validation có thể phức tập hơn bạn tưởng tượng đấy.

# Unit Test trong Laravel

Laravel là một framework phổ biến hiện nay được nhiều các lập trình viên sử dụng. Từ góc nhìn testing, nó cũng được biết đến là có kèm theo testing khi cài framework sử dụng. Trong Laravel có 2 cách để test, thứ nhất là với  `Unit testing`, thứ hai là  `Feature testing`. Unit test cho phép chúng ta test các class model, controller,... Mục tiêu của  `Unit test`  là kiểm tra tính đúng đẵn trong các xử lý của từng đơn vị mã nguồn.Còn Feature testing cho phép bạn test api, test kết quả trả về, test chức năng, hiệu năng cũng như khả năng chịu tải của ứng dụng.

Nếu bạn đa cài được project init Laravel rồi thì bạn có thể nhìn thấy 2 thư mục con có sẵn. Đó chính là thư mục  `tests`  có 2 thư mục con đó chính là  `Unit`  và  `Feature`  dùng cho việc viết test tại đây.

Để rõ hơn thì mình sẽ ví dụ cho việc viết unit test trong phân tiếp theo nhé.

# Ví dụ unit test trong Laravel

Rồi ok chúng mình sẽ đến với phần ví dụ cho việc viết unit test trong project sử dụng Laravel nhé. Ở trong mỗi project Laravel thì đã có sẵn  `phpunit/phpunit`  rồi nên bạn chỉ việc sử dụng nó thôi. Và điều đầu tiên trong việc viết test là phải tạo ra những test class đã. Những class này được lưu trữ trong thư mục  `tests`  của project Laravel. Và chúng có quy tắc đặt tên nhất định để PHPUnit có thể tìm thấy từng file test này và chạy. Ví dụ bạn test model bạn có thể đặt là  `UserTest.php`. Tiếp đến các bạn sẽ thấy file  `TestCase.php`  nó nằm cùng cấp thư mục  `Unit`  và  `Feature`. Nó cơ bản là một file để set up môi trường Laravel và những feature bên trong test của chúng ta. Ý các bạn hiểu là chúng ta sẽ dùng được các function hộ trợ chúng ta viết test hơn, còn các function đó là gì mình sẽ giới thiệu với các bạn trong quá trình làm ví dụ nhé.

Để tạo được một file test các bạn sẽ dùng lệnh

```PHP
php artisan make:test

```

Nào chúng ta thử tạo 1 file test xem nó có hình dạng như nào nhé

```PHP
php artisan make:test UserTest --unit

```

Và Laravel sẽ generate cho chúng ta 1 file có nội dụng như sau:

```PHP
<?php

namespace Tests\Unit;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserTest extends TestCase
{
    /**
     * A basic unit test example.
     *
     * @return void
     */
    public function testExample()
    {
        $this->assertTrue(true);
    }
}


```

Tên phương thức test phải đặt có ý nghĩa sao cho bạn muốn test cái gì, có 2 cách đặt tên cho hàm test

-   test_store_survey()
-   testStoreSurvey()

Và PHPUnit không thể chạy các test với các phương thức  `protected`  và  `private`  , nên các bạn phải để modify của function test là public. Chúng ta cũng có thể dễ dàng tìm ra được file  `phpunit.xml`, PHPUnit sẽ tự xác định vị trí cho tên file được đặt tên trong  `phpunit.xml`  hoặc  `phpunit.xml.dist`  ở trong thư mục thực thi hiện tại. Ở trong file này, chúng ta có thể cấu hình cụ thể sự thực thi cho test của mình.

Mà trước hết chúng mình sẽ tìm qua một chút những hàm  `mong đợi`  kết quả của chúng ta xem sau khi chay test có ra dúng như thế không nhé. Bạn có thể sử dụng 7 hàm PHPUnit assertion để mong muốn đầu ra của bạn như thế nào

1.  assertTrue()
2.  assertFalse()
3.  assertEquals()
4.  assertNull()
5.  assertContains()
6.  assertCount()
7.  assertEmpty()

**assertTrue() và assertFalse()**

Hai hàm này ý muốn cho phép ta mong muốn sau một quá trình chạy test xong kết quả trả về là true hay false

```PHP
<?php

namespace Tests\Unit;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;
use App\User;

class UserTest extends TestCase
{
    /**
     * A basic unit test example.
     *
     * @return void
     */
    public function test_has_user()
    {
        $users = new User(['Lena', 'Misa', 'Leona']);
        $this->assertTrue($users->has('Lena'));
        $this->assertFalse($user->has('Minh Minh'));
    }
}


```

Để chạy phpunit các bạn chạy với câu lệnh sau

```PHP
./vendor/bin/phpunit

```

thì các bạn sẽ thấy được kết quả trả về như này là đã chạy test thành công

```PHP
OK (2 tests, 4 assertions)

```

**assertEquals() và assertNull()**

`assertEquals()`  giúp chúng ta so sánh giá trị thực sau một chuối xử lý với giá trị mà chúng ta mong muốn.

`assertEmpty()`  giúp chúng ta kiểm tra xem giá trị mong muốn là null

```PHP
namespace Tests\Unit;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;
use App\User;

class UserTest extends TestCase
{
    /**
     * A basic unit test example.
     *
     * @return void
     */
    public function test_has_user()
    {
        $users = new User(['Lena', 'Misa', 'Leona']);
        $this->assertTrue($users->has('Lena'));
        $this->assertFalse($user->has('Minh Minh'));
    }
    
    public function test_equal()
    {
        $expected = "Hoang"; 
        $actual = "Min Hoang"; 
        
        $this->assertEquals( 
            $expected, 
            $actual, 
            "actual value is not equals to expected"
        ); 
    }
}

```

Và kết quả

```PHP
PHPUnit 8.2.5 by Sebastian Bergmann and contributors.

F                                                                   1 / 1 (100%)

Time: 64 ms, Memory: 10.00 MB

There was 1 failure:

1) UserTest::test_equal
actual value is not equals to expected
Failed asserting that two strings are equal.
--- Expected
+++ Actual
@@ @@
-'Hoang'
+'Min Hoang'

FAILURES!
Tests: 1, Assertions: 1, Failures: 1.

```

Các bạn thử thay đổi lại  `$expect = 'Hoang'`  là output nó lại ra đúng ngay

```PHP
OK (3 tests, 6 assertions)

```

**assertContains() , assertCount() và assertEmpty()**  Chúng ta sẽ cùng đi tìm hiểu với 3 hàm assertion làm việc với array nhé.

-   `assertContains()`: hàm này mục đích là giá trị mong đợi có tồn tại hay mảng được cung cấp có chưa giá trị mà chúng ta mong đợi hay không
-   `assertCount()`: hàm này mong đợi số lượng items trong một mảng kết quả trả về có match với số lượng mà chúng ta mong muốn hay không
-   `assertEmpty()`: hàm này mong đợi mảng kết quả trả về rỗng.

```PHP
namespace Tests\Unit;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;
use App\User;

class UserTest extends TestCase
{
    /**
     * A basic unit test example.
     *
     * @return void
     */
    
    public function test_contain_princess()
    {
         $princesses = ['Linda', 'Lisa', 'Celindar'];
         
         $this->assertCount(3, $princesses);
         $this->assertContains('Linda', $princesses);
         $this->assertEmpty($princesses);
    }
}

```

Bạn sẽ nhận được kết quả pass 2 test và fail 1 test cuối cùng do  `$princesses`  không trống.

# Làm thế nào để biết được viết unittest đạt yêu cầu ?

Chúng ta viết unittest đạt yêu cầu khi đủ ba yếu tố sau:

-   Arrange: thiết lập trạng thái giả lập, khởi tạo Object, giả lập Mock
-   Act: Chạy đúng method ta đang cần test
-   Assert: So sánh kết quả mong đợi với kế quả trả về.

# Kết luận

Qua một số chia sẻ trên của mình, chúng mình cũng đã tìm hiểu được những cái gì cơ bản nhất về UnitTest, phần tiếp theo mình sẽ viết bài về một ví dụ cụ thể viết UnitTest cho các hàm resource CRUD mà chúng ta hay thường viết trong controller nhé. Cảm ơn các bạn đã đọc bài viết chia sẻ của mình

# Tham khảo

[https://laravel.com/docs/5.8/testing](https://laravel.com/docs/5.8/testing)
