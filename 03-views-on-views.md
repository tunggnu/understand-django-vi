# Góc Nhìn Về Views

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), tôi đã trình bày về URLs và các công cụ đa dạng mà Django cung cấp để mô tả giao diện bên ngoài với internet cho dự án của bạn. Trong bài viết này, chúng ta sẽ xem xét khối xây dựng cốt lõi giúp các URL đó hoạt động: view của Django.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. Góc Nhìn Về Views
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

## View Là Gì?

View là một đoạn mã nhận một HTTP request và trả về một HTTP response. Views là nơi bạn sử dụng chức năng cốt lõi của Django: để phản hồi các yêu cầu gửi đến một ứng dụng trên internet.

Bạn có thể nhận thấy tôi hơi mơ hồ về “đoạn mã.” Đó là chủ ý. Lý do là vì view có nhiều hình thức khác nhau. Nếu nói view là _hàm_ thì cũng chỉ đúng một phần. Các chương sau sẽ đề cập đến cách chúng cũng có thể được triển khai bằng _lớp_.

Ngay cả khi tôi cố gọi view là _callable_ (đối tượng có thể gọi được), tôi vẫn chưa mô tả chính xác vì một số loại view sẽ được “cắm” vào ứng dụng Django theo những cách khác nhau. Ví dụ, một view dựa trên lớp sẽ _tạo ra_ một callable như chúng ta sẽ thấy ở phần sau.

Hãy bắt đầu với hàm vì tôi nghĩ đó là cách tiếp cận nhẹ nhàng nhất để làm quen với view.

## View Hàm

View hàm chính xác là một hàm. Hàm này nhận một instance của `HttpRequest` làm đầu vào và trả về một `HttpResponse` (hoặc một trong các lớp con của nó) làm đầu ra.

Ví dụ kinh điển “Hello World” sẽ trông như sau.

```python
# application/views.py
from django.http import HttpResponse

def hello_world(request):
    return HttpResponse('Hello World')
```

Thêm view `hello_world` vào cấu hình URL mà chúng ta đã học ở bài trước, bạn có thể truy cập trình duyệt tại URL đó và thấy dòng chữ “Hello World” trên trang trình duyệt của mình.

Có thể bạn không thấy điều đó thú vị, nhưng tôi thì có, và tôi nghĩ bạn cũng nên như vậy! Framework đã làm rất nhiều việc nặng nhọc cho chúng ta, và _việc của chúng ta_ chỉ là viết vài dòng Python. Khi được kết nối với một web server trên internet, lời chào của bạn có thể đến với bất kỳ ai có truy cập mạng. Điều đó thật đáng kinh ngạc và đáng để suy ngẫm.

Django đã làm hầu hết phần khó cho chúng ta. Yêu cầu HTTP thô được đóng gói gọn gàng vào lớp `HttpRequest`. View ví dụ của chúng ta không sử dụng thông tin đó, nhưng nó luôn sẵn sàng nếu ta cần. Tương tự, chúng ta cũng không dùng nhiều tính năng của `HttpResponse`. Tuy nhiên, nó đã làm tất cả công việc để đảm bảo nội dung xuất hiện trên trình duyệt người dùng và truyền tải thông điệp của chúng ta.

Để xem chúng ta có thể làm gì với view, hãy xem kỹ hơn về `HttpRequest` và `HttpResponse` để hiểu rõ hơn những gì đang diễn ra.

## HttpRequest

`HttpRequest` là một lớp Python. Các instance của lớp này đại diện cho một HTTP request. HTTP là giao thức truyền tải mà internet sử dụng để trao đổi thông tin. Một request có thể có nhiều định dạng khác nhau, nhưng một request tiêu chuẩn có thể trông như sau:

```text
POST /courses/0371addf-88f7-49e4-ac4d-3d50bb39c33a/edit/ HTTP/1.1
Host: 0.0.0.0:5000
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 155
Origin: http://0.0.0.0:5000
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Pragma: no-cache
Cache-Control: no-cache

name=Science
&monday=on
&tuesday=on
&wednesday=on
&thursday=on
&friday=on
```

