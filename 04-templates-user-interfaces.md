# Template Cho Giao Diện Người Dùng

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã xem các kiến thức cơ bản về việc sử dụng view trong Django. Bài viết này sẽ tập trung vào template. Template là công cụ chủ lực của bạn trong một dự án Django để tạo ra giao diện người dùng. Với template, bạn sẽ có thể xây dựng các trang mà người dùng sẽ nhìn thấy khi truy cập ứng dụng web của bạn. Hãy xem cách template kết nối với view và những tính năng mà Django cung cấp với hệ thống template của nó.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. Template Cho Giao Diện Người Dùng
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

## Thiết Lập Template

Chúng ta cần một nơi để lưu trữ các template. Template là các file tĩnh mà Django sẽ điền dữ liệu vào. Để sử dụng các file này, chúng ta phải hướng dẫn Django biết vị trí của chúng.

Như hầu hết các phần khác của Django, cấu hình này nằm trong file settings của dự án. Sau khi bạn dùng `startproject`, bạn sẽ thấy một phần trong file settings có tên là `TEMPLATES`. Phần này sẽ trông như sau:

```python
# project/settings.py

TEMPLATES = [{
    'BACKEND': 'django.template.backends.django.DjangoTemplates',
    'DIRS': [],
    'APP_DIRS': True,
    'OPTIONS': {
        'context_processors': [
            'django.template.context_processors.debug',
            'django.template.context_processors.request',
            'django.contrib.auth.context_processors.auth',
            'django.contrib.messages.context_processors.messages',
        ],
    },
}]
```

Hệ thống template của Django có thể sử dụng nhiều backend template khác nhau. Backend quyết định cách template của bạn sẽ hoạt động. Tôi khuyên bạn nên giữ nguyên ngôn ngữ template mặc định của Django. Ngôn ngữ này tích hợp chặt chẽ nhất với framework và được hỗ trợ mạnh mẽ nhất.

Điều tiếp theo cần chú ý là `APP_DIRS` với giá trị `True`. Đối với ngôn ngữ template của Django, đặt giá trị này là `True` sẽ khiến Django tìm các file template trong thư mục `templates` của mỗi ứng dụng Django trong dự án của bạn. Lưu ý rằng điều này cũng bao gồm cả các ứng dụng bên thứ ba nên bạn nên giữ giá trị này là `True`.

Vậy, template _của bạn_ nên đặt ở đâu? Có nhiều trường phái khác nhau trong cộng đồng Django. Một số lập trình viên thích đặt tất cả template trong từng ứng dụng. Số khác lại thích đặt tất cả template của dự án vào một thư mục duy nhất. Tôi thuộc nhóm thứ hai. Tôi thấy việc giữ tất cả template của toàn bộ dự án trong một thư mục là rất hữu ích.

Theo quan điểm của tôi, giữ template trong một thư mục giúp bạn dễ dàng biết toàn bộ bố cục và giao diện của hệ thống nằm ở đâu. Để dùng cách này, chúng ta phải thiết lập biến `DIRS` với thư mục mà chúng ta muốn Django sử dụng. Tôi khuyên bạn nên đặt một thư mục `templates` ở gốc dự án. Nếu làm vậy, giá trị `DIRS` của bạn sẽ như sau:

```python
# project/settings.py

TEMPLATES = [
...
    "DIRS": [BASE_DIR / "templates"],
...
]
```

Cuối cùng là `OPTIONS`. Mỗi backend có thể nhận nhiều tùy chọn khác nhau. `startproject` sẽ thiết lập một số context processor. Chúng ta sẽ quay lại context processor ở phần sau của bài viết này.

Khi đã thiết lập template, bạn đã sẵn sàng bắt đầu!

## Sử Dụng Template Với Render

Django xây dựng giao diện người dùng của bạn bằng cách _render_ một template. Ý tưởng của render là dữ liệu động sẽ được kết hợp với một file template tĩnh để tạo ra kết quả cuối cùng.

