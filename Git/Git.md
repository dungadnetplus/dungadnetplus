


> Written with [StackEdit](https://stackedit.io/).
# Chiến thuật sử dụng Git trong teamwork hiệu quả (Kì đầu)

[Happy New Year](https://viblo.asia/tags/happy-new-year)

Tí là một "coder bờ rào" đang được thầu nguyên một dự án về phần mềm quản lí nhân viên tại công ty ABC. Với tốc độ code 500 line/hours, giải lao thì commit code lên master, cuối ngày  **`git push`**  một cái rồi tắt máy lội suối băng đèo về nhà.

Ngờ đâu leader của Tí muốn đẩy nhanh tiến độ nên đã tuyển thêm 2 bạn thực tập vào support. Ngờ đâu, mọi chuyện bắt đầu phức tạp hơn. Code trong team bị chồng chéo và xung đột liên tục, branch master bỗng phân nhánh như màng nhện. Tốc độ code giảm xuống, còn số lần search Google thì tăng lên. Cuộc đời Tí lâm vào bế tắc.

![](https://images.viblo.asia/0081eea3-cc17-4396-a3fb-c5889a089359.png)

Đang trong lúc tuyệt vọng thì Tí tìm được những "bí kíp" giải quyết vấn đề. Hãy cùng xem nó là gì nhé!

## 1. Quy ước phân nhánh

Trong Git thì khả năng phân nhánh (branch) khá đơn giản và nhanh chóng. Vấn đề là cần phải xây dựng một quy ước phân nhánh hợp lí để đồng đội có thể hợp phối hợp nhịp nhàng mà thôi. Bạn có thể chia dự án thành 2 branch chính:

-   master
-   dev

Nhánh  **master**  sẽ là nơi chứa phần code  **ổn định nhất**, sẵn sàng để triển khai bất cứ lúc nào. Trong khi đó, nhánh  **dev**  ban đầu được tách ra từ master, và sẽ chứa phần  **code mới nhất**  được phát triển.

Nếu bạn nào không nhớ thì để tạo nhánh mới trong Git, bạn dùng lệnh:

```none
git checkout -b <tên nhánh mới> [nhánh gốc]

```

Chẳng hạn, để tạo nhánh  `dev`  từ`master`, bạn gõ

```none
git checkout -b dev master.

```

Nếu không cung cấp tham số`[nhánh gốc]`, nhánh mới tạo sẽ dựa trên nhánh hiện tại bạn đang ở. Để xem nhánh hiện tại là nhánh nào, bạn có thể dùng lệnh

```none
$ git branch

```

Ví dụ kết quả trả về là:

```none
  auth
* auth-session
  dev
  graphql
  master

```

thì nhánh hiện tại của bạn chính là  `auth-session.`

## 2. Phân chia công việc

Khi đã có 2 nhánh như ở trên rồi thì mỗi khi phát triển tính năng mới, bạn  **truy cập vào dev và tạo thêm các nhánh nhỏ trong đó.**

```none
git checkout -b login dev

```

Nhánh này dưới quyền cai quản của bạn, nên hãy làm những gì bạn muốn nhé. Cứ commit thường xuyên, dù chỉ là những thay đổi nhỏ nhất. Cũng đừng ngần ngại rằng commit nhỏ sẽ khiến git log khó theo dõi. Vì mọi thứ đều có cách giải quyết.

Đến đây, Tí chợt nhận ra một điều rằng: Lỡ như những người đồng đội của Tí đều cùng làm chung một tính năng thì sao?

Nếu gặp phải trường hợp ấy, bạn có thể tiếp tục chia nhỏ hơn nữa, để đảm bảo mỗi người làm việc trên một nhánh độc lập.

> Một tính năng to sẽ có 2-3 người phát triển. Nếu số lượng người tham gia vượt quá con số này, thì bạn hãy xem xét phân chia các tính năng đó nhỏ hơn.

## 3. Chuẩn bị merge vào dev

Sau khi code hoàn tất và tất cả unit tests đã chạy thành công, giờ là lúc bạn merge/gửi code để review tính năng mới vào dev. Thông thường, sẽ có 2 trường hợp xảy ra:

### Trường hợp 1: Không có gì mới trong dev

Giả sử lúc đó Git history của dự án giống như thế này:![](https://images.viblo.asia/c1cbf728-4b7d-47c7-8dc6-534d70ad5226.png)

Như bạn thấy, nhánh  `login`  màu vàng được rẽ ra từ nhánh  `dev`  màu xanh, và trong nhánh  `dev`  không có code gì mới. Đây là trường hợp lý tưởng, đảm bảo khi  _merge vào dev_  chúng ta sẽ không bị xung đột code.

### Trường hợp 2: Có commits mới trong nhánh dev

![](https://images.viblo.asia/26f6a247-b7fd-4233-b6cd-a41914bb555f.png)

Trong trường hợp này,  `branch dev`  (màu xanh) đang có 2 commits phía trước  `branch login`  (màu vàng). Nếu trong 2 commits đó có chứa thay đổi liên quan đến dev, chẳng hạn như package.json, thì khả năng cao là sẽ xảy ra xung đột khi merge trực tiếp login vào. Mà dù có may mắn không xảy ra xung đột code, thì merge vào cũng sẽ làm history xấu đi.

![](https://images.viblo.asia/83743bec-6537-48b0-b28d-bb536ce13bb8.png)

Do đó, chúng ta sẽ cần sửa lại history của nhánh login bằng cách dùng  `git rebase.`

### git rebase là gì?

![](https://images.viblo.asia/f23d3974-f5cb-4ea1-a615-658c04ba55d1.png)

**git rebase**  sẽ đem những commits bên trong nhánh login và áp dụng lại vào sau commit mới nhất trong nhánh dev.

Cú pháp của lệnh này là:

```none
git rebase <tên nhánh muốn áp dụng lại>

```

Quay trở lại ví dụ trên, bạn sẽ cần chạy những lệnh sau:

```none
# Cập nhật repo hiện tại, đồng thời lấy về commits mới nhất của `dev`
git pull

# Chuyển qua nhánh `login` (bỏ qua bước này nếu bạn chắc chắn mình đang ở `login`)
git checkout login

# Tiến hành rebase
git rebase dev

```

Nếu xảy ra xung đột code, bạn có thể phát hiện và giải quyết chúng sớm. Nguyên tắc chung là không sửa code của người khác, và chỉ kết hợp thêm những gì bạn làm. Việc thực hiện rebase tại nhánh chức năng do bạn phụ trách giúp giảm thiểu khả năng mất code, vì bạn là người hiểu rõ nhất phần code bạn viết.

Sau khi giải quyết hết các xung đột trong code, bạn chạy  `git rebase --continue`  để tiếp tục tiến trình rebase. Bạn cũng có thể chạy  `git rebase --abort`  để hủy bỏ rebase và đưa nhánh login về lại trạng thái ban đầu.

Nếu chưa quen  **rebase**, bạn có thể tạo một branch mới từ  `login`, ví dụ:  `git checkout -b test login`, và tiến hành rebase trên branch này. Sau khi chắc chắn là mọi thứ ổn thỏa, bạn có thể quay lại và tiến hành rebase cho login.

Khi rebase xong, mong là history của bạn trông sẽ giống như thế này:![](https://images.viblo.asia/118ccb31-4dca-42ee-8ed6-b88f64b32d6b.png)

## Tạm kết

Vậy là trong bài viết này chúng ta đã nắm được:

-   Quy ước phân nhánh
-   Chiến thuật phân chia công việc
-   Và phần chuẩn bị để merge vào branch  `dev`

Đó đều là những bí kíp cơ bản đầu tiên giúp bạn làm việc nhóm hiệu quả hơn với Git. Trong phần tiếp theo, mình sẽ đi sâu hơn về các "chiêu thức merge" khác. Quá trình merge từ branch  `dev`  vào branch  `master`  cùng những bonus thú vị khác nhé!

***Update:**  Kì cuối đã có  [tại đây!](https://viblo.asia/p/chien-thuat-su-dung-git-trong-teamwork-hieu-qua-ki-cuoi-gDVK2ByrKLj)

# Chiến thuật sử dụng Git trong teamwork hiệu quả (Kì cuối)

[Happy New Year](https://viblo.asia/tags/happy-new-year)

Trong  [phần trước](https://viblo.asia/p/chien-thuat-su-dung-git-trong-teamwork-hieu-qua-ki-dau-L4x5x6n1ZBM), mình đã giới thiệu về "3 bí kíp" khi thao tác Git trong teamwork rồi. Nếu đã theo dõi (Kì đầu) thì giờ bạn có thể tiếp tục xem Tí đã làm gì tiếp theo để thoát khỏi bế tắc cuộc đời nhé.

## 3. Chuẩn bị merge vào dev (tiếp)

### Rebase interactively

Bạn có còn nhớ vấn đề được đưa ra trong bài trước đó là:  **"Việc có commit nhỏ sẽ khiến git log khó theo dõi."**  không nhỉ?

Và giờ thì bạn sẽ có câu trả lời, đó chính là  `git rebase`cùng với tham số  `-i (interactively)`  bạn sẽ dọn dẹp chúng một cách nhẹ nhàng như sau:

```none
# Di chuyển đến nhánh `login`
git checkout login
# Rebase lên dev interactively
git rebase dev -i

```

Sau đó bạn sẽ nhìn thấy một dãy các kết quả kiểu như vậy:

```none
pick ff80e85 A way to organize routes per module
pick 67cf18d Try Netlify Functions
pick 5546901 Add Dashboard view
pick 2a66ae3 Change layout
pick 58755b4 Add Books module, 404 page.
pick fd79cb9 Refactor. Reduce inline styling.
pick c671f60 Restyling 404 page.
pick 33ef874 Basic layout for book management page.
pick 49c423a Clean up UI a bit
pick 3aa2840 Init

```

Theo lý thuyết ở bài trước thì  **rebase**  sẽ đem từng commit và áp dụng lại theo thứ tự từ trên xuống dưới. Cũng chính bởi vậy mà bạn có thể thoải mái sắp xếp lại thứ tự của các commits trên.

Giờ bạn hay để ý đến  `lệnh pick`  ở phía trước mỗi commit. Hãy thử chỉnh sửa chúng xem sao! Tất nhiên bạn phải tuân thủ theo những quy tắc sau:

-   **pick (p):**  chuyển đổi vị trí của commit
-   **rework (r):**  áp dụng lại commit, và sửa commit message
-   **edit (e):**  áp dụng commit, nhưng dừng quá trình rebase lại để sửa code
-   **squash (s):**  kết hợp commit hiện tại vào commit trước nó
-   **fixup (f):**  giống như squash nhưng bỏ đi commit message
-   **exec (x):**  chạy một lệnh shell nào đó
-   **drop (d):**  bỏ, không sử dụng commit này

![](https://images.viblo.asia/00826257-af72-4f12-9ea2-562393a9d9ba.gif)

Đây chính là  **rebase interactively**. Tại đó, bạn hoàn toàn có thể thêm nhiều quyền để quản lý và sửa đổi commits theo ý mình, làm cho history sạch đẹp hơn.

## 4. Merge vào dev

Sau khi dọn dẹp nhánh  `login`  sạch đẹp, chúng ta có thể merge nhánh này vào  `dev`. Thông thường, Tí  _-- dev cứng nhất team --_  sẽ là người tiến hành kiểm tra và merge. Tí có hai cách tiếp cận:

### Cách 1: merge

Nó đơn giản là merge trực tiếp vào  `dev`  như thế này:

```none
# Chuyển qua nhánh `dev`
git checkout dev
# Merge `login` vào `dev`
git merge login

```

Vậy là thành công rồi.![](https://images.viblo.asia/3882d942-90c7-4497-b3b8-37f4e06b19f1.png)

Cách thức này gọi là  `merge fast-forward`. Tất cả commits của login đã được kết hợp vào dev. Boom! login biến mất khỏi thế gian như chưa hề tồn tại.

Lợi ích dễ thấy nhất của  `merge fast-forward`  là giúp cho history của bạn "thẳng như ruột ngựa", còn bất lợi là bạn không phân biệt được commits nào là của nhánh tính năng, cũng như thời điểm merge diễn ra. Trong trường hợp nhánh tính năng có quá nhiều commits nhỏ và dư thừa, chẳng hạn như những commits sửa lỗi chính tả, cập nhật thư viện..., history của bạn sẽ bị nhiễu.

Bên cạnh đó, chúng ta cũng có cách  `merge non-fast-forward:`

```none
git merge login --no-ff

```

Kết quả sẽ thay đổi như sau:![](https://images.viblo.asia/aed05f7f-8ccd-4bae-9870-52a637fbc70f.png)

Bằng cách này, bạn có thể giải quyết những bất lợi của  `merge fast-forward`

### Cách 2: rebase, squash và merge

Ngoài cách merge các commits của nhánh tính năng vào dev, bạn có thể  `rebase`  và  `squash`  tất cả commits lại làm một, sau đó tiến hành merge. Cách làm này giúp cho branch  `dev`  luôn ở trạng thái gọn gàng nhất, không chứa commit dư thừa. Trong trường hợp lý tưởng, history của  `dev`  sẽ giống như sau:  ![](https://images.viblo.asia/1ccb6fd7-5c29-4b5a-a5f0-628e9a4e2a63.png)  Để cách làm này phát huy tối đa hiệu quả, bạn nên viết commit message rõ ràng và chi tiết nhé.

## 5. Merge vào master

![😎](https://twemoji.maxcdn.com/2/72x72/1f60e.png)! Sau một thời gian quằn quại, cuối cùng team của Tí cũng đã ra được sản phẩm tương đối ổn. Giờ là lúc merge vào master và triển khai lên server.

Lúc này, cũng như khi merge vào dev, bạn có thể chọn  `merge`  (fast-forward hoặc non-fast-forward) hay  `rebase, squash và merge`.

## 6. Hotfix!!!

Hôm ấy, thứ 6, ngày 13. Tí chạy  `npm run build`  rồi rsync code ở  `master`  lên server. Mọi thứ hoàn toàn bình thường. Tí vào website và thấy click vào cái, có vẻ như mọi thứ đều ổn. Vừa định vươn vai về nghỉ thì bỗng nhiên điện thoại đổ chuông. Thì ra số leader của Tí, vừa bắt máy, thì Tí nhận được ngay lời nhắc "WEBSITE BỊ LỖI KÌA THANH NIÊN!!!"

Nếu bạn rơi vào trường hợp này, hãy bình tĩnh!

-   Tạo một branch mới từ master,  `fix-xxx`  chẳng hạn.
-   Từ từ mò ra lỗi trong đống code (vì bạn là dev cứng mà hihi).
-   Thế rồi  `git merge fix-xxx --no-ff`  vào  **cả hai nhánh  `master`  và  `dev`**  *(Nếu cần bạn có thể tham khảo lại  `merge non-fast-forward`  trong mục 4 cách 1). *

Bằng cách này, phần sửa lỗi sẽ xuất hiện ở cả hai branches, giúp history không bị rẽ nhánh bất ngờ.

## Bonus

### Có cần nhánh staging không?

Chuyện là công ty của Tí làm việc mới có một đội ngũ "thần bí" được gọi là QA/QC. Chính vị vậy, họ cần một nhánh riêng có tên gọi  **staging**. Nhánh này sẽ chứa phần code ở giữa  `master`  và  `dev`.

![](https://images.viblo.asia/e553e73a-ae4a-4558-be90-d3b706cda9fa.png)

Khi đó,  `staging`  được tách ra từ  `dev`, và khi code tương đối ổn định rồi thì có thể merge vào  `master`.

### Nên viết commit message như thế nào?

Nếu bạn theo chuẩn  **rebase, squash và merge**  thì chuyện viết commit message tốt rất quan trọng, vì nó là tài liệu để mô tả toàn bộ một tính năng. Có một vài gợi ý cho bạn để viết đây:

-   **Dòng đầu tiên không dài quá 80 chữ, luôn bắt đầu bằng động từ, ngắn gọn súc tích**  (Ví dụ:  `Add module Authentication`. Ngoài ra, bạn có thể chọn thêm tiền tố nếu cần thiết, chẳng hạn:  `Feature: Add module Authentication`  hay  `Fix: unable to get location params from URL`)
-   Bỏ trống hai dòng
-   Sau đó mô tả chi tiết về tính năng đang làm, những điểm cần lưu ý, phần nào của tính năng cần được cải thiện...
-   Khuyến khích bạn kèm theo chữ ký signature khi commit bằng  `git commit -s`

Ví dụ chút cho dễ hiểu vậy

```none
Add module Authentication


This module allows users to register/login into our website using
AWS Cognito account. Added routes:

* /auth/register
* /auth/login

Users after registration will receive a SMS to confirm their account.

TODO:

* Implement social identities
* Add Logout feature
* Add Forgot password feature

```

## Kết luận

Trải qua 2 kì, chúng ta có thể tóm tắt mọi thứ lại như sau:

-   Dự án nên được chia thành nhiều nhánh, bao gồm  `master`,  `dev`  và (có thể có  `staging`)
-   Các nhánh tính năng được chia ra từ  `dev`, phát triển độc lập, được  **rebase trước khi merge lại vào dev**
-   `Rebase`  có thể thay đổi một chút history, hoặc  `squash`  lại thành một commit duy nhất
-   `Merge`  có thể là  _fast-forward hoặc non-fast-forward_
-   `dev`  sẽ được merge vào  `master`  mỗi khi triển khai. (Trường hợp có  `staging`,  `dev`  sẽ được merge vào  `staging`, và  `staging`  sẽ được merge vào  `master`.)
-   Các nhánh hotfix sẽ được chia ra từ  `master`, sau đó  `merge --no-ff`  vào  **`master`**  và  **`dev`**

Dĩ nhiên bài viết chỉ mang tính tham khảo, vì còn có rất nhiều cách làm khác nhưng trên đây là workflow mình hay sử dụng. Và còn có rất nhiều những workflow hay hơn, nếu bạn biết hãy comment để cùng thảo luận nhé!