Ví dụ này lấy từ một dự án phụ sử dụng dữ liệu trường học. Tôi đã cắt bớt một số dòng để nó vừa với màn hình hơn, và tôi cũng định dạng lại một chút cho dễ nhìn hơn.

Khi Django nhận được một request như vậy, nó sẽ phân tích dữ liệu và lưu trữ vào một instance của `HttpRequest`. Request này cung cấp quyền truy cập thuận tiện đến tất cả các phần của dữ liệu thô với các thuộc tính hữu ích cho những tham số thường dùng nhất. Khi xét ví dụ trên, request sẽ có:

- `method` - Thuộc tính này khớp với HTTP method là `POST` và có thể dùng để xử lý _loại_ request mà người dùng gửi lên.
- `content_type` - Thuộc tính này hướng dẫn Django cách xử lý dữ liệu trong request. Giá trị ví dụ sẽ là `application/x-www-form-urlencoded` để chỉ ra đây là dữ liệu form do người dùng gửi lên.
- `POST` - Với các request POST, Django sẽ xử lý dữ liệu form và lưu vào một cấu trúc giống dictionary. `request.POST['name']` sẽ là `Science` trong ví dụ của chúng ta.
- `GET` - Bất kỳ thứ gì thêm vào query string (tức là nội dung sau dấu `?` như `student=Matt` trong `/courses/?student=Matt`) cũng được lưu vào một thuộc tính giống dictionary.
- `headers` - Đây là nơi lưu trữ tất cả các HTTP header như `Host`, `Accept-Language` và các header khác. `headers` cũng giống dictionary và có thể truy cập như `request.headers['Host']`.