Để tạo một `HttpResponse` chứa nội dung đã render, chúng ta dùng hàm `render`. Hãy xem ví dụ với một view dạng hàm (FBV).

```python
# application/views.py

from django.shortcuts import render

def hello_view(request):
    context = {'name': 'Johnny'}
    return render(
        request,
        'hello.txt',
        context
    )
```

Trong ví dụ này, view sẽ sử dụng một template nằm ở `templates/hello.txt` có thể chứa:

```django
Hello {{ name }}
```

Khi view này phản hồi một request, người dùng sẽ thấy “Hello Johnny” trên trình duyệt. Có một số điểm thú vị cần lưu ý ở ví dụ này.

1. Template có thể là bất kỳ loại file văn bản thuần nào. Thông thường chúng ta sẽ dùng HTML để tạo giao diện người dùng nên bạn sẽ thường thấy `some_template.html`, nhưng hệ thống template của Django có thể render trên bất kỳ loại file nào.
2. Trong quá trình render, Django lấy dictionary context và dùng các key của nó làm tên biến trong template. Nhờ cú pháp dấu ngoặc nhọn kép đặc biệt, backend template sẽ thay thế `{{ name }}` bằng giá trị thực “Johnny” có trong context.

Ý tưởng kết hợp context và bố cục tĩnh là khái niệm cốt lõi khi làm việc với template. Phần còn lại của bài viết này sẽ xây dựng dựa trên ý tưởng này và cho bạn thấy những gì ngôn ngữ template của Django có thể làm được.

Ngoài lề, HTML là một chủ đề mà chúng ta sẽ không đi sâu vào. HTML, hay Hypertext Markup Language, là ngôn ngữ dùng trên web để mô tả cấu trúc của một trang. HTML gồm các thẻ và nhiều thẻ trong số đó đi theo cặp. Ví dụ, để tạo một _đoạn văn_, bạn có thể dùng thẻ `p`, được biểu diễn bằng cách đặt `p` trong dấu lớn hơn và nhỏ hơn để tạo thẻ “mở”. Thẻ “đóng” tương tự, nhưng có thêm dấu gạch chéo.

```html
<p>This is a paragraph example.</p>
```

Từ bài trước, bạn có thể nhớ đã thấy `TemplateView`. Trong các ví dụ đó, chúng ta truyền vào tên template, và tôi đã nói rằng Django sẽ lo phần còn lại. Bây giờ bạn có thể hiểu rằng Django lấy tên template và gọi mã tương tự như `render` để trả về một `HttpResponse`. Các ví dụ đó thiếu dữ liệu context để kết hợp với template. Một ví dụ đầy đủ hơn, tái hiện lại view hàm `hello_view` dưới dạng view lớp sẽ như sau:

```python
# application/views.py

from django.views.generic.base import TemplateView

class HelloView(TemplateView):
    template_name = 'hello.txt'

    def get_context_data(
        self,
        *args,
        **kwargs
    ):
        context = super().get_context_data(
            *args, **kwargs)
        context['name'] = 'Johnny'
        return context
```

Ví dụ này sử dụng `get_context_data` để chúng ta có thể chèn dữ liệu “động” vào hệ thống render nhằm tạo ra phản hồi mong muốn.

Trong một ứng dụng thực tế, phần lớn mã bạn cần viết sẽ tập trung vào việc xây dựng context thực sự động. Tôi dùng dữ liệu tĩnh trong các ví dụ này để làm rõ cơ chế của hệ thống template. Khi bạn thấy tôi dùng `context`, hãy tưởng tượng đến việc xây dựng dữ liệu phức tạp hơn để tạo giao diện người dùng.

Đó là những kiến thức nền tảng về render. Bây giờ chúng ta sẽ chuyển sang xem ngôn ngữ template của Django có thể làm gì.

## Template Trong Thực Tế

Khi sử dụng template, chúng ta lấy dữ liệu context và chèn vào các vị trí placeholder trong template.

