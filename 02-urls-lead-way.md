# URLs Mở Đường

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã thấy cách một yêu cầu từ trình duyệt của người dùng đi từ trình duyệt đến “cửa trước” của Django. Bây giờ là lúc tìm hiểu cách Django xử lý các yêu cầu đó.

Một yêu cầu HTTP đến từ trình duyệt sẽ bao gồm một URL mô tả tài nguyên mà Django nên tạo ra. Vì URL có thể có nhiều dạng khác nhau, chúng ta phải hướng dẫn Django về các loại URL mà ứng dụng web của chúng ta có thể xử lý. Đây chính là nhiệm vụ của _cấu hình URL_ (URL configuration). Trong tài liệu Django, cấu hình URL được gọi tắt là URLconf.

URLconf nằm ở đâu? URLconf nằm ở đường dẫn module được thiết lập bởi thiết lập `ROOT_URLCONF` trong file settings của dự án. Nếu bạn chạy lệnh `startproject`, thì thiết lập đó sẽ có tên như `project.urls` trong đó “project” là tên bạn truyền vào lệnh. Nói cách khác, URLconf được đặt trong `project/urls.py`, ngay cạnh file `settings.py`.

Điều đó giải thích vị trí của file, nhưng chưa nói nhiều về cách nó hoạt động. Hãy cùng tìm hiểu sâu hơn.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. URLs Dẫn Đường
3. [Nhìn Vào Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Templates Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/)
8. [Giải Phẫu Một Ứng Dụng](https://www.mattlayman.com/understand-django/anatomy-of-an-application/)
9. [Xác Thực Người Dùng](https://www.mattlayman.com/understand-django/user-authentication/)
10. [Middleware Bạn Đi Đâu?](https://www.mattlayman.com/understand-django/middleware-do-you-go/)
11. [Phục Vụ Static Files](https://www.mattlayman.com/understand-django/serving-static-files/)
12. [Kiểm Thử Ứng Dụng](https://www.mattlayman.com/understand-django/test-your-apps/)
13. [Triển Khai Trang Web](https://www.mattlayman.com/understand-django/deploy-site-live/)
14. [Dữ Liệu Theo Từng Người Dùng Với Sessions](https://www.mattlayman.com/understand-django/sessions/)
15. [Hiểu Về Settings](https://www.mattlayman.com/understand-django/settings/)
16. [Quản Lý File Người Dùng](https://www.mattlayman.com/understand-django/media-files/)
17. [Lệnh Cho Ứng Dụng](https://www.mattlayman.com/understand-django/command-apps/)
18. [Tăng Tốc Với Django](https://www.mattlayman.com/understand-django/go-fast/)
19. [Bảo Mật Và Django](https://www.mattlayman.com/understand-django/secure-apps/)
20. [Mẹo Và Kỹ Thuật Gỡ Lỗi](https://www.mattlayman.com/understand-django/debugging-tips-techniques/)

## URLconf Hoạt Động Như Thế Nào

Hãy thử nghĩ về cấu hình URL như một danh sách các đường dẫn URL mà Django sẽ cố gắng khớp từ trên xuống dưới. Khi Django tìm thấy một đường dẫn phù hợp, yêu cầu HTTP sẽ được chuyển đến một đoạn mã Python liên kết với đường dẫn đó. Đoạn “mã Python” này được gọi là _view_ mà chúng ta sẽ tìm hiểu kỹ hơn sau. Hiện tại, hãy tin rằng view biết cách xử lý các yêu cầu HTTP.

Chúng ta có thể sử dụng một ví dụ về URLconf để minh họa.

```python
# project/urls.py
from django.urls import path

from application import views

urlpatterns = [
    path("", views.home),
    path("about/", views.about),
    path("contact/", views.contact),
    path("terms/", views.terms),
]
```

Những gì có ở đây rất phù hợp với mô tả ở trên: một danh sách các đường dẫn URL mà Django sẽ cố gắng khớp từ trên xuống dưới. Điểm mấu chốt của danh sách này là tên `urlpatterns`. Django sẽ coi danh sách trong biến `urlpatterns` là URLconf.

Thứ tự của danh sách này cũng rất quan trọng vì Django sẽ dừng việc quét danh sách ngay khi gặp một đường dẫn phù hợp. Ví dụ trên không có xung đột giữa các đường dẫn, nhưng hoàn toàn có thể tạo ra hai mục `path` khác nhau có thể khớp cùng một URL mà người dùng gửi lên. Tôi sẽ minh họa ví dụ về điều này sau khi chúng ta xem thêm một khía cạnh khác của path.

Chúng ta có thể làm một ví dụ để xem điều này hoạt động như thế nào với `www.example.com`. Khi xét một URL trong URLconf, Django sẽ bỏ qua scheme (`https://`), domain (`www.example.com`) và dấu gạch chéo đầu tiên khi so khớp. Mọi thứ còn lại sẽ được URLconf dùng để so khớp.

- Một yêu cầu đến `https://www.example.com/about/` sẽ được coi là `"about/"` trong quá trình so khớp và khớp với `path` thứ hai. Yêu cầu đó sẽ được chuyển đến view `views.about`.
- Một yêu cầu đến `https://www.example.com/` sẽ được coi là `""` trong quá trình so khớp và khớp với `path` đầu tiên. Yêu cầu đó sẽ được chuyển đến view `views.home`.

> _Lưu ý: Bạn có thể nhận thấy rằng các URL của Django kết thúc bằng dấu gạch chéo. Hành vi này là do một lựa chọn trong _[triết lý thiết kế](https://docs.djangoproject.com/en/4.1/misc/design-philosophies/#definitive-urls)_ của Django. Thực tế, nếu bạn cố gắng truy cập một URL như_ `https://www.example.com/about`_, Django sẽ chuyển hướng yêu cầu đến cùng URL đó nhưng có thêm dấu gạch chéo ở cuối nhờ thiết lập_ `APPEND_SLASH`[_mặc định_](https://docs.djangoproject.com/en/4.1/ref/settings/#append-slash)_._

## `path` Trước Mắt Chúng Ta

Phần chuỗi trong `path` (ví dụ, `"about/"`) được gọi là _route_. Một route có thể là một chuỗi đơn giản như bạn đã thấy, nhưng nó cũng có thể bao gồm các cấu trúc đặc biệt khác với tính năng gọi là _converters_. Khi bạn sử dụng converter, bạn có thể trích xuất thông tin từ URL để view sử dụng sau này. Hãy xem một path như sau:

```python
    path(
        "blog/<int:year>/<slug:slug>/",
        views.blog_post
    ),
```

Hai converter trong path này là:

- `<int:year>`
- `<slug:slug>`

Việc sử dụng dấu ngoặc nhọn và một số [tên dành riêng](https://docs.djangoproject.com/en/4.1/topics/http/urls/#path-converters) khiến Django thực hiện việc phân tích cú pháp bổ sung trên một URL. Mỗi converter có một số quy tắc mong đợi riêng.

- Converter `int` phải khớp với một số nguyên.
- Converter `slug` phải khớp với một slug. Slug là một thuật ngữ báo chí xuất hiện trong Django vì Django bắt nguồn từ một dự án của một tờ báo ở Kansas. Một slug là một chuỗi có thể bao gồm ký tự, số, dấu gạch ngang và dấu gạch dưới.

Với các định nghĩa converter đó, hãy so sánh với một số URL!

- `https://www.example.com/blog/2020/urls-lead-way/` - KHỚP!
- `https://www.example.com/blog/twenty-twenty/urls-lead-way/` - KHÔNG KHỚP.
- `https://www.example.com/blog/0/life-in-rome/` - KHỚP! Ừm, có thể không phải điều chúng ta muốn. Hãy xem xét điều đó sớm.

Bây giờ chúng ta có thể quay lại vấn đề về thứ tự từ trước. Hãy xem hai path này với thứ tự khác nhau:

```python
    path(
        "blog/<int:year>/",
        views.blog_by_year
    ),
    path(
        "blog/2020/",
        views.blog_for_twenty_twenty
    ),

# vs.

    path(
        "blog/2020/",
        views.blog_for_twenty_twenty
    ),
    path(
        "blog/<int:year>/",
        views.blog_by_year
    ),
```

Ở thứ tự đầu tiên, converter sẽ khớp với bất kỳ số nguyên nào sau `blog/`, bao gồm cả `https://www.example.com/blog/2020/`. Điều đó có nghĩa là thứ tự đầu tiên sẽ không bao giờ gọi view `blog_for_twenty_twenty` vì Django sẽ khớp các mục `path` theo thứ tự.

Ngược lại, ở thứ tự thứ hai, `blog/2020/` sẽ được chuyển đến `blog_for_twenty_twenty` đúng cách vì nó được khớp trước. Điều đó có nghĩa là có một bài học cần nhớ ở đây:

‌

> _Khi thêm các mục_ `path` _khớp với một dải giá trị bằng converter (như ví dụ năm ở trên), hãy chắc chắn đặt chúng **sau** các mục cụ thể hơn._

## Một Góc Nhìn Ngắn Gọn Về Views

Các converter làm gì với dữ liệu bổ sung này? Thật khó để giải thích mà không đề cập đến views. Bài tiếp theo sẽ nói kỹ hơn về views, nhưng đây là phần giới thiệu.

Một view là đoạn mã nhận một request và trả về một response. Sử dụng gợi ý kiểu dữ liệu tùy chọn của Python, đây là một ví dụ sẽ gửi response `Hello World`.

```python
from django.http import (
    HttpRequest,
    HttpResponse
)

def some_view(
    request: HttpRequest
) -> HttpResponse:
    return HttpResponse('Hello World')
```

`HttpRequest` là định dạng đã được Django chuyển đổi của một yêu cầu HTTP, được gói trong một lớp tiện lợi. Tương tự, `HttpResponse` là thứ chúng ta có thể dùng để Django chuyển đổi dữ liệu phản hồi của chúng ta thành một phản hồi HTTP đúng chuẩn để gửi lại cho trình duyệt người dùng.

Bây giờ chúng ta có thể xem lại một trong các converter.

```python
    path(
        "blog/<int:year>/",
        views.blog_by_year
    ),
```

Với converter này trong route, `blog_by_year` sẽ trông như thế nào?

```python
# application/views.py
from django.http import HttpResponse

def blog_by_year(request, year):
    # ... some code to handle the year
    data = 'Some data set by code above'
    return HttpResponse(data)
```

Django bắt đầu bộc lộ một số điểm hay ở đây! Converter đã làm rất nhiều việc tẻ nhạt cho chúng ta. Tham số `year` do Django truyền vào sẽ là một số nguyên vì Django đã thực hiện việc phân tích chuỗi và chuyển đổi.

Nếu ai đó gửi `/blog/not_a_number/`, Django sẽ trả về phản hồi Not Found vì `not_a_number` không thể là một số nguyên. Lợi ích của điều này là chúng ta không phải thêm logic kiểm tra bổ sung trong `blog_by_year` để xử lý trường hợp kỳ lạ khi `year` không giống một con số. Tính năng này thực sự tiết kiệm thời gian! Nó giúp mã của bạn sạch hơn _và_ xử lý chính xác hơn.

Còn ví dụ kỳ lạ khác mà chúng ta đã thấy trước đó là `/blog/0/life-in-rome/` thì sao? Đường dẫn đó sẽ khớp với pattern ở phần trước, nhưng giả sử chúng ta muốn chỉ khớp với năm có bốn chữ số. Làm sao để làm được điều đó? Chúng ta có thể dùng biểu thức chính quy.

## Đường Dẫn Biểu Thức Chính Quy

Biểu thức chính quy là một tính năng lập trình thường được ví như một chiếc cưa máy: _chúng cực kỳ mạnh mẽ, nhưng bạn có thể tự cắt chân mình nếu không cẩn thận._

Biểu thức chính quy có thể diễn đạt các mẫu ký tự phức tạp một cách ngắn gọn. Sự ngắn gọn này thường khiến biểu thức chính quy bị mang tiếng là khó hiểu. Tuy nhiên, khi sử dụng cẩn thận, chúng rất hiệu quả.

Một biểu thức chính quy (thường được viết tắt là “regex”) sẽ khớp các mẫu phức tạp trong chuỗi. Điều này nghe có vẻ giống hệt vấn đề về năm của blog! Trong vấn đề của chúng ta, chúng ta muốn chỉ khớp với số nguyên có bốn chữ số. Hãy xem một giải pháp mà Django có thể xử lý rồi phân tích ý nghĩa của nó.

Nhắc lại, giải pháp này sẽ khớp với một đường dẫn URL như `blog/2020/urls-lead-way/`.

Lưu ý, chúng ta dùng hàm `re_path()` để khớp biểu thức chính quy thay vì `path()`.

```python
re_path(
    r"^blog/(?P<year>[0-9]{4})/(?P<slug>[\w-]+)/$",
    views.blog_post
),
```

Chuỗi “kỳ lạ” này hoạt động giống hệt ví dụ trước **ngoại trừ** việc nó chính xác hơn về việc chỉ cho phép năm có bốn chữ số. Chuỗi “kỳ lạ” này cũng có một cái tên. Nó được gọi là _mẫu regex_ (regex pattern). Khi mã Django chạy, nó sẽ kiểm tra các đường dẫn URL dựa trên các quy tắc được định nghĩa trong mẫu này.

Để hiểu cách nó hoạt động, chúng ta cần biết ý nghĩa của từng phần trong mẫu. Hãy giải thích mẫu này từng đoạn một.

- Chuỗi bắt đầu bằng `r"` vì nó là chuỗi raw trong Python. Điều này được dùng vì biểu thức chính quy sử dụng `\` rất nhiều. Nếu không dùng chuỗi raw, lập trình viên sẽ phải escape dấu gạch chéo ngược liên tục bằng cách dùng `\\`.
- Dấu mũ `^` nghĩa là “mẫu phải _bắt đầu_ từ đây.” Nhờ dấu mũ, một path bắt đầu như `myblog/...` sẽ không khớp.
- `blog/` là phần khớp đúng từng ký tự. Các ký tự này phải khớp chính xác.
- Phần trong ngoặc đơn `(?P<year>[0-9]{4})` là một _capture group_. `?P<year>` là tên gán cho capture group và tương tự như phần bên phải dấu hai chấm trong converter như `<int:year>`. Tên này cho phép Django truyền nội dung vào một tham số tên là `year` cho view. Phần còn lại của capture group, `[0-9]{4}`, là phần mẫu thực sự sẽ khớp. `[0-9]` là _character class_ nghĩa là “khớp với bất kỳ số nào từ 0 đến 9.” `{4}` nghĩa là phải khớp **chính xác** bốn lần. Đây là sự chính xác mà `re_path` mang lại mà converter `int` không thể!
- Dấu gạch chéo `/` giữa các capture group là ký tự khớp đúng từng ký tự.
- Capture group thứ hai, `(?P<slug>[\w-]+)`, sẽ đưa bất kỳ giá trị nào nó khớp vào tham số tên là `slug`. Character class `[\w-]` chứa hai loại ký tự. `\w` nghĩa là bất kỳ ký tự từ nào bạn có thể có trong ngôn ngữ tự nhiên, số và dấu gạch dưới. Loại ký tự còn lại là dấu gạch ngang `-`. Cuối cùng, dấu cộng `+` nghĩa là character class phải khớp ít nhất 1 lần trở lên.
- Dấu gạch chéo cuối cùng cũng là ký tự khớp đúng từng ký tự.
- Để kết thúc mẫu, dấu đô la `$` đóng vai trò ngược lại với dấu mũ và nghĩa là “mẫu phải _kết thúc_ ở đây.” Do đó, `blog/2020/some-slug/another-slug/` sẽ không khớp.

Lưu ý rằng bạn không thể trộn lẫn kiểu `path` và kiểu chuỗi `re_path`. Ví dụ trên phải mô tả slug bằng biểu thức chính quy thay vì dùng converter slug (tức là `<slug:slug>`).

Chúc mừng bạn! Đây chắc chắn là phần khó nhất của bài viết này. Nếu bạn hiểu những gì chúng ta đã làm với `re_path`, phần còn lại sẽ rất dễ dàng. Nếu chưa, _đừng lo lắng!_ Nếu bạn muốn biết thêm về biểu thức chính quy, hãy nhớ rằng mọi thứ tôi mô tả trong mẫu này _không_ phải là đặc trưng của Django. Thay vào đó, đây là hành vi tích hợp sẵn của Python. Bạn có thể tìm hiểu thêm về biểu thức chính quy từ [Regular Expression HOWTO của Python](https://docs.python.org/3/howto/regex.html).

Biết rằng sức mạnh của `re_path` luôn ở đó có thể giúp bạn sau này trên hành trình Django, ngay cả khi bạn chưa cần đến nó bây giờ.

## Nhóm Các URL Liên Quan

Đến thời điểm này, chúng ta đã xem các route riêng lẻ mà bạn có thể ánh xạ trong URLconf. Vậy chúng ta có thể làm gì khi một nhóm view liên quan nên chia sẻ một đường dẫn chung? Tại sao lại muốn làm như vậy?

Hãy tưởng tượng bạn đang xây dựng một dự án giáo dục. Trong dự án, bạn có trường học, học sinh và các khái niệm liên quan đến giáo dục khác. Bạn _có thể_ làm như sau:

```python
# project/urls.py
from django.urls import path

from schools import (
    views as schools_views,
)
from students import (
    views as students_views,
)

urlpatterns = [
    path(
        "schools/", schools_views.index
    ),
    path(
        "schools/<int:school_id>/",
        schools_views.school_detail,
    ),
    path(
        "students/",
        students_views.index,
    ),
    path(
        "students/<int:student_id>/",
        students_views.student_detail,
    ),
]
```

Cách tiếp cận này vẫn hoạt động tốt, nhưng nó buộc URLconf gốc phải biết về tất cả các view được định nghĩa trong từng app, `schools` và `students`. Thay vào đó, chúng ta có thể dùng `include` để xử lý tốt hơn.

```python
# project/urls.py
from django.urls import include, path

urlpatterns = [
    path(
        "schools/",
        include("schools.urls"),
    ),
    path(
        "students/",
        include("students.urls"),
    ),
]
```

Sau đó, trong mỗi ứng dụng, chúng ta sẽ có như sau:

```python
# schools/urls.py
from django.urls import path

from schools import views

urlpatterns = [
    path("", views.index),
    path(
        "<int:school_id>/",
        views.school_detail
    ),
]
```

Việc sử dụng `include` giúp mỗi app Django tự chủ trong việc xác định các view cần thiết. Dự án có thể “không biết” về những gì ứng dụng đang làm.

Ngoài ra, việc lặp lại `schools/` hoặc `students/` được loại bỏ so với ví dụ đầu tiên. Khi Django xử lý một route, nó sẽ khớp phần đầu của route và chuyển _phần còn lại_ cho URLconf được định nghĩa trong từng app. Theo cách này, cấu hình URL có thể tạo thành một cây, trong đó URLconf gốc là nơi mọi yêu cầu bắt đầu, nhưng từng ứng dụng sẽ xử lý chi tiết khi một yêu cầu được chuyển đến app phù hợp.

## Đặt Tên Cho URL

Chúng ta đã xem các cách chính để định nghĩa URL với `path`, `re_path` và `include`. Có một khía cạnh khác cần xem xét. Làm thế nào để tham chiếu đến các URL ở những nơi khác trong mã? Hãy xem view (hơi ngớ ngẩn) này:

```python
# application/views.py
from django.http import (
    HttpResponseRedirect
)

def old_blog_categories(request):
    return HttpResponseRedirect(
        "/blog/categories/"
    )
```

Redirect là khi người dùng cố gắng truy cập một trang và được trình duyệt chuyển đến nơi khác. Có nhiều cách tốt hơn để xử lý redirect so với ví dụ này, nhưng view này minh họa một điểm khác. Điều gì sẽ xảy ra nếu bạn muốn cấu trúc lại dự án để các danh mục blog chuyển từ `/blog/categories/` sang `/marketing/blog/categories/`? Ở dạng hiện tại, chúng ta sẽ phải sửa view này và bất kỳ view nào khác tham chiếu trực tiếp đến route đó.

Thật lãng phí thời gian! Django cung cấp cho chúng ta công cụ để đặt tên cho các path độc lập với route cụ thể. Chúng ta làm điều này với tham số `name` của `path`.

```python
# project/urls.py
from django.urls import path

from blog import views

urlpatterns = [
    ...
    path(
        "/marketing/blog/categories/",
        views.categories,
        name="blog_categories"
    ),
    ...
]
```

Điều này giúp chúng ta có `blog_categories` là một tên độc lập với route `/marketing/blog/categories/`. Để sử dụng tên đó, chúng ta cần `reverse` như một đối tác. View đã chỉnh sửa của chúng ta như sau:

```python
# application/views.py
from django.http import (
    HttpResponseRedirect
)
from django.urls import reverse

def old_blog_categories(request):
    return HttpResponseRedirect(
        reverse("blog_categories")
    )
```

Nhiệm vụ của `reverse` là tra cứu bất kỳ tên path nào và trả về route tương ứng. Điều đó có nghĩa là:

```python
reverse("blog_categories") == "/marketing/blog/categories/"
```

Ít nhất là cho đến khi bạn muốn thay đổi nó lần nữa. 😁

## Khi Tên Bị Trùng

Điều gì xảy ra nếu bạn có nhiều URL muốn đặt cùng một `name`? Ví dụ, `index` hoặc `detail` là những tên phổ biến mà bạn có thể muốn dùng. Chúng ta có thể tham khảo [The Zen of Python](https://www.python.org/dev/peps/pep-0020/) để xin lời khuyên.

> _The Zen of Python, by Tim Peters_
>
> _Đẹp hơn là xấu._
>
> _…_
>
> _**Namespaces là một ý tưởng tuyệt vời – hãy dùng nhiều hơn nữa!**_

Namespace có thể là khái niệm mới nếu bạn chưa lập trình lâu. Chúng là _không gian chung cho các tên_. Có thể điều này đã rõ, nhưng tôi nhớ mình từng gặp khó khăn với khái niệm này khi mới bắt đầu lập trình.

Để làm phép so sánh với thực tế, hãy dùng hình ảnh những chiếc xô. Hãy tưởng tượng bạn có hai quả bóng đỏ và hai quả bóng xanh. Đặt mỗi quả bóng mỗi màu vào hai chiếc xô được dán nhãn “A” và “B.” Nếu tôi muốn một quả bóng xanh cụ thể, tôi không thể nói “hãy đưa tôi quả bóng xanh” vì điều đó mơ hồ. Thay vào đó, để lấy quả bóng cụ thể, tôi cần nói “hãy đưa tôi quả bóng xanh trong xô B.” Trong trường hợp này, chiếc xô là namespace.

Ví dụ chúng ta dùng cho schools và students có thể minh họa ý tưởng này trong mã. Cả hai app đều có một view `index` để đại diện cho gốc của từng phần trong dự án (tức là `schools/` và `students/`). Nếu chúng ta muốn tham chiếu đến các view đó, chúng ta sẽ cố chọn tên dễ nhất là `index`. Thật không may, nếu bạn chọn `index`, Django sẽ không biết view nào là đúng cho `index`. Tên bị mơ hồ.

Một giải pháp là tự tạo namespace bằng cách thêm tiền tố cho `name` như `schools_`. Vấn đề của cách này là URLconf bị lặp lại.

```python
# schools/urls.py
from django.urls import path

from schools import views

urlpatterns = [
    path(
        "",
        views.index,
        name="schools_index"
    ),
    path(
        "<int:school_id>/",
        views.school_detail,
        name="schools_detail"
    ),
]
```

Django cung cấp một cách khác giúp bạn giữ tên ngắn hơn.

```python
# schools/urls.py
from django.urls import path

from schools import views

app_name = "schools"
urlpatterns = [
    path("", views.index, name="index"),
    path(
        "<int:school_id>/",
        views.school_detail,
        name="detail"
    ),
]
```

Bằng cách thêm `app_name`, chúng ta báo cho Django biết các view này nằm trong một namespace. Bây giờ khi muốn lấy một URL, chúng ta dùng tên namespace và tên URL, nối với nhau bằng dấu hai chấm.

```
reverse("schools:index") == "/schools/"
```

Đây là một tiện ích khác mà Django mang lại để giúp trải nghiệm phát triển ứng dụng của bạn dễ dàng hơn.

Như vậy là chúng ta đã kết thúc chủ đề về URL. Đến giờ, chúng ta đã biết cách:

- Tạo cấu hình URL bằng cách tạo một module với danh sách `urlpatterns`.
- Tạo URL với `path` và `re_path`.
- Sử dụng converter để trích xuất thông tin cho view.
- Sử dụng biểu thức chính quy để diễn đạt dữ liệu URL phức tạp hơn.
- Nhóm các URL liên quan với `include`.
- Tham chiếu đến một URL bằng `name`.
- Gom các tên liên quan vào một namespace.

Trong bài tiếp theo, chúng ta sẽ tìm hiểu về views. Bài viết này chỉ mới đưa ra định nghĩa ngắn gọn về view là gì. Django cung cấp cho chúng ta rất nhiều lựa chọn phong phú khi làm việc với views. Chúng ta sẽ khám phá:

- Hàm view
- Lớp view
- Một số view hỗ trợ tích hợp sẵn
- Decorator giúp tăng sức mạnh cho view.
