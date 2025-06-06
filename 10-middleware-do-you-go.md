# Middleware Bạn Đi Đâu?

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã tìm hiểu về hệ thống xác thực tích hợp sẵn. Bài viết đó giúp bạn có cơ hội xem model `User`, các cách đăng nhập người dùng với các công cụ xác thực của Django, và các tính năng giúp kiểm soát phân quyền hoạt động. Trong chủ đề đó, middleware đã xuất hiện như một thành phần không thể thiếu. Bây giờ chúng ta sẽ tìm hiểu sâu hơn về middleware và vai trò của nó trong một dự án Django.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/)
8. [Giải Phẫu Một Ứng Dụng](https://www.mattlayman.com/understand-django/anatomy-of-an-application/)
9. [Xác Thực Người Dùng](https://www.mattlayman.com/understand-django/user-authentication/)
10. Middleware Bạn Đi Đâu?
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

## Nên Nghĩ Về Middleware Như Thế Nào?

Để bắt đầu chủ đề này, hãy xác định middleware tồn tại ở đâu trong một dự án Django.

Middleware là đoạn mã tồn tại ở giữa. “Ở giữa cái gì?” bạn có thể hỏi. “Ở giữa” là đoạn mã thực thi giữa lúc một `HttpRequest` được framework tạo ra và lúc mã bạn viết được Django gọi. “Ở giữa” cũng có thể chỉ đoạn mã thực thi _sau_ khi view của bạn hoàn thành nhưng trước khi Django chuyển đổi `HttpResponse` thành bytes để gửi qua mạng về trình duyệt.

Bạn đã từng ăn kẹo Everlasting Gobstopper chưa? Không, tôi không nói đến loại trong phim Willy Wonka mà tồn tại mãi mãi. Everlasting Gobstopper là một loại kẹo cứng, nhiều lớp, thay đổi màu sắc và hương vị khi bạn ngậm trong miệng cho đến khi đến phần mềm ở giữa.

Middleware cũng giống như các lớp kẹo đó và mã view của bạn giống như phần mềm ở giữa. So sánh này sẽ không còn đúng nếu bạn nghĩ về cách ai đó ăn viên kẹo.

Với viên kẹo, bạn trải nghiệm từng lớp một cho đến khi đến giữa và kết thúc. Một so sánh hợp lý hơn với middleware là bạn đào _xuyên qua_ các lớp và đi ra phía bên kia, trải nghiệm các lớp theo thứ tự ngược lại so với lúc đi vào.

Dưới đây là sơ đồ tất cả các middleware mặc định được thêm vào khi bạn chạy lệnh `startproject`. Nếu bạn là người học trực quan và không thấy ví dụ về kẹo gobstopper hữu ích, hy vọng hình này sẽ minh họa rõ hơn.

```text
               +--------- SecurityMiddleware --------------+
               |+-------- SessionMiddleware --------------+|
               ||+------- CommonMiddleware --------------+||
               |||+------ CsrfViewMiddleware -----------+|||
               ||||+----- AuthenticationMiddleware ----+||||
               |||||+---- MessageMiddleware ----------+|||||
               ||||||+--- XFrameOptionsMiddleware ---+||||||
               |||||||                               |||||||
HttpRequest =================> view function ==================> HttpResponse
               |||||||                               |||||||
```

Django làm sao để tạo ra các lớp này? Khi bạn khởi động Django với một application server như Gunicorn, bạn phải cung cấp cho application server đường dẫn đến module WSGI của bạn. Chúng ta sẽ bàn về application server ở chủ đề sau, nhưng hiện tại, hãy biết rằng một application server có thể chạy ứng dụng Django của bạn. Nếu thư mục dự án chứa file settings của bạn tên là `project`, thì gọi Gunicorn sẽ như sau:

```shell
gunicorn project.wsgi
```

Bạn sẽ có thiết lập này nếu bạn chạy `django-admin startproject project .` (bao gồm dấu chấm cuối), nhưng điều thực sự cần cho application server là vị trí file `wsgi.py` trong dự án của bạn, _dưới dạng đường dẫn module_. Hãy điều chỉnh cho phù hợp với nhu cầu của bạn.

Nhớ lại từ bài đầu tiên của loạt bài rằng WSGI là viết tắt của Web Server Gateway Interface và là lớp chung mà các ứng dụng web Python đồng bộ phải triển khai để hoạt động với application server Python. Bên trong module `project.wsgi` này có một hàm gọi là `get_wsgi_application`, được import từ `django.core.wsgi`.

`get_wsgi_application` làm hai việc:

- Gọi `django.setup` để thực hiện tất cả cấu hình khởi động mà chúng ta đã thấy ở bài trước
- Trả về một instance của `WSGIHandler`

Bạn có thể đoán rằng `WSGIHandler` được thiết kế để làm việc với WSGI, nhưng nó cũng là một subclass của `django.core.handlers.base.BaseHandler`. Lớp base handler này là nơi Django xử lý việc thiết lập middleware.

Base handler bao gồm một phương thức `load_middleware`. Phương thức này có nhiệm vụ lặp qua tất cả middleware được liệt kê trong thiết lập `MIDDLEWARE` của bạn. Khi lặp qua `MIDDLEWARE`, mục tiêu chính của phương thức là đưa từng middleware vào _chuỗi middleware_.

> _Chuỗi middleware chính là viên kẹo gobstopper của Django._

Chuỗi này đại diện cho từng instance middleware của Django, xếp lớp lại với nhau, để tạo ra hiệu ứng mong muốn là cho phép một request và response đi qua từng middleware.

Ngoài việc xây dựng chuỗi middleware, `load_middleware` còn phải thực hiện một số cấu hình quan trọng khác.

- Phương thức này xử lý cả middleware đồng bộ và bất đồng bộ. Khi Django tăng cường hỗ trợ phát triển async, các phần bên trong Django cần quản lý sự khác biệt này. `load_middleware` sẽ điều chỉnh tùy theo những gì nó phát hiện về class middleware.
- Phương thức này đăng ký một middleware với các _tập_ middleware nhất định dựa trên sự hiện diện của các hook method khác nhau. Chúng ta sẽ bàn về các hook này ở phần sau của bài viết.

Như vậy là đã giải thích cấu trúc middleware và cách tất cả middleware tương tác với vòng đời request và response, nhưng middleware thực sự làm gì?

Chúng ta có thể dùng middleware cho rất nhiều mục đích khác nhau. Nhờ chuỗi middleware, một HTTP request thành công sẽ đi qua mọi middleware. Tính chất này khiến middleware rất lý tưởng cho những đoạn mã mà bạn muốn thực thi toàn cục cho dự án Django của mình.

Ví dụ, hãy nhớ lại bài trước về [Xác Thực Người Dùng](https://www.mattlayman.com/understand-django/user-authentication/). Trong bài đó, chúng ta thấy hệ thống xác thực của Django phụ thuộc vào `AuthenticationMiddleware`. Middleware này chỉ có một nhiệm vụ là thêm thuộc tính `user` vào mọi object `HttpRequest` đi qua ứng dụng trước khi request đến mã view.

`AuthenticationMiddleware` làm nổi bật một số đặc điểm tốt cho middleware trong Django.

- Một middleware lý tưởng nên có mục tiêu hẹp hoặc đơn lẻ.
- Một middleware nên chạy càng ít mã càng tốt.

_Tại sao?_ Lý do lại liên quan đến chuỗi middleware. Vì HTTP request sẽ đi qua mọi middleware trong chuỗi, nên _mọi middleware sẽ thực thi cho mọi request._ Nói cách khác, mỗi middleware đều mang lại chi phí hiệu năng cho mỗi request.

Có **một ngoại lệ** cho hành vi này của chuỗi. Một middleware ở đầu chuỗi có thể ngăn các middleware phía sau chạy tiếp.

Ví dụ, `SecurityMiddleware` là middleware đầu tiên trong chuỗi mặc định của một dự án tạo bằng `startproject`. Middleware này được thiết kế để thực hiện một số kiểm tra nhằm giữ cho ứng dụng an toàn. Một kiểm tra là xem kết nối có an toàn không (tức là request dùng HTTPS) nếu HTTPS đã được cấu hình. Nếu một request đến ứng dụng và dùng HTTP thay vì HTTPS, middleware này có thể trả về một `HttpResponsePermanentRedirect` chuyển hướng đến cùng URL nhưng với `https://` và ngăn không cho các middleware còn lại chạy tiếp.

Ngoài hành vi ngoại lệ này, điều quan trọng là nhớ rằng, trong hầu hết trường hợp, mỗi middleware sẽ chạy cho mỗi request. Chúng ta nên lưu ý đến khía cạnh hiệu năng này khi tự tạo middleware.

Giờ chúng ta đã sẵn sàng tìm hiểu cách tự tạo middleware!

## Làm Sao Viết Middleware Tùy Biến?

Giả sử bạn đã tìm được lý do hợp lý để tạo một middleware. Bạn cần một thứ gì đó xảy ra với mọi request và chức năng đó có mục tiêu hẹp.

Bạn có thể bắt đầu với một định nghĩa middleware rỗng. Trong ví dụ này, chúng ta sẽ đặt middleware vào file `middleware.py`.

```python
# project/middleware.py
class AwesomeMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        return self.get_response(
            request
        )
```

Sau khi tạo middleware, bạn thêm nó vào settings.

```python
# project/settings.py

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    ...,
    'middleware.AwesomeMiddleware',
]
```

_Vậy là xong!_ Middleware tùy biến này không làm gì ngoài việc làm hiệu năng chậm đi một chút vì nó thêm một lần gọi hàm cho mỗi request. Vì tôi đặt middleware ở cuối danh sách `MIDDLEWARE`, nó sẽ là middleware cuối cùng chạy trước khi view nhận request và là middleware đầu tiên có cơ hội xử lý response.

Chúng ta có thể phân tích cách class này hoạt động.

- Phương thức `__init__` nhận một callable thường được đặt tên là `get_response`. Middleware được tạo ra trong quá trình `load_middleware` và callable này là phần then chốt giúp chuỗi middleware hoạt động. Callable này sẽ gọi middleware tiếp theo hoặc view tùy vào vị trí middleware hiện tại trong chuỗi.
- Phương thức `__call__` biến instance middleware thành một callable. Phương thức này phải gọi `get_response` để đảm bảo chuỗi không bị đứt đoạn.

Nếu bạn muốn làm thêm điều gì đó, bạn có thể thay đổi phương thức `__call__`. Bạn có thể sửa `__call__` để xử lý trước hoặc sau khi gọi `get_response`. Trong vòng đời request/response, thay đổi trước `get_response` sẽ xảy ra trước khi view được gọi, còn thay đổi sau `get_response` có thể xử lý chính `response` hoặc bất kỳ xử lý hậu request nào khác.

Giả sử chúng ta muốn middleware ví dụ ghi lại một số thông tin thời gian. Chúng ta có thể cập nhật mã như sau:

```python
# project/middleware.py
import logging
import time

logger = logging.getLogger(__name__)


class AwesomeMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        before_timestamp = time.time()
        logger.info(
            f"Tracking {before_timestamp}"
        )

        response = self.get_response(request)

        after_timestamp = time.time()
        delta = after_timestamp - before_timestamp
        logger.info(
            f"Tracking {after_timestamp} for a delta of {delta}"
        )

        return response
```

Chúng ta chưa bàn về logging, nhưng bạn có thể hiểu nó là ghi lại thông điệp ra một nguồn nào đó như file.

Ví dụ này hoạt động như một bộ đo hiệu năng thô sơ. Nếu bạn muốn đo thời gian phản hồi của một view, middleware này sẽ làm được điều đó. Nhược điểm là nó không cho bạn biết _view nào_ được ghi lại. Thôi nào, đây chỉ là ví dụ vui thôi mà! 🤪

Hy vọng bạn bắt đầu thấy middleware hữu ích như thế nào. Nhưng khoan đã! Middleware còn làm được nhiều hơn thế.

Một middleware Django có thể định nghĩa bất kỳ ba hook method nào mà Django sẽ gọi ở các phần khác nhau của vòng đời request/response. Ba phương thức đó là:

- `process_exception` - Hook này được gọi bất cứ khi nào một view raise exception. Điều này có thể bao gồm cả exception chưa được bắt từ view, nhưng hook này cũng nhận các exception được raise có chủ đích như `Http404`.
- `process_template_response` - Hook này được gọi bất cứ khi nào một view trả về response trông giống template response (tức là object response có phương thức `render`).
- `process_view` - Hook này được gọi ngay trước khi view được gọi.

Quay lại ví dụ vui của chúng ta, ta có thể làm nó bớt ngớ ngẩn hơn bằng cách dùng hook `process_view`. Hãy xem chúng ta có thể làm gì:

```python
# project/middleware.py
import logging
import time

logger = logging.getLogger(__name__)


class AwesomeMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        before_timestamp = time.time()
        logger.info(
            f"Tracking {before_timestamp}"
        )

        response = self.get_response(request)

        after_timestamp = time.time()
        delta = after_timestamp - before_timestamp
        logger.info(
            f"Tracking {after_timestamp} for a delta of {delta}"
        )

        return response

    def process_view(self, request, view_func, view_args, view_kwargs):
        logger.info(
            f"Running {view_func.__name__} view"
        )
```

Giờ middleware của chúng ta dùng khả năng reflection của Python để ghi lại tên view. Nếu truy cập admin Django với một user chưa xác thực, log có thể ghi lại như sau:

```text
Tracking 1607438038.232886
Running login view
Tracking 1607438038.261855 for a delta of 0.02896881103515625
```

Middleware này vẫn còn có thể cải tiến nhiều, nhưng bạn có thể thấy các hook giúp middleware có thể có chức năng nâng cao hơn.

Ví dụ về middleware `process_exception`, hãy nghĩ đến một dịch vụ thu thập và báo cáo exception để theo dõi sức khỏe ứng dụng của bạn. Có nhiều dịch vụ như [Rollbar](https://rollbar.com/) và [Sentry](https://sentry.io/welcome/). Tôi tình cờ dùng Rollbar nên sẽ nói về nó. Bạn có thể thấy từ [mã nguồn pyrollbar](https://github.com/rollbar/pyrollbar/blob/8d116a374f2c54da886972f7da7c289e317bbd8a/rollbar/contrib/django/middleware.py#L268) rằng dịch vụ này gửi thông tin exception từ hook `process_exception` đến Rollbar qua hàm `rollbar.report_exc_info`. Nếu không có middleware, việc thu thập và báo cáo exception sẽ _khó hơn rất nhiều_.

Muốn tìm hiểu thêm về các hook? Bạn có thể xem chi tiết về các hook này trong [tài liệu middleware](https://docs.djangoproject.com/en/4.1/topics/http/middleware/#other-middleware-hooks).

## Django Bao Gồm Những Middleware Nào?

Chúng ta đã xem xét mô hình tư duy về middleware và chi tiết cách một middleware hoạt động. Vậy Django bao gồm những middleware nào trong framework?

Danh sách đầy đủ các middleware tích hợp có trong [tài liệu tham khảo middleware](https://docs.djangoproject.com/en/4.1/ref/middleware/). Tôi sẽ mô tả những class middleware phổ biến hoặc hữu ích nhất mà Django cung cấp.

- `AuthenticationMiddleware` - Chúng ta đã gặp middleware này khi tìm hiểu hệ thống xác thực. Nhiệm vụ của middleware này là thêm thuộc tính `user` vào object `HttpRequest`. Chỉ một thuộc tính nhỏ `user` này đã cung cấp sức mạnh cho nhiều tính năng của hệ thống xác thực.
- `CommonMiddleware` - Middleware này hơi “dị”. Nó xử lý nhiều thiết lập của Django để kiểm soát một số khía cạnh của dự án. Ví dụ, thiết lập `APPEND_SLASH` sẽ chuyển hướng một request như `example.com/accounts` thành `example.com/accounts/`. Thiết lập này chỉ hoạt động nếu có `CommonMiddleware`.
- `CsrfViewMiddleware` - Trong bài về forms, tôi đã nhắc đến token CSRF. Nhắc lại, đây là một tính năng bảo mật giúp bảo vệ dự án của bạn khỏi các nguồn độc hại muốn gửi dữ liệu xấu lên trang. `CsrfViewMiddleware` đảm bảo token CSRF có mặt và hợp lệ khi gửi form.
- `LocaleMiddleware` - Middleware này dùng để xử lý dịch thuật nếu bạn muốn quốc tế hóa dự án.
- `MessageMiddleware` - Middleware này dùng cho “flash message”. Đây là các thông báo một lần mà bạn thường thấy sau khi gửi form, dù có thể dùng ở nhiều nơi khác. Chúng ta sẽ bàn thêm về message khi đến chủ đề sessions.
- `SecurityMiddleware` - Middleware này bao gồm nhiều kiểm tra để giúp trang của bạn an toàn. Chúng ta đã thấy ví dụ kiểm tra HTTPS ở phần trước. Middleware này cũng xử lý các vấn đề như XSS, HSTS và nhiều từ viết tắt khác (😛) sẽ gặp ở chủ đề bảo mật sau này.
- `SessionMiddleware` - Middleware này quản lý trạng thái session cho người dùng. Session rất quan trọng cho nhiều phần của Django như xác thực người dùng.

Như bạn thấy từ danh sách chưa đầy đủ này, middleware của Django có thể làm rất nhiều thứ để làm phong phú dự án của bạn theo nhiều cách khác nhau. Middleware là một khái niệm cực kỳ mạnh mẽ cho các dự án Django và là công cụ tuyệt vời để mở rộng khả năng xử lý request của ứng dụng.

Hãy nhớ rằng, middleware đi kèm với chi phí hiệu năng nên hãy tránh nhồi nhét quá nhiều chức năng vào chuỗi middleware. Miễn là bạn nhận thức được sự đánh đổi, middleware là một công cụ tuyệt vời cho bộ công cụ của bạn.

## Tóm Tắt

Trong bài viết này, chúng ta đã thấy hệ thống middleware của Django.

Chúng ta đã bàn về:

- Mô hình tư duy khi nghĩ về middleware
- Cách tự viết middleware
- Một số class middleware đi kèm Django

Lần tới chúng ta sẽ tìm hiểu về static files. Static files là tất cả hình ảnh, JavaScript, CSS, hoặc các loại file khác mà ứng dụng của bạn phục vụ cho người dùng mà không chỉnh sửa gì. Chúng ta cần hiểu:

- Cách cấu hình static files
- Cách làm việc với static files
- Cách xử lý static files khi triển khai trang web lên internet