Biến template là hình thức cơ bản nhất để điền dữ liệu context vào placeholder. Phần trước đã cho ví dụ với biến `name`. Dictionary context chứa key `name`, giá trị của nó sẽ xuất hiện ở bất cứ đâu trong template mà key đó được đặt trong dấu ngoặc nhọn kép.

Chúng ta cũng có thể dùng cú pháp dấu chấm khi dữ liệu context phức tạp hơn. Giả sử template của bạn nhận context như sau:

```python
context = {
    'address': {
        'street': '123 Main St.',
        'city': 'Beverly Hills',
        'state': 'CA',
        'zip_code': '90210',
    }
}
```

Template Django _sẽ không_ hoạt động nếu bạn cố truy cập dữ liệu context như dictionary thông thường (ví dụ, `{{ address['street'] }}`). Thay vào đó, bạn sẽ dùng cú pháp dấu chấm để lấy dữ liệu trong dictionary.

```django
The address is:
    {{ address.street }}
    {{ address.city }}, {{ address.state }} {{ address.zip_code}}
```

Điều này sẽ render thành:

```text
The address is:
    123 Main St.
    Beverly Hills, CA 90210
```

Template Django cũng cố gắng linh hoạt với kiểu dữ liệu context. Bạn cũng có thể truyền vào một instance class Python như class `Address` với các thuộc tính giống như các key trong dictionary ở trên. Template sẽ hoạt động tương tự.

Ngôn ngữ template cốt lõi cũng bao gồm một số từ khóa logic lập trình tiêu chuẩn bằng cách sử dụng _tag_. Tag template trông như `{% some_tag %}` trong khi biến template trông như `{{ some_variable }}`. Biến dùng để điền dữ liệu, còn tag cung cấp nhiều sức mạnh hơn.

Chúng ta có thể bắt đầu với hai tag cốt lõi là `if` và `for`.

Tag `if` dùng để xử lý logic điều kiện mà template của bạn có thể cần.

```django
{% if user.is_authenticated %}
    <h1>Welcome, {{ user.username }}</h1>
{% endif %}
```

Ví dụ này chỉ hiển thị thẻ HTML header chào mừng khi người dùng đã đăng nhập vào ứng dụng. Chúng ta bắt đầu ví dụ với tag `if`. Lưu ý rằng tag `if` cần một tag đóng `endif`. Template phải tôn trọng khoảng trắng vì bố cục của bạn có thể phụ thuộc vào khoảng trắng đó. Ngôn ngữ template không thể dùng khoảng trắng để xác định phạm vi như Python nên nó dùng tag đóng. Như bạn có thể đoán, cũng có các tag `else` và `elif` được chấp nhận trong cặp `if`/`endif`.

```django
{% if user.is_authenticated %}
    <h1>Welcome, {{ user.username }}</h1>
{% else %}
    <h1>Welcome, guest</h1>
{% endif %}
```

Trong trường hợp này, chỉ một trong hai thẻ header sẽ được render tùy vào việc người dùng đã xác thực hay chưa.

Tag cốt lõi còn lại là tag vòng lặp `for`. Vòng lặp `for` trong template Django hoạt động như bạn mong đợi.

```django
<p>Prices:</p>
<ul>
{% for item in items %}
    <li>{{ item.name }} costs {{ item.price }}.</li>
{% endfor %}
</ul>
```

Django sẽ lặp qua các iterable như list và cho phép bạn xuất ra phản hồi template cho từng phần tử trong iterable. Nếu ví dụ trên có một list `items` trong context như:

```python
items = [
    {'name': 'Pizza', 'price': '$12.99'},
    {'name': 'Soda', 'price': '$3.99'},
]
```

Thì kết quả sẽ trông như:

```html
<p>Prices:</p>
<ul>
    <li>Pizza costs $12.99.</li>
    <li>Soda costs $3.99.</li>
</ul>
```

Đôi khi, bạn có thể muốn thực hiện một hành động cụ thể với một phần tử nhất định trong vòng lặp `for`. Hàm `enumerate` tích hợp của Python không có sẵn trực tiếp trong template, nhưng một biến đặc biệt tên là `forloop` sẽ có sẵn bên trong tag `for`. Biến `forloop` này có một số thuộc tính như `first` và `last` mà bạn có thể dùng để template hoạt động khác nhau ở một số vòng lặp nhất định.

