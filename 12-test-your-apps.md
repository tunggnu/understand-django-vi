# Kiểm Thử Ứng Dụng Của Bạn

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã thấy cách các static files như CSS, JavaScript và hình ảnh có thể được tích hợp vào trang web của bạn. Bây giờ, chúng ta sẽ tập trung vào cách xác minh rằng website của bạn hoạt động đúng và tiếp tục hoạt động đúng bằng cách viết các kiểm thử tự động để kiểm tra các trang và logic mã nguồn của bạn.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/)
8. [Giải Phẫu Một Ứng Dụng](https://www.mattlayman.com/understand-django/anatomy-of-an-application/)
9. [Xác Thực Người Dùng](https://www.mattlayman.com/understand-django/user-authentication/)
10. [Middleware Bạn Đi Đâu?](https://www.mattlayman.com/understand-django/middleware-do-you-go/)
11. [Phục Vụ Static Files](https://www.mattlayman.com/understand-django/serving-static-files/)
12. Kiểm Thử Ứng Dụng Của Bạn
13. [Triển Khai Trang Web](https://www.mattlayman.com/understand-django/deploy-site-live/)
14. [Dữ Liệu Theo Từng Người Dùng Với Sessions](https://www.mattlayman.com/understand-django/sessions/)
15. [Hiểu Về Settings](https://www.mattlayman.com/understand-django/settings/)
16. [Quản Lý File Người Dùng](https://www.mattlayman.com/understand-django/media-files/)
17. [Lệnh Cho Ứng Dụng](https://www.mattlayman.com/understand-django/command-apps/)
18. [Tăng Tốc Với Django](https://www.mattlayman.com/understand-django/go-fast/)
19. [Bảo Mật Và Django](https://www.mattlayman.com/understand-django/secure-apps/)
20. [Mẹo Và Kỹ Thuật Gỡ Lỗi](https://www.mattlayman.com/understand-django/debugging-tips-techniques/)

## Tại Sao Cần Viết Kiểm Thử

Tôi sẽ giả định rằng nếu bạn đang đọc bài này, thì bạn đã có một dự án Django hoặc đang cân nhắc làm việc với Django để xây dựng một dự án. Nếu đúng như vậy, hãy nghĩ về dự án của bạn và cách bạn sẽ đảm bảo nó hoạt động đúng.

Khi bạn bắt đầu với một dự án, dù là cho một hướng dẫn hay cho một thứ gì đó thực tế mà bạn dự định phát triển, trang web ban đầu có rất ít chức năng. Để kiểm tra xem trang có hoạt động không, bạn có thể khởi động web server cục bộ, mở trình duyệt, truy cập URL `localhost` và xác nhận rằng trang hoạt động. Việc đó mất bao lâu? 5 giây? 15 giây? 30 giây?

Khi mới bắt đầu, kiểm tra thủ công trang web của bạn là ổn. Tuy nhiên, điều gì sẽ xảy ra khi bạn tạo thêm nhiều trang? Làm sao bạn tiếp tục xác nhận rằng tất cả các trang đều hoạt động? Bạn có thể mở trang cục bộ và bắt đầu click quanh, nhưng thời gian xác nhận mọi thứ hoạt động sẽ tăng lên. Có thể nỗ lực xác minh của bạn mất 3 phút, 5 phút, hoặc thậm chí nhiều hơn. Nếu bạn không cẩn thận, sản phẩm của bạn có thể bắt đầu giống như con quái vật nhiều đầu Hydra trong thần thoại, và thứ từng là một dự án vui vẻ để làm sẽ trở thành một công việc nhàm chán kiểm tra từng trang.

Bạn không thể loại bỏ thực tế rằng dự án càng lớn thì càng có nhiều thứ cần kiểm tra. Điều bạn _có thể_ làm là thay đổi cách tiếp cận. Bạn có thể chuyển việc kiểm tra trang từ thủ công (mất 15 giây để xác nhận một trang) sang việc để máy tính làm trong _mili giây_.

Đây là lúc kiểm thử tự động xuất hiện. Kiểm thử tự động cho phép máy tính làm điều mà máy tính giỏi nhất: thực hiện các tác vụ lặp đi lặp lại, nhất quán và nhanh chóng. Khi chúng ta viết kiểm thử, mục tiêu là xác nhận một logic hoặc hành vi nào đó một cách xác định.

Hãy xem một kiểm thử cho hàm `add` giả định hoạt động như toán tử `+`. Điều này sẽ giúp bạn hình dung kiểm thử tự động là gì nếu bạn chưa từng gặp kiểm thử trước đây.

```python
def test_does_it_add():
    assert add(40, 2) == 42
```

Kiểm thử này hoạt động bằng cách chạy mã và so sánh kết quả với giá trị mà chúng ta mong đợi. Kiểm thử _khẳng định_ rằng biểu thức so sánh là đúng. Nếu so sánh sai, assertion sẽ raise một exception và kiểm thử thất bại.

Kiểm thử tự động này sẽ mất gần như không đáng kể thời gian nếu so với việc chạy hàm trong Python REPL để kiểm tra kết quả thủ công.

Nhìn một ví dụ đơn giản về hàm `add` không giúp bạn nhiều về cách kiểm thử dự án Django. Tiếp theo, chúng ta sẽ xem một số loại kiểm thử cho Django. Nếu bạn thêm các loại kiểm thử này vào dự án, bạn sẽ tự tin hơn khi thay đổi website mà không lo phá vỡ mọi thứ.

## Các Loại Kiểm Thử Django Hữu Ích

Khi chúng ta tìm hiểu giải phẫu một ứng dụng Django, tôi đã nói rằng tôi _luôn_ xóa file `tests.py` đi kèm lệnh `startapp`. Lý do là vì có nhiều loại kiểm thử khác nhau, và tôi muốn các loại đó nằm ở các file riêng biệt. Ứng dụng của tôi sẽ có các file riêng trong một package `tests` bên trong app thay vì một module `tests.py`.

Package `tests` của tôi thường sẽ phản chiếu cấu trúc của chính ứng dụng. Chương trình thực thi kiểm thử, gọi là “test runner”, thường mong đợi tìm kiểm thử trong các file bắt đầu bằng `test_`. Package này thường bao gồm:

- `test_forms.py`
- `test_models.py`
- `test_views.py`
- v.v.

Cấu trúc này gợi ý về các loại kiểm thử bạn sẽ viết cho ứng dụng, nhưng tôi sẽ nói cụ thể hơn ở phần sau. Nhìn chung, khi viết kiểm thử tự động, có một chiều quan trọng cần cân nhắc: kiểm thử của tôi nên chạy bao nhiêu mã nguồn của ứng dụng?

Câu trả lời cho câu hỏi đó ảnh hưởng đến hành vi của kiểm thử. Nếu chúng ta viết một kiểm thử chạy rất nhiều mã, thì chúng ta được lợi là kiểm tra được nhiều phần của hệ thống cùng lúc; tuy nhiên, cũng có một số nhược điểm:

- Chạy nhiều mã nghĩa là có nhiều thứ có thể xảy ra và khả năng kiểm thử bị hỏng theo những cách không ngờ tới cao hơn. Một kiểm thử thường xuyên bị hỏng bất ngờ được gọi là kiểm thử “dễ vỡ” (brittle).
- Chạy nhiều mã nghĩa là có nhiều mã phải thực thi. Điều này nghe hiển nhiên, nhưng ý nghĩa là kiểm thử càng nhiều mã thì càng chạy lâu hơn. Các kiểm thử tự động lớn vẫn có thể nhanh hơn nhiều so với kiểm thử thủ công, nên thời gian chạy chỉ là tương đối.

Khi chúng ta có các kiểm thử chạy nhiều phần của ứng dụng _kết hợp_ với nhau, ta gọi đó là _integration test_ (kiểm thử tích hợp). Kiểm thử tích hợp rất tốt để phát hiện các vấn đề liên quan đến _kết nối giữa các đoạn mã_. Ví dụ, nếu bạn gọi một phương thức và truyền sai tham số, kiểm thử tích hợp có thể phát hiện ra vấn đề đó.

Ở đầu kia của phổ kiểm thử là các kiểm thử chạy rất ít mã. Kiểm thử `add` ở trên là một ví dụ. Các kiểm thử này kiểm tra từng đơn vị mã riêng lẻ (ví dụ một model Django). Vì lý do đó, chúng ta gọi chúng là _unit test_ (kiểm thử đơn vị). Unit test rất tốt để _kiểm tra một đoạn mã riêng biệt_ nhằm xác nhận hành vi của nó.

Unit test cũng có nhược điểm. Các kiểm thử này chạy mà không có nhiều ngữ cảnh từ phần còn lại của ứng dụng. Điều này giúp bạn xác nhận hành vi của từng phần, nhưng có thể không phải là hành vi mà toàn bộ ứng dụng yêu cầu.

Bài học ở đây là cả hai loại kiểm thử đều tốt, nhưng đều có đánh đổi. Hãy cẩn thận với ai đó nói rằng bạn chỉ nên viết một loại kiểm thử này hoặc loại kia.

> _Một bộ kiểm thử tự động tốt sẽ bao gồm cả kiểm thử **đơn vị** và **tích hợp** để kiểm tra hành vi của từng phần riêng lẻ và sự kết nối giữa các phần._

Chúng ta cần cân nhắc thêm một khía cạnh nữa: đâu là “lượng mã đúng” cho một unit test? _Không có câu trả lời tuyệt đối ở đây._ Thực tế, chủ đề này được tranh luận rất nhiều giữa các tester.

Một số người cho rằng unit test chỉ nên chạy mã của đơn vị đó. Nếu bạn có một class chỉ thực hiện logic thuần túy và không cần mã khác, thì bạn đang ở trường hợp lý tưởng. Nhưng nếu bạn kiểm thử một phương thức bạn thêm vào model Django và nó cần tương tác với cơ sở dữ liệu thì sao? Ngay cả khi bạn chỉ kiểm tra phương thức model riêng lẻ, một người theo chủ nghĩa unit test sẽ cho rằng kiểm thử đó thực ra là integration test nếu nó tương tác với cơ sở dữ liệu.

**Tôi thường thấy kiểu tranh luận này không hiệu quả.** Theo kinh nghiệm của tôi, tranh luận triết lý về unit test là gì thường không giúp ích cho việc kiểm thử web app để xác nhận tính đúng đắn. Tôi đề cập đến điều này vì nếu bạn muốn tìm hiểu sâu hơn về kiểm thử sau bài này, tôi khuyên bạn tránh bị cuốn vào bẫy định nghĩa này.

Đây là định nghĩa thực tế của tôi về unit test và integration test trong Django. Các định nghĩa này không hoàn hảo (như _mọi_ định nghĩa), nhưng chúng sẽ giúp bạn hình dung chủ đề trong bài viết này.

- **Unit test** - Kiểm thử kiểm tra từng đơn vị riêng lẻ trong dự án Django như một phương thức model hoặc một form.
- **Integration test** - Kiểm thử kiểm tra một nhóm các đơn vị và sự tương tác giữa chúng, ví dụ kiểm tra một view render ra kết quả mong đợi.

Giờ chúng ta đã có khái niệm cốt lõi về kiểm thử, hãy đi vào chi tiết.

### Unit Test

Khi đi vào ví dụ, tôi cần giới thiệu một vài công cụ mà tôi dùng cho tất cả dự án Django của mình. Tôi sẽ mô tả kỹ hơn về các công cụ này ở phần sau, nhưng cần giới thiệu ngắn gọn ở đây để ví dụ không bị khó hiểu. Hai package “phải có” của tôi là:

- `pytest-django`
- `factory-boy`

`pytest-django` là một package giúp chạy kiểm thử Django thông qua chương trình `pytest`. pytest là một công cụ kiểm thử Python cực kỳ phổ biến với hệ sinh thái extension rất lớn. Thực tế, `pytest-django` là một trong những extension đó.

Lý do lớn nhất tôi dùng `pytest-django` là nó cho phép tôi dùng từ khóa `assert` trong tất cả kiểm thử. Trong module `unittest` của thư viện chuẩn Python và, kéo theo đó, các công cụ kiểm thử tích hợp của Django (subclass từ các class của `unittest`), việc kiểm tra giá trị cần các phương thức như `assertEqual` và `assertTrue`. Như bạn sẽ thấy, chỉ dùng từ khóa `assert` là cách viết kiểm thử rất tự nhiên.

Công cụ quan trọng còn lại là `factory-boy`. Trên PyPI gọi là `factory-boy`, nhưng tài liệu lại dùng `factory_boy`, nên từ đây tôi sẽ dùng tên đó. `factory_boy` là công cụ để xây dựng dữ liệu kiểm thử cho cơ sở dữ liệu. Thư viện này tích hợp tuyệt vời với Django và giúp chúng ta sinh dữ liệu model rất dễ dàng.

Tôi sẽ tập trung vào hai package này ở phần sau để nói kỹ hơn về tính năng, nhưng bạn sẽ thấy chúng được dùng ngay trong các ví dụ.

#### Kiểm Thử Model

Trong các dự án Django, chúng ta dùng model để lưu dữ liệu về ứng dụng, nên rất tự nhiên khi thêm các phương thức vào model để thao tác với dữ liệu. Làm sao để viết kiểm thử xác nhận phương thức đó hoạt động như mong đợi?

Tôi sẽ đưa cho bạn một khung tư duy cho _mọi_ kiểm thử, không chỉ unit test. Khung này sẽ giúp bạn suy nghĩ về mọi kiểm thử khi đọc và viết mã. Đó là _mẫu AAA_. AAA là viết tắt của:

- **Arrange** - Phần này của kiểm thử sẽ thiết lập dữ liệu và các điều kiện cần thiết cho kiểm thử.
- **Act** - Giai đoạn này là khi kiểm thử chạy đoạn mã ứng dụng bạn muốn kiểm tra.
- **Assert** - Phần cuối cùng kiểm tra rằng hành động của bạn đúng như mong đợi.

Với kiểm thử model, nó sẽ trông như sau:

```python
# application/tests/test_models.py

from application.models import Order
from application.tests.factories import OrderFactory

class TestOrder:
    def test_shipped(self):
        """After shipping an order, the status is shipped."""
        order = OrderFactory(
            status=Order.Status.PENDING
        )

        order.ship()

        order.refresh_from_db()
        assert order.status == Order.Status.SHIPPED
```

Chúng ta có thể hình dung một dự án có hệ thống thương mại điện tử. Một phần lớn của việc xử lý đơn hàng là theo dõi trạng thái. Chúng ta có thể tự đặt trường trạng thái ở nhiều nơi trong ứng dụng, nhưng thay đổi trạng thái trong một phương thức cho phép ta làm thêm việc khác. Ví dụ, có thể phương thức `ship` cũng gửi email.

Trong kiểm thử trên, chúng ta kiểm tra chuyển trạng thái từ `PENDING` sang `SHIPPED`. Kiểm thử gọi phương thức `ship`, sau đó làm mới instance model từ cơ sở dữ liệu để đảm bảo trạng thái `SHIPPED` đã được lưu.

Điều gì tốt ở kiểm thử này?

Kiểm thử có docstring. Tin tôi đi, bạn _sẽ_ thấy lợi ích của docstring cho kiểm thử. Có một cám dỗ lớn là chỉ để lại tên hàm như `test_shipped`, nhưng sau này bạn có thể không còn đủ ngữ cảnh.

Nhiều lập trình viên chọn tên kiểm thử dài thay thế. Tôi không phản đối tên kiểm thử dài, nhưng docstring cũng rất hữu ích. Whitespace là _tốt_ và, theo tôi, dễ đọc “The widget updates the game state when pushed.” hơn là `test_widget_updates_game_state_when_pushed`.

Kiểm thử kiểm tra một hành động duy nhất. Một kiểm thử kiểm tra một hành động duy nhất sẽ dễ hiểu. Không có câu hỏi về sự tương tác với phần khác. Cũng không có câu hỏi về việc thực sự đang kiểm tra cái gì. Sự đơn giản của việc kiểm tra một hành động giúp mỗi unit test kể một câu chuyện riêng.

Ngược lại, bạn có thể gặp các kiểm thử trong dự án làm rất nhiều bước chuẩn bị, rồi xen kẽ giữa act và assert trong một kiểm thử. Các kiểm thử này dễ vỡ (tức là dễ bị hỏng và fail) và khó hiểu khi có lỗi.

Các đặc điểm này áp dụng cho nhiều loại kiểm thử khác nhau. Đó là cái hay của một mô hình tư duy kiểm thử vững chắc. Khi bạn thấy kiểm thử:

1. Thiết lập đầu vào,
2. Thực hiện hành động,
3. Kiểm tra đầu ra,

thì kiểm thử tự động sẽ bớt đáng sợ và hữu ích hơn nhiều. Giờ hãy xem mẫu này áp dụng cho form như thế nào.

#### Kiểm Thử Form

Khi viết kiểm thử, chúng ta thường muốn viết kiểm thử “happy path”. Đây là loại kiểm thử khi mọi thứ hoạt động đúng như mong đợi. Đây là một kiểm thử happy path cho form.

```python
# application/tests/test_forms.py

from application.forms import SupportForm
from application.models import SupportRequest

class TestSupportForm:
    def test_request_created(self):
        """A submission to the support form creates a support request."""
        email = "hello@notreal.com"
        data = {
            "email": email,
            "message": "I'm having trouble with your product."
        }
        form = SupportForm(data=data)
        form.is_valid()

        form.save()

        assert SupportRequest.objects.filter(
            email=email
        ).count() == 1
```

Với kiểm thử này, chúng ta mô phỏng một request POST. Kiểm thử:

- Tạo dữ liệu POST là `data`
- Tạo một form đã gán dữ liệu (tức là truyền `data=data` vào constructor)
- Kiểm tra tính hợp lệ của form
- Lưu form
- Khẳng định rằng đã tạo một bản ghi mới

Lưu ý là tôi hơi “bẻ cong” quy tắc AAA một chút cho kiểm thử này. Theo thông lệ của Django với form, form phải hợp lệ trước khi gọi phương thức `save`. Nếu không làm vậy, `cleaned_data` sẽ không được populate đúng và hầu hết các phương thức `save` đều phụ thuộc vào `cleaned_data`. Dù `is_valid` là một hành động, tôi xem nó là bước chuẩn bị cho kiểm thử form.

Khi làm việc với form, điều chúng ta quan tâm là làm sạch dữ liệu để đảm bảo dữ liệu rác không vào cơ sở dữ liệu. Hãy viết một kiểm thử cho form không hợp lệ.

```python
# application/tests/test_forms.py

from application.forms import SupportForm
from application.models import SupportRequest

class TestSupportForm:
    # ... def test_request_created ...

    def test_bad_email(self):
        """An malformed email address is invalid."""
        data = {
            "email": "bogus",
            "message": "Whatever"
        }
        form = SupportForm(data=data)

        is_valid = form.is_valid()

        assert not is_valid
        assert 'email' in form.errors
```

Kiểm thử này cho thấy cách kiểm tra một form không hợp lệ. Các bước chính là:

- Thiết lập dữ liệu form sai
- Kiểm tra tính hợp lệ với `is_valid`
- Kiểm tra trạng thái đầu ra trong `form.errors`

Kiểm thử này cho thấy cách kiểm tra form không hợp lệ, nhưng tôi ít khi viết kiểm thử này trong dự án thực tế. Tại sao? Vì kiểm thử này kiểm tra chức năng của `EmailField` của Django, vốn đã có logic kiểm tra email hợp lệ.

Nói chung, tôi không nghĩ kiểm thử các tính năng của framework là hữu ích. Một dự án mã nguồn mở tốt như Django đã kiểm thử các tính năng đó cho bạn. Khi viết kiểm thử form, bạn nên kiểm tra các phương thức `clean_*` và `clean` tùy biến cũng như bất kỳ phương thức `save` tùy biến nào bạn thêm vào.

Mẫu kiểm thử cho cả happy path và trường hợp lỗi là thứ tôi dùng cho hầu hết kiểm thử form Django. Hãy chuyển sang kiểm thử tích hợp để xem kiểm thử nhiều mã hơn trông như thế nào.

### Kiểm Thử Tích Hợp

Theo tôi, một kiểm thử tích hợp tốt sẽ không khác nhiều so với một unit test tốt. Kiểm thử tích hợp vẫn có thể tuân theo mẫu AAA như các kiểm thử tự động khác. Điều thay đổi là công cụ bạn dùng và các khẳng định bạn sẽ viết.

Định nghĩa của tôi về kiểm thử tích hợp trong Django là kiểm thử dùng test `Client` của Django. Trong các bài trước, tôi chỉ nhắc đến client một cách lướt qua. Trong ngữ cảnh ứng dụng web, client là bất cứ thứ gì tiêu thụ đầu ra của web app để hiển thị cho người dùng.

Client rõ ràng nhất cho web app là trình duyệt, nhưng còn rất nhiều loại client khác. Một số ví dụ có thể dùng đầu ra từ web app:

- Ứng dụng di động native
- Giao diện dòng lệnh
- Thư viện lập trình như package `requests` của Python có thể xử lý HTTP request và response

Test `Client` của Django cũng giống các client khác ở chỗ nó có thể tương tác với dự án Django để nhận dữ liệu từ các request mà nó tạo ra. Điểm hay của test client là đầu ra được trả về theo cách tiện lợi để chúng ta kiểm tra. Client trả về trực tiếp object `HttpResponse`!

Với bối cảnh đó, đây là một kiểm thử tích hợp để chúng ta thảo luận.

```python
# application/tests/test_views.py

from django.test import Client
from django.urls import reverse

from application.tests.factories import UserFactory

class TestProfileView:
    def test_shows_name(self):
        """The profile view shows the user's name."""
        client = Client()
        user = UserFactory()

        response = client.get(
            reverse("profile")
        )

        assert response.status_code == 200
        assert user.first_name in response.content.decode()
```

Kiểm thử này đang làm gì? Và kiểm thử này _không_ làm gì?

Bằng cách dùng test client của Django, kiểm thử này chạy rất nhiều mã Django. Nó đi qua:

- Định tuyến URL
- Thực thi view (có thể sẽ truy vấn cơ sở dữ liệu)
- Render template

Đó là rất nhiều mã được chạy trong một kiểm thử! Mục tiêu của kiểm thử là kiểm tra tất cả các phần chính có kết nối với nhau.

Giờ hãy xem kiểm thử _không_ làm gì. Dù kiểm thử chạy rất nhiều mã, không có quá nhiều câu lệnh `assert`. Nói cách khác, mục tiêu của kiểm thử tích hợp không phải là kiểm tra từng chi tiết nhỏ có thể xảy ra trong toàn bộ luồng. Hy vọng rằng chúng ta đã có unit test kiểm tra các phần nhỏ đó.

Khi tôi viết kiểm thử tích hợp, tôi chủ yếu muốn trả lời câu hỏi: **hệ thống có hoạt động trơn tru mà không bị vỡ không?**

Giờ chúng ta đã bàn về unit test và integration test, vậy có những công cụ nào giúp kiểm thử dễ dàng hơn?

## Công Cụ Hỗ Trợ

Khi kiểm thử ứng dụng, bạn có rất nhiều package hỗ trợ đến mức có thể bị choáng ngợp. Nếu bạn kiểm thử lần đầu, bạn có thể gặp khó khăn khi áp dụng mẫu AAA và biết nên kiểm thử gì. Chúng ta muốn giảm thiểu những thứ bạn phải biết thêm.

Chúng ta sẽ xem lại hai công cụ tôi đã liệt kê ở trên, `pytest-django` và `factory_boy`, để bạn bắt đầu. Hãy coi đây là bộ sinh tồn kiểm thử Django của bạn. Khi kỹ năng kiểm thử phát triển, bạn có thể thêm nhiều công cụ khác, nhưng hai công cụ này là khởi đầu tuyệt vời.

### `pytest-django`

[pytest](https://docs.pytest.org/en/stable/) là một “test runner”. Công cụ này dùng để chạy kiểm thử tự động. Nếu bạn đọc [Writing and running tests](https://docs.djangoproject.com/en/4.1/topics/testing/overview/) trong tài liệu Django, bạn sẽ thấy Django _cũng_ có một test runner với `./manage.py test`. Vậy tại sao tôi lại khuyên bạn dùng `pytest`?

Tôi sẽ mạnh dạn khẳng định: **pytest tốt hơn**. (Tôi vừa meta đấy! 😆)

Tôi thích nhiều thứ ở test runner tích hợp của Django, nhưng tôi luôn quay lại với pytest vì một lý do chính: tôi có thể dùng `assert` trong kiểm thử. Như bạn đã thấy trong các ví dụ, từ khóa `assert` giúp mã kiểm thử dễ đọc. Chúng ta có thể dùng tất cả phép so sánh thông thường của Python (ví dụ `==`, `!=`, `in`) để kiểm tra đầu ra của kiểm thử.

Test runner của Django xây dựng dựa trên công cụ kiểm thử có sẵn trong Python là module `unittest`. Với các công cụ đó, lập trình viên phải tạo class kiểm thử kế thừa từ `unittest.TestCase`. Nhược điểm của class `TestCase` là bạn phải dùng một loạt phương thức `assert*` để kiểm tra mã.

Danh sách các phương thức `assert*` có trong tài liệu [unittest](https://docs.python.org/3/library/unittest.html#assert-methods). Bạn có thể thành công với các phương thức này, nhưng tôi nghĩ nó đòi hỏi phải nhớ một API với rất nhiều phương thức. Hãy cân nhắc điều này. Bạn thích:

1. Dùng `assert`? HAY
2. Dùng `assertEqual`, `assertNotEqual`, `assertTrue`, `assertFalse`, `assertIs`, `assertIsNot`, `assertIsNone`, `assertIsNotNone`, `assertIn`, `assertNotIn`, `assertIsInstance`, và `assertNotIsInstance`?

Dùng `assert` từ pytest nghĩa là bạn có tất cả lợi ích của các phương thức `assert*`, nhưng chỉ cần nhớ một từ khóa duy nhất. Nếu vậy vẫn chưa đủ, hãy so sánh độ dễ đọc:

```python
self.assertEqual(my_value, 42)
assert my_value == 42

self.assertNotEqual(my_value, 42)
assert my_value != 42

self.assertIsNotNone(my_value)
assert my_value is not None

self.assertTrue(my_value)
assert my_value
```

Vì lý do tương tự mà lập trình viên Python thích dùng phương thức `property` thay vì getter và setter (ví dụ `obj.value = 42` thay vì `obj.set_value(42)`), tôi nghĩ cú pháp kiểu `assert` đơn giản hơn nhiều để đọc.

Ngoài việc xử lý tuyệt vời từ khóa `assert`, [pytest-django](https://pytest-django.readthedocs.io/en/latest/) còn có nhiều tính năng khác mà bạn có thể thấy hữu ích khi viết kiểm thử tự động.

### `factory_boy`

Package kiểm thử khác mà tôi nghĩ mọi lập trình viên nên dùng trong dự án Django là `factory_boy`.

> `factory_boy` _giúp bạn xây dựng dữ liệu model cho kiểm thử._

Khuyến nghị khi viết kiểm thử tự động là dùng một cơ sở dữ liệu kiểm thử trống. Thực tế, mẫu phổ biến mà Django cung cấp là dùng cơ sở dữ liệu trống cho mỗi kiểm thử. Có một database trắng giúp mỗi kiểm thử độc lập và dễ kiểm tra trạng thái database. Vì database kiểm thử trống, bạn sẽ cần một chiến lược để tạo dữ liệu kiểm thử phù hợp.

Khi bạn phát triển dự án Django, bạn sẽ có nhiều model mô tả domain mà website của bạn giải quyết. Sinh dữ liệu model cho kiểm thử là một khả năng cực kỳ giá trị.

Bạn _có thể_ dùng phương thức `create` của model manager để tạo bản ghi database cho kiểm thử, nhưng bạn sẽ gặp giới hạn rất nhanh.

Thách thức lớn nhất khi dùng `create` là các ràng buộc database như foreign key. Nếu bạn muốn tạo một bản ghi cần nhiều foreign key không được null thì sao? Bạn chỉ còn cách tạo các bản ghi foreign key đó.

Chúng ta có thể hình dung một ứng dụng hiển thị thông tin về các bộ phim. Model `Movie` có thể có nhiều quan hệ foreign key như đạo diễn, nhà sản xuất, hãng phim, v.v. Tôi sẽ sử dụng một vài quan hệ trong ví dụ, nhưng hãy tưởng tượng điều gì sẽ xảy ra khi số lượng foreign key tăng lên.

```python
def test_detail_view_show_genre(client):
    """The genre is on the detail page."""
    director = Director.objects.create(
        name="Steven Spielberg"
    )
    producer = Producer.objects.create(
        name="George Lucas"
    )
    studio = Studio.objects.create(
        name='Paramount'
    )
    movie = Movie.objects.create(
        genre='Sci-Fi',
        director=director,
        producer=producer,
        studio=studio
    )

    response = client.get(
        reverse('movie:detail', args=[movie.id])
    )

    assert response.status_code == 200
    assert 'Sci-Fi' in response.content.decode()
```

Thoạt nhìn, kiểm thử này không _quá_ tệ. Tôi nghĩ phần lớn là do tôi giữ cho phần modeling đơn giản. Nhưng nếu các model `Director`, `Producer` hoặc `Studio` cũng có các khóa ngoại bắt buộc thì sao? Chúng ta sẽ phải dành phần lớn công sức cho bước Arrange (sắp xếp dữ liệu đầu vào) của kiểm thử. Ngoài ra, khi xem xét kiểm thử này, chúng ta bị sa lầy vào những chi tiết không cần thiết. Liệu chúng ta có cần biết tên của đạo diễn, nhà sản xuất và hãng phim không? Không, chúng ta không cần những thông tin đó cho kiểm thử này. Giờ hãy xem phiên bản tương đương sử dụng `factory_boy`.

```python
def test_detail_view_show_genre(client):
    """The genre is on the detail page."""
    movie = MovieFactory(genre='Sci-Fi')

    response = client.get(
        reverse('movie:detail', args=[movie.id])
    )

    assert response.status_code == 200
    assert 'Sci-Fi' in response.content.decode()
```

`MovieFactory` trông như phép màu. Kiểm thử của chúng ta không cần quan tâm đến các chi tiết khác. Giờ kiểm thử chỉ tập trung vào genre.

Factory đơn giản hóa việc tạo bản ghi database. Thay vì nối các model trong kiểm thử, ta chuyển việc đó sang định nghĩa factory. Lợi ích là kiểm thử có thể dùng phong cách đơn giản như ví dụ thứ hai. Nếu cần thêm foreign key mới cho model, chỉ cần cập nhật factory, không phải tất cả kiểm thử khác dùng model đó.

Factory `Movie` có thể trông như sau:

```python
# application/tests/factories.py

import factory

from application.models import Movie

# Other factories defined here...

class MovieFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Movie

    director = factory.SubFactory(
        DirectorFactory
    )
    producer = factory.SubFactory(
        ProducerFactory
    )
    studio = factory.SubFactory(
        StudioFactory
    )
    genre = 'Action'
```

Định nghĩa factory này rất khai báo. Ta khai báo muốn gì, và `factory_boy` sẽ tự biết cách ghép lại. Điều này giúp factory dễ hiểu vì bạn tập trung vào _cái gì_ chứ không phải _làm thế nào_ để tạo model.

Điểm đáng chú ý khác là các factory có thể kết hợp với nhau. Khi gọi `MovieFactory()`, `factory_boy` thiếu dữ liệu về mọi thứ nên phải tự tạo tất cả. Thách thức là `MovieFactory` không biết tạo `Director` hay các foreign key khác. Thay vào đó, factory sẽ ủy quyền cho _các_ factory khác qua thuộc tính `SubFactory`. Nhờ vậy, `factory_boy` có thể tạo model và toàn bộ cây quan hệ chỉ với một lệnh gọi.

Khi muốn ghi đè một số dữ liệu sinh ra, bạn chỉ cần truyền thêm tham số như tôi đã làm với “Sci-Fi” cho trường `genre`. Bạn cũng có thể truyền instance model khác vào factory.

`factory_boy` giúp kiểm thử với bản ghi database trở nên thú vị. Theo kinh nghiệm của tôi, hầu hết kiểm thử Django đều cần một lượng dữ liệu database nào đó nên tôi dùng factory rất nhiều. Tôi nghĩ bạn sẽ thấy `factory_boy` là bổ sung xứng đáng cho bộ công cụ kiểm thử của mình.

## Tóm Tắt

Trong bài viết này, chúng ta đã tìm hiểu về kiểm thử với dự án Django. Chúng ta tập trung vào:

- Tại sao nên viết kiểm thử tự động
- Những loại kiểm thử nào hữu ích cho ứng dụng Django
- Những công cụ nào giúp kiểm thử dễ dàng hơn

Lần tới, chúng ta sẽ tìm hiểu về triển khai. Triển khai là đưa dự án của bạn vào môi trường nơi bạn sẽ chia sẻ ứng dụng cho người khác sử dụng. Đó có thể là internet hoặc mạng nội bộ công ty. Dù bạn triển khai ở đâu, bạn sẽ muốn biết về:

- Triển khai ứng dụng với web application server Python (tức là `./manage.py runserver` không dành cho app đã triển khai)
- Các điều kiện tiên quyết khi triển khai để quản lý settings, migration và static files
- Checklist xác nhận settings đã cấu hình đúng các bảo vệ bảo mật
- Giám sát ứng dụng để phát hiện lỗi