Còn nhiều thuộc tính khác có sẵn cho `HttpRequest`, nhưng danh sách trên đủ để bạn bắt đầu. Xem thêm tại [Request and response objects](https://docs.djangoproject.com/en/4.1/ref/request-response/) để biết các thuộc tính khác.

Tôi cũng nên lưu ý rằng các instance của `HttpRequest` là nơi phổ biến để gắn thêm dữ liệu bổ sung. Các request của Django sẽ đi qua nhiều thành phần trong framework. Điều này khiến các đối tượng này rất phù hợp để bổ sung các tính năng mà bạn có thể cần. Ví dụ, nếu bạn cần quản lý người dùng (chúng ta sẽ tìm hiểu ở bài sau), có đoạn mã sẽ gắn thuộc tính `request.user` để đại diện cho một người dùng trong hệ thống của bạn. Điều này _rất_ tiện lợi.

Bạn có thể coi các đối tượng `HttpRequest` là giao diện chung cho hầu hết các đầu vào mà mã của bạn sử dụng.

## HttpResponse

Giao diện chính còn lại mà view của bạn sẽ sử dụng trực tiếp hoặc gián tiếp là giao diện `HttpResponse`.

Nhiệm vụ của bạn với tư cách là người dùng Django là đảm bảo view trả về một loại `HttpResponse` nào đó. Một instance response sẽ bao gồm tất cả thông tin cần thiết để tạo ra một HTTP response hợp lệ cho trình duyệt của người dùng.

Một số thuộc tính phổ biến của `HttpResponse` gồm:

- `status_code` - Đây là mã trạng thái HTTP. Status code là tập hợp các số mà HTTP định nghĩa để thông báo cho client (ví dụ, trình duyệt) về việc request thành công hay thất bại. `200` là mã thành công thông thường. Bất kỳ số nào từ `400` trở lên sẽ chỉ ra lỗi, như `404` khi tài nguyên được yêu cầu không tìm thấy.
- `content` - Đây là nội dung bạn cung cấp cho người dùng. Response lưu trữ dữ liệu này dưới dạng bytes. Nếu bạn cung cấp dữ liệu dạng chuỗi Python, Django sẽ tự động mã hóa thành bytes cho bạn.

```text
>>> from django.http import HttpResponse
>>> response = HttpResponse('Hello World')
>>> response.content
b'Hello World'
```

Khi làm việc với view trong Django, bạn không phải lúc nào cũng dùng trực tiếp `HttpResponse`. `HttpResponse` có nhiều lớp con phục vụ các mục đích phổ biến. Hãy xem một số ví dụ:

- `HttpResponseRedirect` - Bạn có thể muốn chuyển hướng người dùng đến một trang khác. Có thể người dùng vừa mua hàng trên trang của bạn, và bạn muốn họ xem trang hóa đơn. Lớp con này rất phù hợp cho trường hợp đó.
- `HttpResponseNotFound` - Đây là lớp con dùng để tạo response `404 Not Found`. Django cung cấp một số hàm hỗ trợ để trả về loại này nên bạn có thể không dùng trực tiếp lớp con này, nhưng biết nó tồn tại cũng tốt.
- `HttpResponseForbidden` - Loại response này dùng khi bạn không muốn người dùng truy cập một phần nào đó của website (tức là HTTP status `403 Forbidden`).

Ngoài các lớp con, Django còn có các kỹ thuật khác để trả về instance `HttpResponse` mà không cần tự tạo. Hàm phổ biến nhất là `render`.

`render` là công cụ để làm việc với template. Template là chủ đề của bài tiếp theo, nhưng đây là một cái nhìn trước.

Bạn có thể viết một view cho một trang web và chèn rất nhiều HTML vào Python. HTML là ngôn ngữ đánh dấu của các trang internet mà chúng ta dùng để mô tả bố cục trang.

View này có thể trông như sau:

```python
from django.http import HttpResponse

def my_html_view(request):
    response_content = """
    <html>
    <head><title>Hello World!</title>
    <body>
        <h1>This is a demo.</h1>
    </body>
    </html>
    """
    return HttpResponse(response_content)
```

Dù cách này hoạt động, nó có nhiều hạn chế.

1. Đoạn HTML này không thể tái sử dụng bởi các view khác. Điều này không quan trọng với ví dụ nhỏ này, nhưng sẽ là vấn đề lớn khi bạn muốn tạo nhiều view dùng nhiều markup và cần chia sẻ giao diện chung.
2. Việc trộn lẫn Python và HTML sẽ rất lộn xộn. Cần bằng chứng? Hãy tìm hiểu lịch sử lập trình và xem về [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface). Nó không đẹp chút nào.
3. Làm sao để ghép các đoạn HTML lại với nhau? Không dễ chút nào.

Với template, chúng ta có thể tách biệt bố cục khỏi logic.

```python
# application/views.py
from django.shortcuts import render

def my_html_view(request):
    return render(
        request,
        "template.html",
        {}
    )
```

Và chúng ta sẽ có một file khác tên là `template.html` chứa:

```html
<html>
<head><title>Hello World!</title>
<body>
    <h1>This is a demo.</h1>
</body>
</html>
```

Điều quan trọng trong bài này không phải là template. Điều đáng chú ý là `render` sẽ tải nội dung từ `template.html`, lấy kết quả đầu ra và thêm kết quả đó vào một instance của `HttpResponse`.

Như vậy là chúng ta đã điểm qua `HttpRequest` và `HttpResponse`. Với các khối xây dựng này, chúng ta có thể xem các cách khác để tạo view trong Django cho dự án của bạn.

## View Dạng Lớp

Đến giờ chúng ta đã thấy mối quan hệ này với view:

```text
HttpRequest -> view -> HttpResponse
```

View không nhất thiết chỉ là hàm. Django cũng cung cấp công cụ để tạo view từ lớp. Các loại view này kế thừa từ lớp `View` của Django.

Khi bạn viết một view dựa trên lớp (thường gọi tắt là CBV), bạn thêm các phương thức instance khớp với các HTTP method. Hãy xem ví dụ:

```python
# application/views.py
from django.http import HttpResponse
from django.views.generic.base import View

class SampleView(View):
    def get(self, request, *args, **kwargs):
        return HttpResponse("Hello from a CBV!")
```

Phương thức `get` trên lớp này tương ứng với một HTTP request kiểu `GET`. `*args` và `**kwargs` là quy ước phổ biến trong Python để cho phép một phương thức hoặc hàm nhận bất kỳ số lượng đối số vị trí hoặc từ khóa nào. Chúng ta cần chúng để khớp với chữ ký phương thức mà Django yêu cầu cho CBV. Tương tự, bạn sẽ viết phương thức `post` để phản hồi HTTP request kiểu `POST` và các method khác. Khi đã định nghĩa view này, chúng ta có thể kết nối nó với URLconf:

```python
# project/urls.py
from django.urls import path

from application.views import SampleView

urlpatterns = [
    path("", SampleView.as_view()),
]
```

Lưu ý rằng chúng ta không truyền trực tiếp `SampleView` vào `path`. `path` mong đợi một đối tượng callable, nên chúng ta phải gọi `as_view`, một class method trả về một hàm sẽ gọi mã trong lớp của chúng ta.

Tại thời điểm này, nếu tôi là bạn, tôi sẽ không ấn tượng lắm. Tại sao phải thêm nhiều mã mẫu như vậy khi bạn chỉ cần viết một hàm là xong? Nếu chỉ có vậy thì tôi hoàn toàn đồng ý với bạn. View dựa trên lớp không thêm nhiều giá trị so với phiên bản hàm. Nếu có, CBV còn khó nhớ hơn, nên có thể gây nhầm lẫn.

Nơi mà view dựa trên lớp thực sự tỏa sáng là khi sử dụng các lớp khác ngoài lớp `View` ban đầu.

Django bao gồm rất nhiều view dựa trên lớp để sử dụng cho nhiều mục đích khác nhau. Chúng ta có thể khám phá một vài trong số đó với kiến thức hiện tại về framework.

## Các View Có Sẵn

Tôi sẽ không trình bày hết tất cả các view dựa trên lớp vì có rất nhiều. Ngoài ra, nếu bạn mới bắt đầu loạt bài này và chưa từng làm Django trước đây, thì vẫn sẽ còn nhiều lỗ hổng kiến thức (chúng ta sẽ cùng lấp đầy!), và một số view sẽ chưa có ý nghĩa.

### RedirectView

Dùng `RedirectView` để chuyển hướng người dùng đến nơi khác. Bạn _có thể_ tạo một view trả về instance `HttpResponseRedirect`, nhưng view dựa trên lớp này có thể làm điều đó cho bạn.

Thực tế, bạn có thể dùng `RedirectView` mà không cần kế thừa nó. Xem ví dụ sau:

```python
# project/urls.py
from django.urls import path
from django.views.generic.base import RedirectView

from application.views import NewView

urlpatterns = [
    path("old-view-path/",
         RedirectView.as_view(url="https://www.somewhereelse.com")),
    path("other-old-path/", RedirectView.as_view(pattern_name='new-view')),
    path("new-path/", NewView.as_view(), name='new-view'),
]
```

`RedirectView` có thể dùng `url` cho một URL đầy đủ, hoặc dùng `pattern_name` nếu bạn cần chuyển hướng đến một view đã chuyển sang nơi khác trong dự án.

`as_view` là thứ giúp chúng ta không cần kế thừa `RedirectView`. Các đối số truyền vào `as_view` sẽ ghi đè bất kỳ thuộc tính lớp nào. Hai cách dùng `RedirectView` sau là tương đương:

```python
# project/urls.py
from django.urls import path
from django.views.generic.base import RedirectView

from application.views import NewView

class SubclassedRedirectView(RedirectView):
    pattern_name = 'new-view'

urlpatterns = [
    path("old-path/", SubclassedRedirectView.as_view()),
    # RedirectView bên dưới hoạt động giống SubclassedRedirectView.
    path("old-path/", RedirectView.as_view(pattern_name='new-view')),
    path("new-path/", NewView.as_view(), name='new-view'),
]
```

### TemplateView

Ở đầu bài viết, chúng ta đã thấy cách tách bố cục trang web khỏi logic cần thiết để xây dựng trang bằng template.

Template được dùng quá phổ biến nên Django cung cấp một lớp biết cách tạo response chỉ với tên template.

Ví dụ như sau:

```python
# application/views.py
from django.views.generic.base import TemplateView

class HomeView(TemplateView):
    template_name = 'home.html'
```

Chúng ta sẽ xem chi tiết hơn về template view ở bài tiếp theo khi đi sâu vào template.

### Các Lớp View Khác

Các view dựa trên lớp khác của Django phục vụ nhiều mục đích khác nhau. Django có các view sẽ:

- Hiển thị và xử lý form HTML để người dùng nhập dữ liệu và gửi dữ liệu lên ứng dụng.
- Lấy dữ liệu từ cơ sở dữ liệu và hiển thị một bản ghi cho người dùng (ví dụ, một trang web để xem thông tin về một bộ phim cụ thể).
- Lấy dữ liệu từ cơ sở dữ liệu và hiển thị thông tin từ một tập hợp bản ghi cho người dùng (ví dụ, hiển thị dàn diễn viên của một bộ phim).
- Hiển thị dữ liệu theo các khoảng thời gian cụ thể như ngày, tuần, tháng.

Khi tiếp tục khám phá Django, chúng ta sẽ bàn về các view này khi chủ đề liên quan (như form) là nội dung chính của bài viết. Hiện tại, khi bạn phát triển view của riêng mình, hãy nhớ rằng Django có thể đã có một view dựa trên lớp hỗ trợ công việc của bạn.

## Các Decorator Và Mixin Hữu Ích Cho View

Trước khi kết thúc phần tìm hiểu về view, hãy nói về một số decorator và lớp mixin hữu ích.

Decorator là một tính năng của Python (và nhiều ngôn ngữ khác) cho phép bạn mở rộng một hàm với các khả năng bổ sung. Decorator có thể bọc một hàm view để cung cấp hành vi mới cho view đó. Decorator rất hữu ích khi bạn có chức năng chung muốn thêm vào nhiều view mà không phải copy-paste nhiều mã.

Lớp mixin phục vụ mục đích tương tự như decorator, nhưng sử dụng tính năng đa kế thừa của Python để “trộn” hành vi mới vào một view dựa trên lớp hiện có.

### Decorator Nên Biết

Khi làm việc với view dựa trên hàm, có một thách thức khi xử lý các HTTP method khác nhau. Mặc định, một view dựa trên hàm có thể nhận request từ _bất kỳ_ HTTP method nào. Một số view sẽ xử lý nhiều method như sau:

```python
# application/views.py
from django.http import (
    HttpResponse,
    HttpResponseNotAllowed,
)

def multi_method_view(request):
    if request.method == 'GET':
        return HttpResponse('Method was a GET.')
    elif request.method == 'POST':
        return HttpResponse('Method was a POST.')
    return HttpResponseNotAllowed()
```

View này dùng thuộc tính `method` của instance `request` để kiểm tra HTTP method của request. Nếu bạn chỉ muốn view phản hồi một HTTP method duy nhất thì sao? Giả sử bạn chỉ muốn phản hồi POST. Chúng ta có thể viết:

```python
# application/views.py
from django.http import (
    HttpResponse,
    HttpResponseNotAllowed,
)

def guard_clause_view(request):
    if request.method != 'POST':
        return HttpResponseNotAllowed()

    return HttpResponse('Method was a POST.')

# HOẶC

def if_clause_view(request):
    if request.method == 'POST':
        return HttpResponse('Method was a POST.')
    else:
        return HttpResponseNotAllowed()
```

Cả hai cách đều hoạt động, nhưng mã sẽ lộn xộn hơn vì phải thụt lề thêm. Thay vào đó, chúng ta có thể dùng decorator `require_POST` và để Django kiểm tra method giúp.

```python
# application/views.py
from django.http import HttpResponse
from django.view.decorators.http import require_POST

@require_POST
def the_view(request):
    return HttpResponse('Method was a POST.')
```

Phiên bản này nêu rõ mong đợi ngay từ đầu với decorator và khai báo hợp đồng mà view sẽ tuân theo. Nếu người dùng thử một method khác (như `GET`), Django sẽ trả về HTTP status code `405`, là mã lỗi “method not allowed.”

Một decorator phổ biến khác bạn có thể gặp là `login_required`. Khi chúng ta bàn về quản lý người dùng, bạn sẽ thấy chúng ta có thể bảo vệ một view bằng cách thêm decorator này.

```python
# application/views.py
from django.contrib.auth.decorators import login_required
from django.http import HttpResponse

@login_required
def the_view(request):
    return HttpResponse('This view is only viewable to authenticated users.')
```

Bất kỳ người dùng chưa xác thực nào sẽ tự động bị chuyển hướng đến trang đăng nhập của ứng dụng web.

Một ví dụ cuối cùng về decorator tích hợp hữu ích là `user_passes_test`. Đây là một decorator khác dùng với hệ thống quản lý người dùng cho phép chúng ta kiểm soát _người dùng nào_ được phép truy cập view. Ví dụ, chúng ta có thể tạo một view chỉ cho phép người dùng cấp nhân viên truy cập.

```python
# application/views.py
from django.contrib.auth.decorators import user_passes_test
from django.http import HttpResponse

@user_passes_test(lambda user: user.is_staff)
def the_view(request):
    return HttpResponse('Only visible to staff users.')
```

Decorator này nhận một callable chấp nhận một đối số là đối tượng user. View chỉ truy cập được nếu giá trị trả về của callable kiểm tra là `True`.

Điều tôi muốn chỉ ra với các ví dụ này là chỉ với một decorator, bạn có thể nhanh chóng bổ sung tính năng mới cho view. Và, nhờ cách decorator bọc hàm, bạn có thể “xếp chồng” chúng lại với nhau.

```python
# application/views.py
from django.contrib.auth.decorators import user_passes_test
from django.http import HttpResponse
from django.view.decorators.http import require_POST

@require_POST
@user_passes_test(lambda user: user.is_staff)
def the_view(request):
    return HttpResponse('Only staff users may POST to this view.')
```

### Mixin Nên Biết

Lớp mixin đối với view dựa trên lớp cũng giống như decorator đối với view dựa trên hàm. Điều này không _hoàn toàn_ đúng vì view dựa trên lớp cũng có thể dùng decorator, nhưng nó giúp bạn hình dung vị trí của mixin.

Giống như các decorator `login_required` và `user_passes_test`, chúng ta có các mixin tương đương là `LoginRequiredMixin` và `UserPassesTestMixin`. Có thể bạn có một số template view chỉ nên cho người dùng đã xác thực hoặc người dùng cấp nhân viên truy cập. Các view đó có thể trông như sau:

```python
# application/views.py
from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin
from django.views.generic.base import TemplateView

class HomeView(LoginRequiredMixin, TemplateView):
    template_name = 'home.html'

class StaffProtectedView(UserPassesTestMixin, TemplateView):
    template_name = 'staff_eyes_only.html'

    def test_func(self):
        return self.request.user.is_staff
```

Bạn có thể thấy các view này tương tự như các decorator tương ứng nhưng với cách dùng hơi khác.

Một điều cần lưu ý với mixin là vị trí của chúng. Do cách Python xử lý đa kế thừa, bạn nên đặt các lớp mixin bên trái trong danh sách các lớp cha kế thừa. Điều này đảm bảo Python sẽ hoạt động đúng với các lớp này. Lý do chính xác là do quy tắc MRO (method resolution order) của Python khi dùng đa kế thừa. MRO nằm ngoài phạm vi bài viết này, nhưng bạn có thể tìm hiểu thêm nếu muốn.

Còn rất nhiều lớp mixin khác. Hầu hết các view dựa trên lớp tích hợp của Django đều được xây dựng bằng cách kết hợp nhiều lớp mixin khác nhau. Nếu bạn muốn xem chúng được xây dựng thế nào, hãy xem [Classy Class-Based Views](https://ccbv.co.uk/), một trang web hiển thị các CBV tích hợp và các mixin, thuộc tính có sẵn cho các lớp đó.

## Tóm Tắt

Như vậy là chúng ta đã kết thúc phần cơ bản về view. Chúng ta đã xem qua:

- Hàm view
- `HttpRequest` và `HttpResponse`
- View dạng lớp
- Một số view hỗ trợ tích hợp sẵn
- Decorator và mixin giúp tăng sức mạnh cho view.

Trong bài tiếp theo, chúng ta sẽ xem cách view có thể kết hợp bố cục tĩnh với dữ liệu động mà chúng ta cung cấp thông qua template. Template là công cụ chủ lực cho giao diện người dùng dựa trên Django của bạn. Chúng ta sẽ tìm hiểu:

- Cách thiết lập template cho trang web
- Cách gọi template từ view
- Cách sử dụng dữ liệu
- Cách xử lý logic
- Các hàm tích hợp sẵn cho template
- Tùy biến template với phần mở rộng mã của riêng bạn