```django
Counting:
{% for number in first_three_numbers %}
    {{ number }}{% if forloop.last %} is last!{% endif %}
{% endfor %}
```

‌

Ví dụ này sẽ tạo ra:

```text
Counting:
    1
    2
    3 is last!
```

Với biến, tag `if` và tag `for`, bạn đã có thể tạo ra những template khá mạnh mẽ, nhưng vẫn còn nhiều thứ nữa!

### Thêm Bối Cảnh Cho Context

Khi thiết lập settings cho template, chúng ta đã lướt qua context processor. Context processor là một cách hữu ích để mở rộng context có sẵn cho template khi render.

Đây là tập hợp context processor mà lệnh `startproject` của Django thêm vào mặc định.

```python
'context_processors': [
    'django.template.context_processors.debug',
    'django.template.context_processors.request',
    'django.contrib.auth.context_processors.auth',
    'django.contrib.messages.context_processors.messages',
],
```

Context processor là các hàm (chính xác là callable, nhưng hãy tập trung vào hàm) nhận một `HttpRequest` và phải trả về một dictionary. Dictionary trả về sẽ được hợp nhất với bất kỳ context nào khác được truyền vào template.

Về mặt ý tưởng, khi chuẩn bị render và có một dictionary `context` được truyền vào `render`, hệ thống template sẽ làm như sau:

```python
for processor in context_processors:
    context.update(processor(request))

# Tiếp tục render template
```

Mã thực tế trong hệ thống template phức tạp hơn một chút, nhưng không khác nhiều so với đoạn mô tả ý tưởng này!

Chúng ta có thể xem định nghĩa thực tế của context processor `request` có trong danh sách mặc định đó.

```python
# django/template/context_processors.py

def request(request):
    return {'request': request}
```

Chỉ vậy thôi! Nhờ context processor này, đối tượng `request` sẽ có sẵn như một biến cho bất kỳ template nào trong dự án của bạn. Điều này rất mạnh mẽ.

#### Bên Lề

Đừng ngại xem mã nguồn của các dự án mà bạn phụ thuộc vào. Hãy nhớ rằng những người bình thường đã viết ra các framework mà bạn yêu thích! Bạn có thể học được nhiều bài học giá trị từ những gì họ đã làm. Ban đầu mã nguồn có thể hơi khó hiểu, nhưng không có gì là ma thuật cả!

“Mặt tối” của context processor là chúng sẽ chạy cho mọi request. Nếu bạn viết một context processor chậm và thực hiện nhiều tính toán, _mọi request_ sẽ bị ảnh hưởng về hiệu năng. Vì vậy hãy dùng context processor một cách cẩn thận.

### Các Đoạn Template Tái Sử Dụng

Bây giờ hãy nói về một trong những tính năng mạnh mẽ nhất của hệ thống template: các phần có thể tái sử dụng.

Hãy nghĩ về một website. Hầu hết các trang đều có giao diện và cảm giác giống nhau. Điều này đạt được bằng cách lặp lại rất nhiều HTML giống nhau, tức là Hypertext Markup Language định nghĩa cấu trúc của một trang. Các trang này cũng dùng cùng một CSS, tức là Cascading Style Sheets, định nghĩa kiểu dáng cho các phần tử trang.

Hãy tưởng tượng bạn được giao quản lý một trang web và cần tạo hai trang riêng biệt. Trang chủ trông như sau:

```html
<!DOCTYPE html>
<html>
    <head>
        <link rel="stylesheet" type="text/css" href="styles.css">
    </head>
    <body>
        <h1>Hello from the Home page</h1>
    </body>
</html>
```

Và đây là một trang giới thiệu về công ty đứng sau website.

```html
<!DOCTYPE html>
<html>
    <head>
        <link rel="stylesheet" type="text/css" href="styles.css">
    </head>
    <body>
        <h1>Learn about our company</h1>
    </body>
</html>
```

