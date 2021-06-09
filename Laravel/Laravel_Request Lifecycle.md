


> Written with [StackEdit](https://stackedit.io/).
# Tổng Quan Về Laravel Một Framework Khá Mạnh Mẽ

written by  [Trungquandev](https://trungquandev.com/author/trungquandev/)  July 22, 2016

[![Tổng Quan Về Laravel Một Framework Khá Mạnh Mẽ](https://trungquandev.com/wp-content/uploads/2016/07/mo-hinh-hoat-dong.jpg "mo-hinh-hoat-dong")](https://trungquandev.com/wp-content/uploads/2016/07/mo-hinh-hoat-dong.jpg)

#### **Xin chào các bạn, mình là Quân, hôm nay mình sẽ làm một bài tổng quan chi tiết về FrameWork Laravel, một FW khá mạnh mẽ hiện nay.**

**Những nội dung có trong bài này:**

-   _Luồng hoạt động của Laravel_
-   _Middleware_
-   _Request và Response_
-   _View: Blade Template và Elixir_
-   _Database: Relationship, Schema, Eloquent ORM, Validation, Collections, Mirgate, Seed, Query Builder…_
-   _Authenticate_
-   _Session_
-   _Cache_
-   _Và các phần hỗ trợ trong Laravel: Helper, Hashing, Events, Errors & Logging, ErrorDetail, Handling Errors, Pagination(phân trang)._

**Đầu tiên về luồng hoạt động của Laravel thì Laravel hoạt động theo mô hình MVC như thế này:**  
![mo-hinh-hoat-dong](http://trungquandev.com/wp-content/uploads/2016/07/mo-hinh-hoat-dong.jpg)

Tóm tắt lại sơ đồ trên là thế này:  Khi người dùng gửi một yêu cầu lên hệ thống, hệ thống sẽ gửi về cho Controller xử lý các yêu cầu của người dùng. Trong quá trình làm việc đó, Controller sẽ phải thông qua lớp Model nếu muốn làm việc với Cơ sở dữ liệu (DataBase). Sau khi xử lý xong, Model sẽ đưa dữ liệu về cho Controller, Controller tiếp tục đưa sang View và View hiển thị lại cho người dùng kết quả cuối cùng.

**Cái quan trọng thứ 2 mình muốn nói đến là MiddleWare trong laravel.**

Sơ đồ ở trên sở dĩ mình không đưa  **MiddleWare**  vào vì mình muốn nhấn mạnh mô hình  **MVC**, và tách riêng  **MiddleWare**  để giải thích cho các bạn dễ hiểu:

Các bạn xem hình sau:

![middleware](http://trungquandev.com/wp-content/uploads/2016/07/middleware.jpg)

Giả sử ta có một vị khách tên là  **Route**  đến và muốn đi vào  **Controller**, thì vị khách  **Route**  này sẽ phải  **checkin**  ở lễ tân  **MiddleWare**. Nếu hợp lệ thì cho  **Route**  đi tiếp, còn không thì không cho phép.

Vậy có thể hiểu  **MiddleWare**  nó sẽ bảo vệ các  **Controller**  của chúng ta.

**Tiếp theo là Service Providers:**

**Service Providers**  nằm trong config/app.php, khi bạn vào app.php này, bạn kéo xuống sẽ thấy Providers là một mảng.

Nó là nơi để bạn đăng ký tất cả mọi thứ, sau đó  **Service**  **Providers**  sẽ cho  **Laravel**  biết tất cả về package của bạn, và  **Laravel**  sẽ biết các  **dependencies**  (phụ thuộc, tin tưởng) nào cần bind và cần giải quyết (resolve)

Nói chung  **Service Providers** nghĩa là đăng ký tất cả mọi thứ như nghe sự kiện, trung gian và các tuyến đường….nó là trung tâm để bạn cấu hình khi mà tạo một ứng dụng, một package riêng cho mình.

**Request và Response**  trong  **Laravel**

**Request và Response**  là gì, mình sẽ giải thích đơn giản như này, các bạn hãy nhìn vào hình sau:

![request-response](http://trungquandev.com/wp-content/uploads/2016/07/request-response.jpg)

Khi mà các bạn gửi dữ liệu thông qua các đường dẫn trên route, thì cách đó chỉ có tác dụng với phương thức  **GET**, còn nếu khi cần sử dụng  **POST**, các bạn sẽ không đưa được dữ liệu lên đường dẫn đó được. Chính vì vậy mà Laravel tạo ra một cái gọi là Request, Requet này sẽ quản lý các dữ liệu được gửi lên Route và truyền dữ liệu đó sang bên Controller. Dữ liệu Request có thể là một mảng, một đối tượng hoặc là một JSON

Sau đó Controller sẽ xử lý Request và tạo ra một lớp Response rồi trả Response đó về máy tính cho chúng ta. Response đảm nhiệm việc chuyển đổi giữa các kiểu dữ liệu Request và gửi về cho người dùng.  
View trong Laravel (2 khái niệm Blade Template và Elixir)  
**Thì Blade Template là gì?**  Thế này, Blade Template là một Template Engine có tác dụng làm sạch đi các đoạn code PHP nằm trong View để tách biệt Hoàn Toàn giữ người làm Front-End (cắt CSS) và người code PHP. Blade Template là một engine khá mạnh mẽ nên được Laravel chọn và tích hợp vào sẵn. Nên tất cả các file trong View đều sẽ có phần mở rộng là  **.blade.php** (Chỉ đơn giản vậy thôi, còn chi tiết mình sẽ làm riêng về nó ở một bài khác, vì bài này chỉ là tổng quan ^^, bạn nào chịu khó tìm tòi thì cứ search google, không thiếu đâu :v –_Trung Quân_)

**Elixir là gì?** Thì trong laravel 5 có tích hợp GULP( tự động hóa trong js) và Core của mình khiến cho việc thao tác với js, css dễ dàng hơn nhiều.

Elixir xuất hiện là để cung cấp một cách làm mới sáng sủa hơn cùng với  **fluent API**(là một cách để cấu hình các lớp domain) Nhằm thực hiện các công việc cơ bản của GULP một cách đơn giản cho ứng dụng laravel

Có thể nói Elixir là một khu vực hỗ trợ rất nhiều các trình dịch css, js và các công cụ test thông dụng. Thay vì phải xây dựng css và js của riêng bạn để cho hệ thống, thì bạn có thể sử dụng Elixir và chạy một cách dễ dàng.

**Database trong laravel: Database-Relationship-Eloquent ORM-Validation-Collections**

**Schema**: lớp này cung cấp cho chúng ta các hàm để Tạo bảng, chỉnh sửa bảng, thêm bảng, xóa bảng…. trong laravel

**Migrate**  và  **Seed**: quản lý cơ sở dữ liệu trong laravel, backup hay reset lại cơ sở dữ liệu hay là tạo một bộ cơ sở dữ liệu mẫu để chúng ta làm việc với database một cách dễ dàng hơn

**Query**  **Builder**: là các câu lệnh truy vấn đến cơ sở dữ liệu trong laravel

**Eloquent**  –**Model**: là phần Model trong mô hình MVC ở trên đầu

**Relationship**: liên kết dữ liệu giữa các bảng trong laravel (một nhiều) (nhiều nhiều)

_Cụ thể hơn:_

Về  **Migrate**  mình có một ví dụ dễ hiểu sau:

![migrations](http://trungquandev.com/wp-content/uploads/2016/07/migrations.jpg)

Giả sử bạn là người lập trình viên trong hình trên, ngày thứ 2 trong tuần bạn tạo ra một bảng A, thứ 3 tạo bảng B, thứ 4 Sửa bảng A và thứ 5 xóa Bảng B.

Đến thứ 5 sau khi xóa bảng B thì hệ thông của bạn báo lỗi, bạn sẽ làm thế nào, khi này, Laravel sẽ cung cấp cho bạn Migration, lớp Migration này sẽ lưu lại các công việc mà bạn đã làm, từ đây, bạn sẽ có thể quay lại ngày thứ 3 để tiếp tục làm việc với bảng B và tìm hiểu tại sao khi xóa lại bị lỗi.

**Migrate**  nó sẽ  **lưu lại thứ tự công việc**  mà bạn làm ở trong bảng Migrations trong database, rất thuận lợi cho chúng ta làm việc nhóm khi mà hôm nay người này tạo bảng, ngày mai người khác tạo bảng khác hay là sửa xóa bảng của bạn.

Các file Migrate sẽ được lưu ở Database>Migrate.

**Seed là gì?**  Nó là bộ dữ liệu mẫu, giúp chúng ta insert dữ liệu mẫu vào bảng đó.

Seed được lưu ở Database>Seeds

Dữ liệu mẫu mà ta thêm vào có thể thêm được nhiều bằng cách truyền vào với dạng mảng như sau:

![Seeder](http://trungquandev.com/wp-content/uploads/2016/07/Seeder.jpg)

**Query**  **Builder:** là các lệnh truy vấn trong laravel, nó có tác dụng thay thế cho các câu lệnh truy vấn thông thường bằng các phương thức trong lớp DB.

Mình ví dụ: Nếu bạn muốn lấy toàn bộ dữ liệu trong một bảng có tên là Products, thì bình thường dùng PHP thuần bạn sẽ làm như sau:

**_Select * from products_**

Trong laravel thì khác, cũng có yêu cầu như thế, nhưng câu lệnh sẽ là:

**_DB::table(‘products’)->get();_**

**Eloquent ORM** –**Model :** một phần mà mình thấy rất là hay

Model trong laravel có thể lưu ở bất kỳ đâu để nạp tự động tùy theo file composer.js nhưng thông thường mặc định tất cả model sẽ được lưu trong thư mục  **app/**

**Thì Eloquent ORM** nó cung cấp cho chúng ta cách thức chuyển đổi cơ sở dữ liệu quan hệ sang mô hình hướng đối tượng.

**Eloquent ORM** làm việc trên cơ sở Active Record. Mỗi Bảng cơ sở dữ liệu của chúng ta sẽ được “ánh xạ” thành một file Model tương ứng, và chính Model này chúng ta sẽ sử dụng nó để tương tác với bảng.

Ví dụ ta có một bảng Product trong database, ta tạo model bằng câu lệnh:

**_php artisan make:model Product_**

Ta sẽ có một file Product.php trong thư mục app, bên trong file Product.php này ta có thể viết các câu lệnh truy vấn, các hàm hoặc các ràng buộc  **_relationship_**  cho bảng Product trong database của bạn

**Relationship:** là các kiểu quan hệ ràng buộc trong laravel: cụ thể mình có một danh sách như sau: nhìn qua là các bạn hiểu ngay thôi:

**-Một-Một**: liên kết từ bảng cha tới bảng con: câu lệnh:  **_->hasOne();_**

**-Một-Một**: liên kết từ bảng con tới bảng cha: câu lệnh:  **_->belongsTo();_**

**-Một-Nhiều**: câu lệnh:  **_->hasMany();_**

**-Nhiều-Nhiều**: câu lệnh:  **_->belongsToMany();_**

-Liên kết qua  **trung gian**:  **_->hasManyThrough();_**

**Validation trong laravel**

Chắc các bạn đã nghe qua validation form trong lập trình để đảm bảo người dùng có thể nhập thông tin chính xác rồi, giống như form đăng ký chẳng hạn, email phải có đuôi @abc.com gì gì đó.

Thì trong Laravel có cung cấp sẵn cho chúng ta một hệ thống Validation sẵn, bạn có thể dử dụng một cách dễ dàng mà không cần phải code dài dòng nhiều

**Collections trong laravel**

Collections trong laravel cung cấp cho chúng ta rất nhiều phương thức hay, ngắn gọn giúp tiết kiệm thời gian làm việc, đặc biệt là làm API kết nối đến database vì dữ liệu trả về từ database có sẵn kiểu là Collection. Một vài phương thức đơn giản như là:

List: trả về một mảng có cặp key và value

**_$collection->lists(‘email’, ’id’);// xuất ra một mảng có key là id và value là email_**

Còn nhiều nữa như là Filter, ToJson, ToArray, Count, Take, Sum, SortBy, SortByDesc……..mình sẽ nói rõ hơn ở bài khác, nhưng mình khuyên các bạn nên chủ động google, nhiều lắm =))

**Authenticate trong Laravel**

**Authenticate** viết tắt là Auth, là một lớp hỗ trợ cho chúng ta trong việc quản lý việc đăng nhập, đăng xuất, kiểm tra xem người dùng đã đăng nhập hay chưa, kiểm tra thông tin đăng nhập của người dùng.

![authenticate](http://trungquandev.com/wp-content/uploads/2016/07/authenticate.jpg)

Ví dụ như hình trên, bạn cần kiểm tra login của một tài khoản, thì cột bên trái sử dụng php thuần, bạn sẽ phải khởi tạo biến, sau đó viết câu lệnh truy vấn với điều kiện, rồi thực thi nó, rồi lại kiểm tra nó…

Cột bên phải là sử dụng Auth trong laravel, các bạn chỉ việc đưa thông tin đăng nhập của người dùng vào và Auth này sẽ trả lại kết quả cho chúng ta.

**Session trong Laravel**

Session trong laravel thì định nghĩa cũng giống như Session trong PHP thuần, nó là một phiên làm việc giúp chúng ta lưu trữ các thông tin cần thiết như là Login hay là giỏ hàng chẳng hạn

Cấu hình session trong laravel sẽ ở Config/session.php

**_‘lifetime’_:**  thời gian tồn tại của session, tính theo phút

**_‘expire_on_close’_**: true là mất khi đóng trình duyệt, false thì ngược lại không mất

**_‘driver’=>env(‘SESSION_DRIVER’, ‘file’)_  :**  chọn nơi sẽ lưu trữ session

-Cách sử dụng Session:  _Session::put(‘key’, ‘value’);_

-Đặt một giá trị vào trong một giá trị session của mảng:  **_Session::push(‘user.teams’, ‘developers’);_**

-Truy vấn tới Session được lưu trữ: $value =  _Session::get(‘key’);_

_-Còn rất nhiều,c ác bạn có thể tham khảo thêm tại đây:  [https://laravel.com/docs/5.2/session](https://laravel.com/docs/5.2/session)_

**Cơ chế Cache trong Laravel**

Trước tiên mình sẽ nói qua về Cache cho bạn nào chưa hiểu: Cache là một bộ nhớ đệm, được hiểu là một tầng ở giữa cơ sở dữ liệu và website trong ứng dụng mà bạn xây dựng.

Tất cả dữ liệu trong cache đó là kết quả của những tiến trình mà bạn xử lý trước đó hoặc bản copy dữ liệu đã được lưu trữ ở nơi khác.

Cache được sử dụng để giải quyết việc các truy vấn tới cơ sở dữ liệu bị chậm khi mà có nhiều người truy cập vào trang web cùng lúc

Cache hoạt động như là một tầng trung gian để lưu trữ những dữ liệu không thay đổi giữa các request và việc lấy các thông tin từ bộ nhớ cache sẽ nhanh hơn là bạn phải truy vấn lại đến cơ sở dữ liệu một lần nữa.

Vậy  **Cache trong laravel**  cũng như vậy thôi, Laravel cung cấp các API thống nhất cho những hệ thống bộ nhớ cache khác nhau, các câu hình cache đặt ở file  **config/cache.php**

Hiện nay Laravel hỗ trợ nhiều loại bộ nhớ đệm phổ biến, và thông dụng là 2 loại  **Memcached**  và  **Redis**

**Phương thức GET** trong  **facache cache** được sử dụng để lấy dữ liệu từ bộ nhớ Cache. Nếu dữ liệu ta yêu cầu không có trong bộ nhớ cache thì phương thức GET này sẽ trả về giá trị null. Bạn cũng có thể đưa vào một tham số thứ hai tới phương thức get để xác địch giá trị mặc định trả về trong trường hợp cache không có dữ liệu và bạn không muốn nó trả về null

(**Facache cache**: là cách để laravel sử dụng các chức năng được cung cấp từ các class được sử dụng thông qua các Service Provider)

**_Các phần hỗ trợ trong Laravel_**

**Helper**: là những hàm đã được xây dựng sẵn để hỗ trợ chúng ta. Nói đến đây chắc các bạn tự hỏi Helper khác Library như thế nào? Thì Library là viết dựa trên những class dùng để định nghĩa các phương thức và thực thi các hành động bên trong lớp đó.

Helper có thể gọi ở bất cứ đâu và tùy ý sử dụng.

Các hàm Helper mà Laravel đã định nghĩa sẵn cho chúng ta đều có ở đây, các bạn có thể vào và tìm hiểu thêm về nó, có rất nhiều hàm.

[https://laravel.com/docs/5.0/helpers](https://laravel.com/docs/5.0/helpers)

**Hashing**: là hàm mã hóa dữ liệu, sử dụng thuật toán Blowfish. Bcrypt  [https://en.wikipedia.org/wiki/Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)

**Events:** dịch đúng nghĩa là “sự kiện”, chắc chắn bạn đã từng nghe qua các sự kiện click, hover… khi làm việc với database rồi. Ở đây ta làm việc với PHP, Event trong laravel cũng tương tự theo kiểu ta có một sự kiện và trong sự kiện đó có nhiều hành động.

Ví dụ dễ hiểu là như sự kiện  _văn nghệ_ thì ta có các hành động như luyện tập, diễn văn nghệ, mời khán giả, quảng cáo…..v.v.

**Errors & Logging:**  Các hàm xử lý lỗi và Log

**Các Logging**  trong laravel cung cấp một lớp đơn giản trên đầu trang của thư viện Monolog. Mặc định các tập tin hàng ngày được tạo ra và lưu lại trong  _storage/logs_

Bạn cũng có thể tự đăng ký một sự kiện để bắt các message được thông qua để đăng nhập

**Error Detail:**  Các lỗi ứng dụng của bạn sẽ được hiển thị thông qua các trình duyệt, và được điều khiển bởi  _app.debug_  trong file  _config/app.php_

**Handling Errors:** Tất cả các trường hợp ngoại lệ được xử lý trong lớp  _App\Exceptions\Handers._ Lớp này có 2 phương thức là  _Report_  và  _Render_

_Report: (báo cáo):_ được sử dụng để đăng nhập các ngooaij lệ hoặc gửi chúng đến một dịch vụ bên ngoài

_Render:(trả lại):_được sử dụng để chuyển đổi các ngoại lệ vào phản hồi HTTP mà phải được gửi lại trình duyệt.

**Pagination: Phân trang trong Laravel**

-Phân trang trong trang web thì hầu hết tất cả các trang web đều sẽ có phần này khi mà trang của bạn có nhiều dữ liệu cần hiển thị, bạn không thể hiển thị hết chúng cùng một lúc được mà phải chia ra các trang…

-Không khó khăn như ở các framework khác, ở Laravel, Việc phân trang rất đơn giản. Mặc định Laravel có 2 kiểu view phân trang.

-Pagination::slider sẽ hiện đầy đủ dãy đường link phân trang trên trang hiện tại.

-Pagination::simple sẽ hiển thị 2 nút Previous và nút Next.

-Cả 2 loại View trên đều tương thích với Bootstrap

-Có nhiều cách phân trang:

+Theo Query Builer:

_$users = DB::table(‘users’)->paginate(15); //hiển thị 15 user trên một trang_

+Theo Eloquent Model ORM:

_**$allUsers = User::paginate(15);**_

**_$someUsers = User::where(‘votes’, ‘>’, 100)->paginate(15);_**

+Theo Simple Pagination: chỉ có nút Next và Previous:

**_$users = User::where(‘id’, ‘>’, 20)->paginate(5);_**

+Phân trang thủ công: thông qua phương thức Paginator::make

_**$paginator = Paginator::make(‘$user’, $totlalUser, $perPage);**_

-Ngoài ra bạn cũng có thể thay đổi đường dẫn của link phân trang như sau:

_**$users = User::paginate();**_

_**$user->setBaseUrl(‘trungquan/url’);**_

_//Kết quả link sẽ có dạng:_ **http://trungquandev.com/trungquan/url?page=7**

-Thêm dữ liệu vào link phân trang bằng phương thức  _appends_ nếu bạn muốn thêm một vài tham số vào link phân trang

-Cuối cùng là hiển thị dữ liệu phân trang lên view, thì khi mà lấy dữ liệu phân trang trong Cơ Sở dữ liệu xong Laravel sẽ trả về cho chúng ta một mảng các đối tượng, Lúc này chúng ta chỉ cần gửi dữ liệu đó qua View và hiển thị thôi.

**_$users->links();_**

Done.

Nếu có gì thắc mắc hoặc cần hỏi, bạn có thể comment dưới  [Bài Viết](http://trungquandev.com/tong-quan-ve-laravel-mot-framework-kha-manh/)  này hoặc  [Liên Hệ](http://trungquandev.com/contact/)  với mình, mình sẽ trả lời bạn sớm nhất. Cảm ơn các bạn và hẹn gặp lại các bạn ở những bài hướng dẫn tiếp theo.

[_**Best Regards – Trung Quân**_](http://trungquandev.com/)

#### Khóa học lập trình làm việc thực tế:

#### **Nếu các bạn thấy bài viết của mình có ích thì hãy ủng hộ mình bằng cách tham khảo bài viết giới thiệu khóa học cực kỳ chất lượng và chính chủ dưới đây của mình nhé, cảm ơn các bạn ^^**

Nguồn https://trungquandev.com/
