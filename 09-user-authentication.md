# Xác Thực Người Dùng

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã tìm hiểu về cấu trúc của một _ứng dụng_ Django và cách các ứng dụng là thành phần cốt lõi của một dự án Django. Trong bài viết này, chúng ta sẽ đào sâu vào hệ thống xác thực người dùng tích hợp sẵn của Django. Chúng ta sẽ thấy Django giúp bạn dễ dàng hơn như thế nào bằng cách cung cấp các công cụ để ứng dụng web của bạn tương tác với người dùng của trang.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/)
8. [Giải Phẫu Một Ứng Dụng](https://www.mattlayman.com/understand-django/anatomy-of-an-application/)
9. Xác Thực Người Dùng
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

## Xác Thực Và Phân Quyền

Chúng ta cần bắt đầu với một số thuật ngữ trước khi đi vào nghiên cứu. Khi dự án của bạn tương tác với người dùng, có hai khía cạnh chính gắn liền với người dùng mà chúng ta phải xem xét.

_Xác thực (Authentication)_: Khi một người dùng cố gắng chứng minh họ là ai, đó là xác thực. Một người dùng thường xác thực với trang của bạn thông qua một form đăng nhập hoặc sử dụng nhà cung cấp xã hội như Google để xác minh danh tính.

> _Xác thực chỉ có thể chứng minh rằng _[_bạn là bạn_](https://en.wikipedia.org/wiki/The_Important_Book)_._

_Phân quyền (Authorization)_: Người dùng được phép làm gì? Phân quyền trả lời câu hỏi đó. Chúng ta dùng phân quyền để xác định quyền hoặc nhóm mà người dùng thuộc về, từ đó giới hạn những gì người dùng có thể làm trên trang.

> _Phân quyền xác định bạn có thể làm gì._

Hệ thống xác thực của Django bao phủ cả hai chủ đề này. Đôi khi ngành phần mềm sẽ rút gọn authentication thành “authn” và authorization thành “authz”, nhưng tôi nghĩ các nhãn đó khá ngớ ngẩn và dễ gây nhầm lẫn. Tôi sẽ gọi rõ tên đầy đủ và gọi toàn bộ hệ thống của Django là “auth”.

## Thiết Lập

Nếu bạn dùng lệnh `startproject` để bắt đầu dự án, thì xin chúc mừng, bạn đã xong phần này và có thể chuyển tiếp!

Các tính năng xác thực trong Django yêu cầu một vài ứng dụng tích hợp sẵn của Django và một số middleware.

Các ứng dụng Django là:

- `django.contrib.auth` và
- `django.contrib.contenttypes` (ứng dụng `auth` phụ thuộc vào cái này)

Các middleware là:

- `SessionMiddleware` để lưu dữ liệu về người dùng trong session
- `AuthenticationMiddleware` để liên kết người dùng với request

Middleware và session đều là các chủ đề sẽ bàn sau nên hiện tại bạn có thể coi chúng là chi tiết nội bộ và bỏ qua.

Tài liệu Django cung cấp thêm bối cảnh về các điều kiện tiên quyết này, bạn có thể xem thêm ở phần [cài đặt chủ đề xác thực](https://docs.djangoproject.com/en/4.1/topics/auth/#installation).

## Ai Sẽ Xác Thực?

Nếu trang của bạn sẽ có bất kỳ mức độ cá nhân hóa nào cho người dùng, thì chúng ta cần một cách để theo dõi danh tính.

Trong hệ thống xác thực của Django, danh tính được theo dõi bằng model `User`. Model này lưu trữ thông tin mà bạn có thể muốn liên kết với bất kỳ ai sử dụng trang của bạn. Model bao gồm:

- các trường tên,
- địa chỉ email,
- các trường ngày giờ cho thời điểm người dùng tham gia hoặc đăng nhập vào trang,
- các trường boolean cho một số quyền cơ bản rất thường dùng,
- và dữ liệu mật khẩu.

Model `User` là một model cực kỳ quan trọng trong nhiều hệ thống. Trừ khi bạn tạo một website hoàn toàn công khai và không cần quan tâm đến danh tính, bạn có lẽ sẽ sử dụng model `User` rất nhiều.

Ngay cả khi bạn _không_ mong đợi khách truy cập trang của mình phải xác định danh tính, bạn vẫn có thể hưởng lợi từ model `User` vì nó được tích hợp với trang quản trị Django. Tôi đã đề cập trong [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/) rằng chúng ta cần một người dùng có quyền nhất định để truy cập admin, nhưng tôi đã lướt qua chi tiết điều đó nghĩa là gì.

Trang admin chỉ cho phép người dùng có thuộc tính `is_staff` được đặt là `True`. `is_staff` là một trong các trường boolean mà tôi đã liệt kê là có trong model `User` mặc định.

Giờ chúng ta đã hiểu rằng model `User` là một model rất quan trọng trong một trang Django. Tối thiểu, model này quan trọng khi bạn dùng trang admin Django, nhưng nó cũng có thể rất quan trọng với những người truy cập trang của bạn.

Tiếp theo, hãy xem sâu hơn về xác thực và cách nó hoạt động cùng với model `User`.

## Xác Thực Bằng Mật Khẩu

Giống như nhiều website khác mà bạn từng dùng, hệ thống xác thực tích hợp của Django xác thực người dùng bằng mật khẩu.

Khi một người dùng muốn xác thực, họ phải đăng nhập vào trang. Django cung cấp một view dạng class `LoginView` có thể xử lý các bước cần thiết. `LoginView` là một form view mà:

- Thu thập `username` và `password` từ người dùng
- Gọi hàm `django.contrib.auth.authenticate` với `username` và `password` để xác nhận người dùng là ai họ nói
- Chuyển hướng đến một đường dẫn được đặt trong tham số `next` của querystring URL hoặc `settings.LOGIN_REDIRECT_URL` nếu không có tham số `next`
- Hoặc, nếu xác thực thất bại, render lại trang form với thông báo lỗi phù hợp

Hàm `authenticate` hoạt động như thế nào? Hàm `authenticate` ủy quyền trách nhiệm quyết định thông tin xác thực của người dùng có hợp lệ không cho một _authentication backend_.

Như chúng ta đã thấy với template và cơ sở dữ liệu, hệ thống xác thực cũng có các backend có thể hoán đổi. Với các backend khác nhau, bạn có thể có nhiều cách xác thực. Hàm `authenticate` sẽ lặp qua các backend xác thực được thiết lập trong danh sách `AUTHENTICATION_BACKENDS` trong settings. Mỗi backend có thể làm một trong ba việc:

- Xác thực đúng với người dùng và trả về một instance `User`.
- Không xác thực và trả về `None`. Khi đó backend tiếp theo sẽ được thử.
- Không xác thực và raise exception `PermissionDenied`. Khi đó sẽ không thử backend nào nữa.

Bạn có thể thêm một backend vào thiết lập đó để cho phép người dùng xác thực bằng tài khoản mạng xã hội (ví dụ [django-allauth](https://django-allauth.readthedocs.io/en/latest/) là một lựa chọn tuyệt vời). Bạn có thể ở môi trường doanh nghiệp và cần Single Sign-On (SSO) cho công ty. Cũng có các backend hỗ trợ điều đó.

Dù có nhiều lựa chọn, chúng ta sẽ tập trung vào backend tích hợp sẵn đi kèm hệ thống xác thực. Backend mặc định gọi là `ModelBackend` và nằm trong module `django.contrib.auth.backends`.

`ModelBackend` được đặt tên như vậy vì nó dùng model `User` để xác thực. Khi nhận được `username` và `password` từ người dùng, backend sẽ so sánh dữ liệu đó với các bản ghi `User` hiện có.

Hàm `authenticate` sẽ gọi _phương thức_ `authenticate` trên `ModelBackend`. Backend sẽ tìm một bản ghi `User` dựa trên `username` được truyền vào phương thức bởi hàm `authenticate`. Nếu bản ghi người dùng tồn tại, backend sẽ gọi `user.check_password(password)` với `password` là mật khẩu thực tế do người dùng gửi lên qua POST tới `LoginView`.

Django không lưu mật khẩu thực tế. Làm vậy sẽ là một điểm yếu lớn vì nếu cơ sở dữ liệu bị lộ thì toàn bộ mật khẩu người dùng cũng bị lộ. Thay vào đó, trường `password` trên model `User` lưu _hash_ của mật khẩu.

Có thể bạn chưa từng gặp hashing trước đây. Hash là một giá trị được tính toán bằng cách đưa dữ liệu đầu vào qua một hàm đặc biệt. Chi tiết về tính toán hash là một chủ đề rất sâu, nhất là khi xét về bảo mật, nhưng điều quan trọng cần biết là bạn _không thể đảo ngược_ phép tính này.

Nói cách khác, nếu bạn tạo hash từ `mysekretpassword`, bạn sẽ không thể lấy giá trị hash đó để tìm ra đầu vào gốc là `myseckretpassword`.

Tại sao điều này hữu ích? Bằng cách tính toán hash, Django có thể lưu giá trị đã tính toán đó mà không làm lộ mật khẩu người dùng. Khi người dùng muốn xác thực với trang, họ gửi mật khẩu, Django sẽ tính hash trên giá trị gửi lên và _so sánh với hash lưu trong cơ sở dữ liệu._ Nếu hai hash trùng nhau, trang có thể kết luận người dùng đã gửi đúng mật khẩu. Chỉ hash của mật khẩu đúng mới trùng với hash lưu trong model `User`.

Hashing là một chủ đề thú vị. Nếu bạn muốn tìm hiểu thêm về cách Django quản lý hash, hãy đọc [Quản lý mật khẩu trong Django](https://docs.djangoproject.com/en/4.1/topics/auth/passwords/) để xem chi tiết.

## Các View Xác Thực

Có rất nhiều thứ phải làm cho xác thực!

Django có bắt bạn phải tự gọi hàm `authenticate` và tự kết nối các view không? Không!

Tôi đã đề cập đến `LoginView` ở trên, nhưng đó không phải là view duy nhất mà Django cung cấp để giúp quản lý xác thực. Bạn có thể thêm bộ view này chỉ với một dòng `include`:

```python
# project/urls.py

from django.urls import include, path

urlpatterns = [
    ...
    path(
        "accounts/",
        include("django.contrib.auth.urls")
    ),
]
```

Bộ này bao gồm nhiều tính năng:

- View đăng nhập
- View đăng xuất
- View đổi mật khẩu
- View đặt lại mật khẩu

Nếu bạn chọn thêm bộ này, việc của bạn là override các template tích hợp sẵn để phù hợp với giao diện trang của bạn. Ví dụ, để tùy biến view đăng xuất, bạn sẽ tạo một file tên là `registration/logged_out.html` trong thư mục template của bạn. [Tài liệu về tất cả các view xác thực](https://docs.djangoproject.com/en/4.1/topics/auth/default/#all-authentication-views) cung cấp thông tin về từng view và tên template cần override. Lưu ý bạn _phải_ cung cấp template cho view đăng nhập vì framework không cung cấp template mặc định cho view đó.

Nếu bạn có nhu cầu phức tạp hơn cho trang của mình, bạn có thể cân nhắc các ứng dụng Django bên ngoài trong hệ sinh thái. Cá nhân tôi thích [django-allauth](https://django-allauth.readthedocs.io/en/latest/). Dự án này rất dễ tùy biến và cung cấp cách thêm xác thực xã hội để đăng ký bằng nền tảng mạng xã hội bạn chọn. Tôi cũng thích django-allauth vì nó có sẵn luồng đăng ký mà bạn không phải tự xây dựng. Ứng dụng này rất đáng để thử.

Chúng ta đã thấy Django xác thực người dùng với model `User`, hàm `authenticate` và backend xác thực tích hợp `ModelBackend`. Chúng ta cũng đã thấy Django cung cấp các view hỗ trợ đăng nhập, đăng xuất và quản lý mật khẩu.

Khi một người dùng đã xác thực, họ được phép làm gì? Chúng ta sẽ xem tiếp khi tìm hiểu về phân quyền trong Django.

## Được Phép Làm Gì?

### Phân Quyền Dựa Trên Thuộc Tính Người Dùng

Django có nhiều cách để bạn kiểm soát người dùng được phép làm gì trên trang.

Cách đơn giản nhất để kiểm tra người dùng là xem trang đã nhận diện người dùng hay chưa. Trước khi người dùng xác thực bằng cách đăng nhập, họ là người dùng ẩn danh. Thực tế, hệ thống xác thực Django có một class đặc biệt để biểu diễn loại người dùng này. Đúng như nguyên tắc “ít gây bất ngờ”, class này tên là `AnonymousUser`.

Model `User` có thuộc tính `is_authenticated`. Dễ đoán, người dùng đã xác thực sẽ trả về `True` cho `is_authenticated` còn instance `AnonymousUser` sẽ trả về `False` cho thuộc tính này.

Django cung cấp decorator `login_required` có thể dùng thông tin `is_authenticated` này. Decorator này sẽ chặn bất kỳ view nào cần người dùng đã xác thực.

Đây có thể là mức kiểm tra phân quyền phù hợp nếu bạn có ứng dụng chỉ cho phép một số người đăng nhập. Ví dụ, nếu bạn vận hành một ứng dụng SaaS yêu cầu người dùng trả phí để sử dụng sản phẩm, bạn có thể chỉ cần kiểm tra `is_authenticated` là đủ. Trong trường hợp đó, nếu ứng dụng chỉ cho phép người dùng có đăng ký (hoặc dùng thử) đăng nhập, `login_required` sẽ ngăn không cho người dùng không trả phí sử dụng sản phẩm.

Có các giá trị boolean khác trên model `User` mà bạn có thể dùng để kiểm tra phân quyền.

- `is_staff` là boolean để quyết định người dùng có phải là nhân viên không. Mặc định, giá trị này là `False`. Chỉ người dùng staff mới được dùng trang admin tích hợp của Django. Bạn cũng có thể dùng decorator `staff_member_required` nếu có các view chỉ dành cho thành viên nhóm của bạn có quyền đó.
- `is_superuser` là cờ đặc biệt để chỉ người dùng có quyền truy cập mọi thứ. Khái niệm “superuser” này rất giống với superuser trong hệ thống phân quyền của Linux. Không có decorator đặc biệt cho boolean này, nhưng bạn có thể dùng decorator `user_passes_test` nếu có các view riêng tư cần bảo vệ.

```python
from django.contrib.admin.views.decorators import (
    staff_member_required
)
from django.contrib.auth.decorators import (
    user_passes_test
)
from django.http import HttpResponse

@staff_member_required
def a_staff_view(request):
    return HttpResponse(
        "You are a user with staff level permission."
    )

def check_superuser(user):
    return user.is_superuser

@user_passes_test(check_superuser)
def special_view(request):
    return HttpResponse(
        "Super special response"
    )
```

Decorator `user_passes_test` hoạt động giống như `login_required`, nhưng nó nhận một callable nhận vào một object user và trả về boolean. Nếu giá trị boolean là `True`, request được phép và người dùng nhận response. Nếu boolean là `False`, người dùng sẽ bị chuyển hướng đến trang đăng nhập.

### Phân Quyền Dựa Trên Quyền Và Nhóm

Bộ kiểm tra đầu tiên mà chúng ta xem là dữ liệu lưu cùng bản ghi model `User`. Dù cách này phù hợp với một số trường hợp, còn phân quyền phụ thuộc vào chức năng ứng dụng thì sao?

Django đi kèm hệ thống quyền linh hoạt cho phép ứng dụng kiểm soát ai được xem gì. Hệ thống quyền bao gồm một số quyền tự động tạo cũng như khả năng tạo quyền tùy biến cho bất kỳ mục đích nào. Các bản ghi quyền này là instance model `Permission` từ `django.contrib.auth.models`.

Bất cứ khi nào bạn tạo một model mới, Django sẽ tạo thêm một bộ quyền. Các quyền tự động này ánh xạ tới các thao tác CRUD mà bạn có thể dùng trong admin Django. Ví dụ, nếu bạn có app `pizzas` và tạo model `Topping`, Django sẽ tạo các quyền sau:

- `pizzas.add_topping` cho Create
- `pizzas.view_topping` cho Read
- `pizzas.change_topping` cho Update
- `pizzas.delete_topping` cho Delete

Lý do lớn để tạo các quyền này là để hỗ trợ phát triển _và_ kiểm soát trong admin Django. Người dùng staff (tức là `user.is_staff == True`) trong ứng dụng của bạn ban đầu _không có quyền nào_. Đây là mặc định an toàn để bất kỳ nhân viên mới nào cũng không thể truy cập toàn bộ dữ liệu hệ thống trừ khi bạn cấp thêm quyền khi đã tin tưởng họ.

Khi một người dùng staff đăng nhập vào admin Django, ban đầu họ sẽ thấy rất ít. Khi cấp thêm quyền cho tài khoản, admin Django sẽ hiển thị thêm thông tin tương ứng với quyền đã chọn. Dù quyền thường được cấp qua trang admin của `User`, bạn cũng có thể thêm quyền cho user qua mã. Model `User` có một trường `ManyToManyField` tên là `user_permissions` liên kết instance user với các quyền cụ thể.

Tiếp tục với ví dụ ứng dụng pizza, giả sử bạn làm việc với một đầu bếp cho app pizza. Đầu bếp của bạn có thể cần quyền kiểm soát các topping mới cho khách hàng, nhưng bạn có lẽ không muốn đầu bếp xóa đơn hàng khỏi lịch sử ứng dụng.

Với đầu bếp, bạn sẽ cấp quyền `pizzas.add_topping`, `pizzas.view_topping`, và `pizzas.change_topping`, nhưng không cấp `orders.delete_order`.

```python
from django.contrib.auth.models import (
    Permission, User
)
from django.contrib.contenttypes.models import (
    ContentType
)
from pizzas.models import Topping

content_type = ContentType.objects.get_for_model(
    Topping
)
permission = Permission.objects.get(
    content_type=content_type,
    codename="add_topping"
)
chef_id = 42
chef = User.objects.get(id=42)
chef.user_permissions.add(permission)
```

Chúng ta chưa bàn về app `contenttypes` nên đoạn mã này có thể lạ với bạn, nhưng hệ thống xác thực dùng content type để tham chiếu model một cách tổng quát khi xử lý quyền. Bạn có thể tìm hiểu thêm về content type và cách dùng tại [tài liệu contenttypes framework](https://docs.djangoproject.com/en/4.1/ref/contrib/contenttypes/). Điều quan trọng là quyền hoạt động như bất kỳ model Django nào khác.

Thêm quyền cho từng user là tính năng hay cho nhóm nhỏ, nhưng nếu nhóm của bạn lớn, việc này có thể trở thành ác mộng.

Giả sử ứng dụng của bạn rất thành công và bạn cần thuê một đội ngũ hỗ trợ lớn để giúp khách hàng. Nếu đội hỗ trợ cần xem một số model trong hệ thống, sẽ rất phiền nếu bạn phải quản lý quyền cho từng thành viên.

Django có khả năng tạo nhóm để giải quyết vấn đề này. Model `Group` là giao điểm giữa một tập quyền và một tập người dùng. Như vậy, bạn có thể tạo một nhóm như “Support Team”, gán tất cả quyền mà nhóm cần, và thêm tất cả nhân viên hỗ trợ vào nhóm đó. Khi cần cấp quyền mới cho nhóm, chỉ cần thêm một lần vào nhóm.

Các nhóm của user được theo dõi bằng một trường `ManyToManyField` khác tên là `groups`.

```python
from django.contrib.auth.models import (
    Group, User
)

support_team = Group.objects.get(
    name="Support Team"
)
support_sally = User.objects.get(
    username="sally"
)
support_sally.groups.add(support_team)
```

Ngoài các quyền tích hợp mà Django tạo và hệ thống quản lý nhóm, bạn cũng có thể tạo thêm quyền cho mục đích riêng.

Hãy cấp cho đầu bếp quyền nướng pizza trong app tưởng tượng của chúng ta.

```python
from django.contrib.auth.models import (
    Permission, User
)
from django.contrib.contenttypes.models import (
    ContentType
)
from pizzas.models import Pizza

content_type = ContentType.objects.get_for_model(
    Pizza
)
permission = Permission.objects.create(
    codename="can_bake",
    name="Can Bake Pizza",
    content_type=content_type,
)
chef_id = 42
chef = User.objects.get(id=42)
chef.user_permissions.add(permission)
```

Để kiểm tra quyền trong mã, bạn có thể dùng phương thức `has_perm` trên model `User`. `has_perm` nhận một app label và codename quyền nối với nhau bằng dấu chấm.

```python
>>> chef = User.objects.get(id=42)
>>> chef.has_perm('pizzas.can_bake')
True
```

Bạn cũng có thể dùng decorator trên view để kiểm tra quyền. Decorator sẽ kiểm tra `request.user` có quyền phù hợp không.

```python
# pizzas/views.py

from django.contrib.auth.decorators import permission_required

@permission_required('pizzas.can_bake')
def bake_pizza(request):
    # Time to bake the pizza
    # if you're allowed.
    ...
```

## Làm Việc Với Người Dùng Trong View Và Template

Chúng ta đã bàn về cách xác thực người dùng và kiểm tra phân quyền. Vậy làm sao _tương tác_ với người dùng trong mã ứng dụng?

Cách đầu tiên là trong view. Một phần của việc cấu hình hệ thống xác thực là thêm `AuthenticationMiddleware` trong `django.contrib.auth.middleware`.

Middleware này có một nhiệm vụ khi xử lý request: thêm thuộc tính `user` vào `request` mà view sẽ nhận. Middleware này giúp chúng ta truy cập bản ghi user rất tiện lợi.

```python
# application/views.py

from django.http import HttpResponse

def my_view(request):
    if request.user.is_authenticated:
        return HttpResponse(
            'You are logged in.'
        )
    else:
        return HttpResponse(
            'Hello guest!'
        )
```

`AuthenticationMiddleware` là thứ giúp các decorator tôi đã mô tả trong bài này (như `login_required`, `user_passes_test`, và `permission_required`) hoạt động. Mỗi decorator sẽ tìm bản ghi `user` như một thuộc tính gắn vào `request`.

Còn template thì sao? Nếu bạn phải thêm user vào context của view cho mọi view thì sẽ rất phiền.

May mắn thay, có một context processor tên là `auth` giúp bạn tránh được điều đó (processor này nằm trong `django.contrib.auth.context_processors`). Context processor này sẽ thêm `user` vào context của mọi view khi xử lý request.

Nhớ rằng context processor là một hàm nhận object `request` và trả về một dictionary sẽ được trộn vào context. Biết vậy, bạn đoán context processor này hoạt động thế nào?

Nếu bạn đoán là `AuthenticationMiddleware`, bạn đúng rồi đấy! 🍪 Vì middleware thêm `user` vào `request`, context processor chỉ cần tạo dictionary như `{'user': request.user}`. Thực tế có thêm một chút chi tiết, bạn có thể xem [mã nguồn Django](https://github.com/django/django/blob/4.1/django/contrib/auth/context_processors.py#L49) nếu muốn biết thêm.

Thực tế nó trông như thế nào? Chúng ta đã từng thấy rồi! Một ví dụ trong phần giải thích template đã dùng biến context `user`. Đây là ví dụ để bạn không phải quay lại xem:

```django
{% if user.is_authenticated %}
    <h1>Welcome, {{ user.username }}</h1>
{% endif %}
```

Nếu bạn dùng quyền của Django, bạn cũng có thể tận dụng biến context `perms` trong template. Biến này cũng được cung cấp bởi context processor `auth` và cho phép template truy cập quyền của `user` một cách ngắn gọn. [Tài liệu Django](https://docs.djangoproject.com/en/4.1/topics/auth/default/#permissions) có một số ví dụ hay về cách dùng biến `perms`.

Giờ bạn đã thấy Django tận dụng middleware xác thực để giúp truy cập người dùng dễ dàng trong view và template.

## Tóm Tắt

Trong bài viết này, chúng ta đã tìm hiểu về hệ thống xác thực người dùng tích hợp của Django.

Chúng ta đã học về:

- Cách thiết lập xác thực
- Model `User` là gì
- Cách xác thực hoạt động
- Các view tích hợp của Django để xây dựng hệ thống đăng nhập
- Các mức phân quyền có sẵn
- Cách truy cập người dùng trong view và template

Lần tới chúng ta sẽ tìm hiểu về middleware trong Django. Đúng như tên gọi, middleware là đoạn mã tồn tại ở “giữa” quá trình request và response. Chúng ta sẽ học về:

- Mô hình tư duy khi làm việc với middleware
- Cách tự viết middleware
- Một số class middleware đi kèm Django