Các ví dụ này chỉ là một lượng nhỏ HTML, nhưng nếu bạn được yêu cầu đổi stylesheet từ `styles.css` sang một stylesheet mới do designer tạo tên là `better_styles.css` thì sao? Bạn sẽ phải cập nhật ở cả hai nơi. Bây giờ hãy tưởng tượng nếu có 2.000 trang thay vì 2 trang. Việc thay đổi lớn trên toàn site sẽ gần như không thể!

Django giúp bạn tránh hoàn toàn tình huống này với một vài tag. Hãy tạo một template mới tên là `base.html`.

```django
<!DOCTYPE html>
<html>
    <head>
        <link rel="stylesheet" type="text/css" href="styles.css">
    </head>
    <body>
        {% block main %}{% endblock %}
    </body>
</html>
```

Chúng ta đã tạo một template có thể tái sử dụng với tag `block`! Chúng ta có thể sửa lại trang chủ để dùng template mới này.

```django
{% extends "base.html" %}

{% block main %}
    <h1>Hello from the Home page</h1>
{% endblock %}
```

Phiên bản mới này của trang chủ _kế thừa_ template base. Tất cả những gì template cần làm là định nghĩa lại block `main` để điền nội dung. Chúng ta có thể làm tương tự với trang giới thiệu.

Nếu quay lại nhiệm vụ thay thế `styles.css` bằng `better_styles.css`, chúng ta chỉ cần cập nhật trong `base.html` và thay đổi đó sẽ áp dụng cho mọi template kế thừa nó. Dù có 2.000 trang đều kế thừa từ `base.html`, việc đổi stylesheet cũng chỉ là một dòng mã cho toàn bộ site.

Đó là sức mạnh của hệ thống mở rộng template của Django. Dùng `extend` khi bạn cần nội dung gần như giống nhau. Thêm block bất cứ khi nào bạn cần tùy biến một trang mở rộng. Bạn có thể mở rộng một trang bằng nhiều loại block khác nhau. Ví dụ chỉ có block `main`, nhưng bạn có thể có các trang tùy biến `sidebar`, `header`, `footer` hoặc bất cứ phần nào có thể thay đổi.

Một công cụ mạnh khác để tái sử dụng là tag `include`. Tag `include` hữu ích khi bạn muốn tách một phần template để dùng ở nhiều nơi khác nhau. Bạn có thể dùng `include` để:

1. Giữ template gọn gàng. Bạn có thể chia một template lớn thành các phần nhỏ dễ quản lý hơn.
2. Dùng một đoạn template ở nhiều vị trí khác nhau trên site. Có thể bạn có một phần template chỉ xuất hiện ở một số trang.

Quay lại ví dụ website, hãy tưởng tượng `base.html` dài tới 20.000 dòng. Việc tìm đúng phần template để sửa sẽ khó hơn. Chúng ta có thể chia nhỏ template thành các phần nhỏ hơn.

```django
<!DOCTYPE html>
<html>
    {% include "head.html" %}
    <body>
        {% include "navigation.html" %}
        {% block main %}{% endblock %}
    </body>
    {% include "footer.html" %}
</html>
```

Tag `include` có thể di chuyển các phần phụ đó. Nếu bạn đặt tên template hợp lý, khi cần thay đổi cấu trúc một phần như navigation, bạn chỉ cần vào đúng file template đó. File template đó sẽ chỉ tập trung vào phần tử bạn cần thay đổi.

`block`, `extends` và `include` là các tag cốt lõi giúp bạn tránh việc mã giao diện người dùng bị tràn lan với nhiều sự lặp lại.

Tiếp theo, hãy nói về nhiều tag template tích hợp sẵn của Django có thể tăng sức mạnh cho UI của bạn.

## Hộp Công Cụ Template

