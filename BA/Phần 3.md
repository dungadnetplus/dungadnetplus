


> Written with [StackEdit](https://stackedit.io/).

# Business Analysis: Planning and Ensuring Success (Phần 3)

[Happy New Year](https://viblo.asia/tags/happy-new-year)

**Plan Business Analysis Governance.**

> ... define how decisions are made about requirements and designs...

Bài trước mình đã giới thiệu đến các bạn sự can thiệp hay các cách thức làm việc với  `Stakeholder`  rồi, trong bài viết ngày hôm nay mình sẽ giới thiệu đến các bạn phần kiến thức làm cách nào mà chúng ta lên plan cho việc  `Plan Business Analysis Governance`. Mục tiêu của việc này là để định nghĩa làm cách nào để đưa ra những quyết định về requirement và design, nó bao gồm review, change control, approval và prioritization. (viết tiếng anh nhé, các bạn tự ngẫm và hiểu nhé, chứ dịch ra tiếng việt hơi củ chuối  ![😃](https://twemoji.maxcdn.com/2/72x72/1f603.png)))

![](https://images.viblo.asia/6bedcfe0-81d2-4eaa-b49a-8935c22aa2e9.png)

# Introduction

Người BA đảm bảo quá trình quản lý (governance progress) phải được thực hiện và làm rõ bất cứ những điều gì  **ambiguity**  . Một  `governance process`  nhận định được những quyết định của người lập lên kế hoạch (decision makers), những thông tin được yêu cầu cho những quyết định được đưa ra. Một  `governance process`  miêu tả các cách approve và ưu tiên những quyết định nào đã được đưa ra cho requirement và design.

Người BA cần nhận định những điều sau:

-   Cách tiếp cận và ưu tiên công việc business analysis
-   Quy trình cho việc đề nghị sự thay đổi để phân tích nghiệp vụ thông là gì ?
-   AI sẽ là người có thẩm quyền và trách nhiệm để đề nghị những thay đổi và ai nên tham gia vào bàn bạc những thay đổi đó.
-   Ai sẽ là người có khả năng cho việc phân tích những change request
-   Ai sẽ là người authorize để chấp thuận những thay đổi
-   Những cách tài liệu hóa và truyền đạt là những thay đổi trên

# Elements

## Decision Making

![](https://images.viblo.asia/18f5d8be-d062-42a6-9dad-4c403200108b.png)Những quyết định được đưa ra thông qua sáng kiến. Một stakeholder sẽ đảm nhiệm một vài role trong quá trình đưa ra ý kiến như là

-   Tham gia trong quá trình bàn bạc đưa ra quyết định
-   SME (subject matter expert)
-   reviewer of information
-   approver of decision

Quá trình đưa ra quyết định được thực hiện khi team có nhiều ý kiến trái chiều và cần lựa chọn những ý kiến tối ưu nhất, cho nên mới cần những key stakeholder để có thể chốt lại

## Change Control Process

![](https://images.viblo.asia/68535eba-fa97-4e72-b833-22615997f72c.png)  Khi những người BA phát triển  `a change control process`, thì họ cần phải làm những điều sau:

-   Quyết định quá trình cho request thay đổi : xác định những requirement và design quá trình kiểm soát change và xác định liệu chúng áp dụng cho tất cả các thay đổi hoặc chỉ những thay đổi về size, cost, hay mức độ effort.
-   Quyết định những thành phần của change request : xác định thông tin đã được bao gồm trong đề nghị để có thể support đưa ra quyết định và thực hiện nếu nó được chấp thuận.
-   Những thành phần có thể cân nhắc trong change request: cost, time estimate, benefits, risks, priority.
-   Quyết định những cách thay đổi sẽ được ưu tiên
-   Xác định những cách thay đổi sẽ được document lại
-   Xác định những cách thay đổi sẽ được truyền đạt
-   Xác định ai sẽ thực hiện phân tích tác động
-   Xác định ai sẽ authorize những thay đổi

## Plan Prioritization Approach

Chúng ta phải lên kế hoạch cho cái gì ưu tiên làm trước để có được kết quả tốt nhất, ví dụ như timeline, expected value, resource,.. Khi lên kế hoạch cho quá trình thực hiện ưu tiên những cái gì làm trước, người BA cần xác định những thứ sau![](https://images.viblo.asia/ce6fe223-663f-4902-9e1a-a328f507e2b5.png)

## Plan for Approvals

Khi những đã được chấp thuận bởi tất cả stakeholder rồi thì nội dung và bài thuyết trình của requirement và design cần sự chính xác, đày đủ và chứa đầy đủ chi tiết để cho phép quá trình được tiếp tục đưa ra.

Vấn đề về thời gian và tần suất phê duyệt là phải phụ thuộc vào quy mô và sự phức tạp của thay đổi, được liên kết những rủi ro của việc từ chối hoặc chậm chễ trong việc chấp thuận.

Người BA phải xác định những kiểu requirement và design để được approve, thời gian cho sự chấp thuận, quá trình để để follow và nhận được sự chấp thuận, ai sẽ là chấp thuận cho requirement và design.

Khi việc lên kế hoạch đánh giá cao quá trình chấp thuận, người BA cân nhắc về văn hóa tổ chức và kiểu thông tin đang được chấp thuận. Ví dụ, những hệ thống mới hoặc những quy trình cho những doanh nghiệp lớn như financial, pharmaceutical hoặc healthcare thường được yêu cầu review thường xuyên, nghiêm ngặt và phê duyệt một cách chi tiết.

Việc lên kế hoạch cho sự chập thuận cũng bao gồm schedule của những sự kiện - những sự chấp thuận sẽ xuất hiện và làm cách nào chúng ta có thể tracking được chúng.![](https://images.viblo.asia/dcfcb5c2-9ebb-4918-aee9-948118ddb705.png)

# Techniques

Người BA có thể sử dụng một số những kỹ thuật dưới đây để có thể áp dụng  ![](https://images.viblo.asia/04b1a9a0-aebe-4c2b-a919-a9261be989a3.png)  ![](https://images.viblo.asia/014b3c50-1d7a-4745-82e1-1fa5d49c4e03.png)

# Stakeholders

![](https://images.viblo.asia/4f9ad84e-5eee-458e-a514-2b092ae4329f.png)

# In clusion

Vậy qua đó mình đã giới thiệu cho bạn phần kiến thức  `Plan Business Analusis Governance`. Mong rằng nó đem đến chút ít ỏi nào hiểu biết về chương này. Cảm ơn các bạn đã đọc bài viết của mình.

[sharpBusinessanalyst](https://viblo.asia/tags/sharpbusinessanalyst)
