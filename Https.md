


> Written with [StackEdit](https://stackedit.io/).

## Mẹo hướng dẫn cấu hình SSL trên localhost cho XAMPP

Bài viết này sẽ hướng dẫn cách bạn tạo chứng chỉ SSL cục bộ được sử dụng trong XAMPP trên Windows.

Trong bài viết này sẽ sử dụng tên miền ảo làm ví dụ là site.test

### 1. Vào thư mục Apache trong XAMPP.

Mặc định  **XAMPP** đợc cài đặt trong thư mục **C:\xampp\apache**.

### 2. Tạo một thư mục mới.

Đây là nơi ta sẽ lưu trữ chứng chỉ SSL. Trong ví dụ này Ngôi Sao Số sẽ tạo thư mục  **crt** ,Vì vậy, đường dẫn sẽ có dạng **C:\xampp\apache\crt**

### 3. Tải về và thêm 2 tập tin này vào thư mục vừa tạo

Vào  [đường dẫn https://github.com/ngoisaoso/Enabling-SSL-with-XAMPP sau tải](https://github.com/ngoisaoso/Enabling-SSL-with-XAMPP)  2 tập tin  **cert.conf make-cert.bat** về. 2 tập tin này sẽ dùng để tạo chứng chỉ SSL cho tên miền tùy thích.

### 4. Sửa cert.conf và chạy make-cert.bat

Mở file cert.conf và thay đổi {{DOMAIN}} thành tên miền bạn muốn, trong trường hợp này **site.test**  và lưu lại.

Nhấp đúp chuột vào make-cert.bat và nhập tên miền  **site.test**  khi được nhắc. Và nhập trả lời cho các câu hỏi khác, thiết lập mặc định có sẵn trong cert.conf

![huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-1](https://wiki.ngoisaoso.vn/uploads/news/2020_04/huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-1.jpg)

### 5. Cài đặt chứng chỉ trong windows.

Sau đó, bạn sẽ thấy thư mục site.test được tạo. Trong thư mục đó ta sẽ có  **server.crt**  and  **server.key**. Đây là chứng chỉ SSL certificate.

Nhấp đúp chuột vào server.crt để cài đặt nó trên Windows để Windows chấp nhận chứng chỉ này.

![huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-2](https://wiki.ngoisaoso.vn/uploads/news/2020_04/huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-2.png)

Và chọn **Local Machine**  trong  **Store Location.**

![huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-3](https://wiki.ngoisaoso.vn/uploads/news/2020_04/huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-3.png)

Tiếp tục chọn “**Place all certificate in the following store**” và click **browse**  sau đó chọn  **Trusted Root Certification Authorities**.

![huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-4](https://wiki.ngoisaoso.vn/uploads/news/2020_04/huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-4.png)

Chọn **Next**  và **Finish**.

Và bây giờ chứng chỉ này đã được cài đặt là tin cậy (trusted) trong Windows. Tiếp theo là làm thế nào để sử dụng chứng chỉ này trong XAMPP.

### 6. Thêm trang web trong máy chủ Windows

1.  Mở notepad với quyền administrator.
2.  Sửa C:\Windows\System32\drivers\etc\hosts
3.  Thêm một dòng mới:

```
127.0.0.1 site.test
```

Điều này sẽ giúp XAMPP khi truy cập http://site.test sẽ trỏ tên miền này về IP localhost

### 7. Thêm tên miền này vào file conf của XAMPP.

Giờ bạn cần kích hoạt SSL cho tên miền này và cho XAMPP biết nơi lưu trữ Chứng chỉ SSL. Vì vậy, bạn cần chỉnh sửa  **C:\xampp\apache\conf\extra\httpd-xampp.conf**

Và thêm dòng mới này dưới cùng:

### site.test
 <VirtualHost *:80>
     DocumentRoot "C:/xampp/htdocs"
     ServerName site.test
     ServerAlias *.site.test
 </VirtualHost>
 <VirtualHost *:443>
     DocumentRoot "C:/xampp/htdocs"
     ServerName site.test
     ServerAlias *.site.test
     SSLEngine on
     SSLCertificateFile "crt/site.test/server.crt"
     SSLCertificateKeyFile "crt/site.test/server.key"
 </VirtualHost>

Sau đó, bạn sẽ cần khởi động lại Apache trong XAMPP. Đơn giản, chỉ cần mở Bảng điều khiển XAMPP và bấm stop và start tại mục Apache.  
  
**Mẹo**: Trong file conf XAMPP, bạn có thể thay đổi thư mục gốc cho từng tên miền nếu cần.

### 8. Khởi động lại trình duyệt và thử lại!

Cần khởi động lại trình duyệt để hệ thống tải chứng chỉ. Và truy cập tên miền trên trình duyệt của bạn, và bạn sẽ thấy khóa màu xanh lá như hình bên dưới!

![huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-5](https://wiki.ngoisaoso.vn/uploads/news/2020_04/huong-dan-cau-hinh-ssl-tren-localhost-cho-xampp-5.png)

Ngôi Sao Số hy vọng hướng dẫn này hữu ích!