Tài liệu Django có một [danh sách lớn các tag tích hợp sẵn](https://docs.djangoproject.com/en/4.1/ref/templates/builtins/) mà bạn có thể dùng trong dự án của mình. Chúng ta sẽ không đề cập hết tất cả, nhưng tôi sẽ tập trung vào một số tag để bạn cảm nhận được những gì có sẵn.

Một trong những tag tích hợp sẵn được dùng nhiều nhất ngoài những gì đã đề cập là tag `url`. Nhớ lại từ bài viết về URL rằng bạn có thể lấy URL tới một view đã đặt tên bằng hàm `reverse`. Nếu bạn muốn dùng URL đó trong template thì sao? Bạn có thể làm như sau:

```python
# application/views.py

from django.shortcuts import render
from django.urls import reverse

def the_view(request):
    context = {
        'the_url': reverse('a_named_view')
    }
    return render(
        request,
        'a_template.html',
        context
    )
```

Cách này hoạt động, nhưng thật phiền khi phải truyền mọi URL qua context. Thay vào đó, template của chúng ta có thể tự tạo URL phù hợp. Đây là ví dụ về `a_template.html`:

```django
<a href="{% url "a_named_view" %}">Go to a named view</a>
```

Tag `url` là tương đương với hàm `reverse` trong template. Giống như `reverse`, `url` có thể nhận args hoặc kwargs cho các route cần biến khác. `url` là một công cụ cực kỳ hữu ích mà bạn sẽ dùng rất nhiều khi xây dựng giao diện người dùng.

Một tag hữu ích khác là tag `now`. `now` là một cách tiện lợi để hiển thị thông tin về thời gian hiện tại. Sử dụng cái mà Django gọi là _format specifier_, bạn có thể nói cho template biết cách hiển thị thời gian hiện tại. Muốn thêm năm bản quyền hiện tại vào website? Không vấn đề!

```django
&copy; {% now "Y" %} Your Company LLC.
```

Một tag tích hợp cuối cùng cần xem xét là tag `spaceless`. HTML _phần nào_ nhạy cảm với khoảng trắng. Có những trường hợp khó chịu mà sự nhạy cảm này có thể phá hỏng ngày làm việc của bạn khi xây dựng giao diện người dùng. Bạn có thể tạo một menu điều hướng hoàn hảo bằng danh sách không thứ tự không? Có thể. Hãy xem ví dụ này:

```html
<ul class="navigation">
    <li><a href="/home/">Home</a></li>
    <li><a href="/about/">About</a></li>
</ul>
```

Khoảng trắng thụt vào ở các phần tử list (hoặc ký tự xuống dòng sau chúng) có thể gây rắc rối khi làm việc với CSS. Biết rằng khoảng trắng có thể ảnh hưởng đến layout, chúng ta có thể dùng `spaceless` như sau:

```django
{% spaceless %}
<ul class="navigation">
    <li><a href="/home/">Home</a></li>
    <li><a href="/about/">About</a></li>
</ul>
{% endspaceless %}
```

Tag template nhỏ gọn này sẽ loại bỏ mọi khoảng trắng giữa các thẻ HTML để đầu ra của bạn trông như:

```html
<ul class="navigation"><li><a href="/home/">Home</a></li>...</ul>
```

Bằng cách loại bỏ khoảng trắng thừa, bạn có thể có trải nghiệm nhất quán hơn với CSS và tránh được nhiều phiền toái. (Tôi đã cắt bớt đầu ra để vừa với màn hình.)

Có một loại tích hợp khác mà chúng ta chưa xem. Các hàm tích hợp thay thế này được gọi là **filter**. Filter thay đổi đầu ra của biến trong template. Cú pháp filter hơi đặc biệt. Nó trông như sau:

```django
Here's a filter example: {{ a_variable|some_filter:"filter arguments" }}
```

Điểm quan trọng là ký tự pipe ngay sau biến. Ký tự này báo cho hệ thống template rằng chúng ta muốn biến đổi biến bằng một kiểu chuyển đổi nào đó. Lưu ý rằng filter được dùng trong dấu ngoặc nhọn kép thay vì cú pháp `{%` như tag.

Một filter rất phổ biến là filter `date`. Khi bạn truyền một instance `datetime` của Python vào context, bạn có thể dùng filter `date` để kiểm soát định dạng của datetime. [Tài liệu về date](https://docs.djangoproject.com/en/4.1/ref/templates/builtins/#date) cho biết các tùy chọn bạn có thể dùng để thay đổi định dạng.

```django
{{ a_datetime|date:"Y-m-d" }}
```

Nếu `a_datetime` là ngày Cá tháng Tư, nó có thể trả về chuỗi như `2020-04-01`. Filter `date` có nhiều định dạng giúp bạn tạo ra hầu hết các kiểu định dạng ngày tháng mà bạn nghĩ ra.

`default` là một filter hữu ích khi giá trị template của bạn là `False`. Điều này rất phù hợp khi bạn có một biến là chuỗi rỗng. Ví dụ dưới đây sẽ xuất ra “Nothing to see here” nếu biến là Falsy.

```django
{{ a_variable|default:"Nothing to see here." }}
```

Falsy là một khái niệm trong Python mô tả bất cứ thứ gì mà Python đánh giá là false trong biểu thức boolean. Chuỗi rỗng, list rỗng, dict rỗng, set rỗng, `False` và `None` đều là các giá trị Falsy phổ biến.

`length` là một filter đơn giản cho list. `{{ a_list_variable|length }}` sẽ trả về một số. Đây là tương đương với hàm `len` trong template Django.

Tôi rất thích filter `linebreaks`. Nếu bạn tạo một form (chúng ta sẽ tìm hiểu ở bài sau) và chấp nhận một trường textarea cho phép người dùng nhập xuống dòng, thì filter `linebreaks` cho phép bạn hiển thị các dòng mới đó khi render dữ liệu của người dùng. Mặc định, HTML sẽ không hiển thị ký tự xuống dòng như mong muốn. Filter `linebreaks` sẽ chuyển `\n` thành thẻ HTML `<br>`. Rất tiện!

Trước khi tiếp tục, hãy xem thêm hai filter nữa.

`pluralize` là một filter tiện lợi khi văn bản của bạn liên quan đến số lượng. Hãy xem ví dụ đếm số phần tử.

```django
{{ count_items }} item{{ count_items|pluralize }}
```

Filter `pluralize` sẽ làm đúng nếu có 0, 1 hoặc nhiều phần tử trong list.

```
0 items
1 item
2 items
3 items
(và cứ thế)
```

Lưu ý rằng `pluralize` không xử lý được các danh từ số nhiều bất quy tắc như “mice” cho “mouse”.

Filter cuối cùng trong chuyến tham quan này là filter `yesno`. `yesno` tốt cho việc chuyển đổi `True|False|None` thành thông điệp văn bản có ý nghĩa. Hãy tưởng tượng chúng ta đang làm một ứng dụng theo dõi sự kiện và trạng thái tham dự của một người là một trong ba giá trị đó. Template của chúng ta có thể như sau:

```django
{{ user.name }} has {{ user_accepted|yesno:"accepted,declined,not RSVPed" }}.
```

Tùy vào giá trị của `user_accepted`, template sẽ hiển thị thông điệp phù hợp cho người đọc.

Có rất nhiều filter tích hợp đến mức thật khó để chọn ra filter yêu thích. Hãy xem danh sách đầy đủ để tìm ra filter phù hợp với bạn.

Nếu các filter tích hợp không đáp ứng được nhu cầu của bạn thì sao? Đừng lo, Django cho phép bạn tạo tag và filter tùy chỉnh cho mục đích riêng. Chúng ta sẽ xem cách làm ngay sau đây.

## Tự Xây Dựng "Lightsaber" Trong Template

Khi bạn cần xây dựng tag hoặc filter template riêng, Django cung cấp cho bạn công cụ để làm điều đó.

Có ba yếu tố chính khi làm việc với tag tùy chỉnh:

1. Định nghĩa tag ở nơi Django mong đợi.
2. Đăng ký tag với engine template.
3. Load tag trong template để có thể sử dụng.

Bước đầu tiên là đặt tag vào đúng vị trí. Để làm điều đó, chúng ta cần một package Python tên là `templatetags` bên trong một ứng dụng Django. Chúng ta cũng cần một module trong thư mục đó. Hãy chọn tên module cẩn thận vì đó là cái chúng ta sẽ load trong template sau này.

```
application
├── templatetags
│   ├── __init__.py
│   └── custom_tags.py
├── __init__.py
├── ...
├── models.py
└── views.py
```

Tiếp theo, chúng ta cần tạo tag hoặc filter và đăng ký nó. Hãy bắt đầu với ví dụ về filter.

```python
# application/templatetags/custom_tags.py

import random
from django import template

register = template.Library()

@register.filter
def add_pizzazz(value):
    pieces_of_flair = [
        ' Amazing!',
        ' Wowza!',
        ' Unbelievable!'
    ]
    return value + random.choice(pieces_of_flair)
```

Bây giờ, nếu chúng ta có một biến `message`, chúng ta có thể thêm chút "pizzazz" cho nó. Để dùng filter tùy chỉnh, chúng ta phải load module tag vào template với tag `load`.

```django
{% load custom_tags %}

{{ message|add_pizzazz }}
```

Nếu message của chúng ta là “You got a perfect score!”, thì template sẽ hiển thị thông điệp đó kèm một trong ba lựa chọn ngẫu nhiên như “You got a perfect score! Wowza!”

Việc viết tag tùy chỉnh cơ bản rất giống với filter tùy chỉnh. Mã sẽ nói lên tất cả.

```python
# application/templatetags/custom_tags.py

import random
from django import template

register = template.Library()

@register.simple_tag
def champion_welcome(name, level):
    if level > 42:
        welcome = f"Hello great champion {name}!"
    elif level > 20:
        welcome = f"Greetings noble warrior {name}!"
    elif level > 5:
        welcome = f"Hello {name}."
    else:
        welcome = "Oh, it's you."
    return welcome
```

Chúng ta có thể load tag tùy chỉnh và dùng tag này như bất kỳ tag tích hợp nào khác.

```django
{% load custom_tags %}

{% champion_welcome "He-Man" 50 %}
```

Tag chào mừng vui nhộn này sẽ phản hồi theo nhiều biến đầu vào và thay đổi tùy vào level truyền vào. Ví dụ trên sẽ hiển thị “Hello great champion He-Man!”

Chúng ta chỉ đang xem các loại tag tùy chỉnh phổ biến nhất trong ví dụ. Có một số tính năng tag tùy chỉnh nâng cao hơn mà bạn có thể khám phá trong [tài liệu về custom template tags của Django](https://docs.djangoproject.com/en/4.1/howto/custom-template-tags/).

Django cũng sử dụng `load` để cung cấp cho tác giả template một số công cụ bổ sung. Ví dụ, chúng ta sẽ thấy cách load một số thẻ tùy chỉnh do framework cung cấp khi chúng ta học về làm việc với hình ảnh và JavaScript ở các phần sau.

## Tóm tắt

Bây giờ chúng ta đã thấy template hoạt động thực tế! Chúng ta đã xem qua:

- Cách thiết lập template cho trang web của bạn
- Các cách gọi template từ view
- Cách sử dụng dữ liệu
- Cách xử lý logic
- Các thẻ và filter tích hợp sẵn dành cho template
- Tùy biến template với phần mở rộng mã của riêng bạn

Trong bài tiếp theo, chúng ta sẽ xem xét cách người dùng có thể gửi dữ liệu đến một ứng dụng Django với các form HTML. Django có các công cụ giúp xây dựng form nhanh chóng và hiệu quả. Chúng ta sẽ tìm hiểu:

- Lớp `Form` mà Django sử dụng để xử lý dữ liệu form trong Python
- Kiểm soát các trường có trong form
- Cách Django hiển thị form cho người dùng
- Cách thực hiện kiểm tra hợp lệ cho form
