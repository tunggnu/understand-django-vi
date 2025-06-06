# Quản Trị Mọi Thứ

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã sử dụng models để xem cách Django lưu trữ dữ liệu trong một cơ sở dữ liệu quan hệ. Chúng ta đã đề cập đến tất cả các công cụ để đưa dữ liệu của bạn vào hoạt động trong ứng dụng. Trong bài viết này, chúng ta sẽ tập trung vào các công cụ tích hợp sẵn mà Django cung cấp để giúp chúng ta quản lý dữ liệu đó.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. Quản Trị Mọi Thứ
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

## Django Admin Là Gì?

Khi bạn vận hành một ứng dụng, bạn sẽ thấy có những dữ liệu cần được chú ý đặc biệt. Có thể bạn đang tạo một blog và cần tạo hoặc chỉnh sửa các thẻ hoặc danh mục. Có thể bạn có một cửa hàng trực tuyến và cần quản lý hàng tồn kho. Dù bạn xây dựng gì đi nữa, bạn có lẽ sẽ phải quản lý _một thứ gì đó_.

Làm thế nào để bạn quản lý dữ liệu đó?

- Nếu bạn là lập trình viên, bạn có thể đăng nhập vào máy chủ, mở shell quản lý của Django và làm việc trực tiếp với dữ liệu bằng Python.
- Nếu bạn không phải lập trình viên, ừm, có lẽ bạn không còn cách nào! **Không, điều đó không đúng!**

Django bao gồm một giao diện quản trị web có thể giúp cả lập trình viên lẫn người không biết lập trình. Giao diện quản trị này thường được gọi tắt là Django admin.

Giống như nhiều phần mở rộng khác trong hệ sinh thái Django, trang admin là một ứng dụng Django. Trang này được sử dụng phổ biến đến mức nó đã được cấu hình sẵn khi bạn chạy lệnh `startproject`.

Trước khi tiếp tục, tôi muốn lưu ý một vấn đề bảo mật. Khi sử dụng `startproject`, Django sẽ đặt trang admin ở địa chỉ `/admin/` theo mặc định. **Hãy thay đổi điều này**. Mẫu khởi tạo giúp bạn thiết lập trang admin một cách tiện lợi, nhưng URL mặc định này khiến cho [script kiddies](https://en.wikipedia.org/wiki/Script_kiddie) dễ dàng tấn công trang admin của bạn để cố gắng truy cập trái phép. Đặt trang admin ở một URL khác _không_ hoàn toàn bảo vệ trang của bạn (bạn không nên dựa vào “bảo mật nhờ che giấu”), nhưng nó sẽ giúp tránh được nhiều cuộc tấn công tự động.

Django admin cho phép bạn nhanh chóng tương tác với các model của mình. Như bạn sẽ thấy ngay sau đây, bạn có thể đăng ký một model với trang admin. Khi model đã được đăng ký, bạn có thể sử dụng giao diện trang admin để thực hiện các thao tác CRUD trên dữ liệu.

CRUD là một từ viết tắt mô tả các chức năng chính của nhiều trang web. CRUD là viết tắt của:

- **Create** - Một trang web có thể tạo dữ liệu (tức là chèn dữ liệu vào cơ sở dữ liệu)
- **Read** - Người dùng có thể xem dữ liệu
- **Update** - Dữ liệu có thể được cập nhật bởi người dùng
- **Delete** - Người dùng có thể xóa dữ liệu khỏi hệ thống

Nếu bạn nghĩ về các hành động mà bạn thực hiện trên một trang web, hầu hết các hành động sẽ thuộc một trong bốn loại trên.

Trang admin cung cấp các công cụ để thực hiện tất cả các thao tác đó. Có một vài trang chính mà bạn có thể điều hướng khi làm việc với trang admin của Django, nơi các thao tác CRUD diễn ra. Những trang này có sẵn cho bạn mà bạn chỉ cần thực hiện rất ít thao tác ngoài việc đăng ký mà bạn sẽ thấy ở phần tiếp theo.

1. Trang chỉ mục admin - Trang này sẽ hiển thị tất cả các model, được nhóm theo ứng dụng Django mà chúng xuất phát, đã được đăng ký với admin.
2. Trang danh sách model - Trang danh sách hiển thị các dòng dữ liệu từ một model (tức là một bảng cơ sở dữ liệu). Từ trang này, quản trị viên có thể thực hiện các hành động trên nhiều bản ghi cơ sở dữ liệu như xóa một loạt bản ghi chỉ với một thao tác.
3. Trang thêm model - Admin cung cấp một trang nơi bạn có thể tạo các instance model mới bằng các form được sinh tự động dựa trên các trường của model.
4. Trang chỉnh sửa model - Trang chỉnh sửa cho phép bạn cập nhật một instance model hiện có (tức là một dòng trong bảng cơ sở dữ liệu). Từ trang này, bạn cũng có thể xóa một instance model.

Nếu bạn xem xét bộ trang nhỏ này, bạn sẽ nhận ra rằng mọi phần của từ viết tắt CRUD đều có thể thực hiện trong trang admin này. Quyền tạo và xóa nằm trong tay bạn. 😈

Bây giờ chúng ta đã hiểu những gì có trong trang admin, hãy tập trung vào cách thêm model của bạn vào admin.

## Đăng Ký Model Với Admin

Để trang admin hiển thị dữ liệu model của bạn, chúng ta cần cập nhật file `admin.py`. Ở một ứng dụng mới tạo bằng `startapp`, bạn sẽ thấy file `admin.py` hầu như trống rỗng. Chúng ta cần thêm một chút “kết dính” để admin biết về một model.

Trang admin mong đợi một class `ModelAdmin` cho mỗi model mà bạn muốn hiển thị trong trang.

Hãy xem xét một ví dụ đơn giản về model hóa một cuốn sách.

```python
# application/models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(
        max_length=256
    )
    author = models.CharField(
        max_length=256
    )
```

Bây giờ chúng ta có thể tạo một class `ModelAdmin` cho model `Book`.

```python
# application/admin.py
from django.contrib import admin

from .models import Book

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    pass
```

Có một vài điểm quan trọng cần chú ý với file `admin.py` này.

1. `BookAdmin` là một lớp con của `admin.ModelAdmin`.
2. `BookAdmin` được đăng ký với trang admin bằng decorator `admin.register`.

Bạn cũng có thể đăng ký một class admin bằng cách gọi `register` sau khi định nghĩa class nếu không muốn dùng decorator.

```python
# application/admin.py
from django.contrib import admin

from .models import Book

class BookAdmin(admin.ModelAdmin):
    pass

admin.site.register(Book, BookAdmin)
```

Bây giờ chúng ta đã có một model được đăng ký với trang admin, làm sao để xem nó? Hãy khởi động server phát triển với `runserver` và truy cập vào URL mà trước đây là `/admin/` (bởi vì bạn đã đổi sang một địa chỉ khác ngoài `/admin/`, đúng không? Đúng chứ!?).

Trên trang này, bạn sẽ gặp một màn hình đăng nhập. Chúng ta chưa tìm hiểu hệ thống xác thực, nhưng hiện tại bạn chỉ cần biết rằng chỉ những tài khoản người dùng có quyền staff mới có thể đăng nhập.

Django cung cấp một lệnh cho phép chúng ta tạo tài khoản người dùng có quyền staff và tất cả các quyền khác. Giống như hệ điều hành Linux, tài khoản người dùng có mọi quyền được gọi là superuser. Bạn có thể tạo tài khoản superuser với lệnh `createsuperuser`.

```shell
$ ./manage.py createsuperuser
Username: matt
Email address: matt@somewhere.com
Password:
Password (again):
Superuser created successfully.
```

Khi đã có tài khoản superuser, bạn đã sẵn sàng đăng nhập vào trang admin. Vì bạn sử dụng tài khoản superuser, bạn sẽ có quyền xem mọi model đã được đăng ký với trang admin.

Sau khi đăng nhập, bạn có thể xem trang admin của model `Book`. Hãy thử tạo một cuốn sách với nút “Add Book”. Xem trang danh sách. Chỉnh sửa sách. Xóa sách. Bạn có thể thấy chỉ với một lượng công việc rất nhỏ, Django đã cung cấp cho bạn một giao diện CRUD đầy đủ để tương tác với model của mình.

Chúng ta vừa thêm một `ModelAdmin` đơn giản nhất có thể. Thân class chỉ có `pass` mà không có thuộc tính nào. Django cung cấp cho chúng ta rất nhiều tùy chọn để kiểm soát cách các trang admin cho `Book` hoạt động. Hãy cùng điểm qua một số thuộc tính admin thường dùng.

## Tùy Biến Trang Admin Của Bạn

Giống như nhiều phần khác của Django, framework sử dụng các thuộc tính cấp class để xác định hành vi của một class. Khác với forms và models, nơi các thuộc tính cấp class chủ yếu là các trường mà bạn tự định nghĩa, các class `ModelAdmin` cung cấp giá trị cho các thuộc tính đã được định nghĩa rõ trong tài liệu. Các thuộc tính này đóng vai trò như các “hook” cho phép bạn tùy biến hành vi của các trang admin.

Việc tạo ra các trang admin hiệu quả chủ yếu là sử dụng các thuộc tính này để class `ModelAdmin` hoạt động như bạn muốn. Do đó, làm chủ trang admin Django là làm chủ các tùy chọn `ModelAdmin` được liệt kê [trong tài liệu](https://docs.djangoproject.com/en/4.1/ref/contrib/admin/#modeladmin-options). Danh sách này khá dài, nhưng đừng nản chí! Tôi nghĩ bạn có thể tận dụng khoảng 80% giá trị của Django admin chỉ với một số ít tùy chọn.

Khi bạn thử nghịch các trang `Book`, có lẽ bạn nhận thấy danh sách sách khá đơn điệu. Danh sách mặc định trông giống như một loạt liên kết hiển thị `Book object (#)`. Chúng ta có thể thay đổi giao diện và tính hữu dụng của trang này với một vài thiết lập khác nhau.

Hãy bắt đầu với `list_display`. Thuộc tính `ModelAdmin` này kiểm soát những trường nào sẽ xuất hiện trên trang danh sách. Với ví dụ model sách, chúng ta có thể thêm trường tiêu đề vào trang.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ('id', 'title')
```

Django sẽ biến trường được liệt kê đầu tiên thành liên kết mà người dùng có thể nhấp để xem trang chi tiết admin cho bản ghi model. Trong ví dụ này, tôi dùng trường `id` làm liên kết, nhưng tôi cũng có thể dùng tuple chỉ có một phần tử `('title',)` để trang chỉ hiển thị tiêu đề và tiêu đề là liên kết.

Đôi khi bạn sẽ có loại model mà bạn chỉ muốn xem một tập con các bản ghi. Giả sử model `Book` có thêm trường category.

```python
# application/models.py

class Book(models.Model):
    class Category(
        models.IntegerChoices
    ):
        SCI_FI = 1
        FANTASY = 2
        MYSTERY = 3
        NON_FICTION = 4

    # ... title và author như trước

    category = models.IntegerField(
        choices=Category.choices,
        default=Category.SCI_FI
    )
```

Bằng cách sử dụng thuộc tính `list_filter`, chúng ta có thể cho phép trang danh sách admin lọc theo category mà mình muốn.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ('id', 'title')
    list_filter = ('category',)
```

Tùy chọn này sẽ đặt một thanh bên ở phía bên phải trang admin. Trong thanh bên đó, bạn sẽ thấy các category mà tôi đã đưa vào class choices `Category`. Nếu tôi nhấp vào liên kết “Fantasy”, trình duyệt sẽ chuyển đến `/admin/application/book/?category__exact=2` và chỉ hiển thị các dòng dữ liệu có category phù hợp.

Đây không phải là kiểu lọc duy nhất mà admin có thể làm. Chúng ta cũng có thể lọc theo thời gian với trường `date_hierarchy`. Tiếp theo, hãy thêm trường `published_date` cho model.

```python
# application/models.py

class Book(models.Model):
    # ... title, author, category

    published_date = models.DateField(
        default=datetime.date.today
    )
```

Chúng ta cũng có thể thay đổi `ModelAdmin` để sử dụng trường mới này.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    date_hierarchy = "published_date"
    list_display = ("id", "title")
    list_filter = ("category",)
```

Bằng cách thêm thuộc tính `date_hierarchy`, trang danh sách sẽ có thêm một số thành phần giao diện mới. Ở phía trên cùng trang sẽ có các bộ chọn giúp lọc theo khoảng thời gian mong muốn. Đây là một cách rất hữu ích để duyệt qua bảng dữ liệu của bạn.

Chúng ta vẫn có thể đi xa hơn nữa. Có thể bạn muốn tất cả sách được sắp xếp theo tiêu đề. Ngay cả khi thuộc tính `ordering` không được đặt trong meta options của model, `ModelAdmin` cũng có thuộc tính `ordering` riêng.

_“Meta” là gì?_ Ngoài các trường, một model Django có thể thiết lập thông tin bổ sung về cách xử lý dữ liệu. Các tùy chọn bổ sung này là các thuộc tính “meta” của model. Một model Django thêm thông tin meta bằng cách khai báo một class lồng `Meta` trong model. Xem [Model Meta options](https://docs.djangoproject.com/en/4.1/ref/models/options/) để biết thêm các tính năng tùy biến hành vi model.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    date_hierarchy = "published_date"
    list_display = ("id", "title")
    list_filter = ("category",)
    ordering = ("title",)
```

Với thiết lập này, tất cả sách trên trang sẽ được sắp xếp theo tiêu đề. Thuộc tính `ordering` sẽ thêm một mệnh đề `ORDER BY` phù hợp vào truy vấn cơ sở dữ liệu thông qua `QuerySet` do admin sinh ra.

Tùy chọn cuối cùng tiện lợi cho trang danh sách mà tôi muốn nhấn mạnh là `search_fields`.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    date_hierarchy = "published_date"
    list_display = ("id", "title")
    list_filter = ("category",)
    ordering = ("title",)
    search_fields = ("author",)
```

Với tùy chọn này, trang danh sách sẽ có thêm một thanh tìm kiếm ở phía trên. Trong ví dụ này, tôi thêm khả năng tìm kiếm dựa trên tác giả của sách.

Khi bạn tìm kiếm, URL kết quả có thể trông như `/admin/application/book/?q=tolkien`. Django sẽ thực hiện tìm kiếm không phân biệt hoa thường trên trường đó. `QuerySet` sẽ giống như:

```python
search_results = Book.objects.filter(
    author__icontains="tolkien"
)
```

Kết quả này sẽ không thể cạnh tranh với một công cụ tìm kiếm chuyên dụng, nhưng có được một tính năng tìm kiếm ổn chỉ với một dòng mã là rất tuyệt!

`ModelAdmin` cũng bao gồm một số thiết lập hữu ích để thay đổi hành vi của trang chi tiết của từng bản ghi cơ sở dữ liệu.

Ví dụ, giả sử model `Book` có một trường `ForeignKey` để theo dõi biên tập viên.

```python
# application/models.py
from django.contrib.auth.models import User

class Book(models.Model):
    # ... title, author, category
    # published_date như trước

    editor = models.ForeignKey(
        User,
        null=True,
        blank=True,
        on_delete=models.CASCADE
    )
```

Trên trang admin của một cuốn sách cụ thể, trường `editor` sẽ mặc định là một dropdown. Trường này sẽ bao gồm mọi bản ghi `User` trong ứng dụng của bạn. Nếu bạn có một trang phổ biến với hàng ngàn hoặc hàng triệu người dùng, trang này sẽ bị “nghẽn” khi tải tất cả các bản ghi user vào dropdown đó.

Thay vì có một trang vô dụng không thể tải nổi, bạn có thể dùng `raw_id_fields`.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    date_hierarchy = "published_date"
    list_display = ("id", "title")
    list_filter = ("category",)
    ordering = ("title",)
    raw_id_fields = ("editor",)
    search_fields = ("author",)
```

Bằng cách sử dụng `raw_id_fields`, admin sẽ chuyển từ dropdown sang một ô nhập liệu cơ bản hiển thị khóa ngoại của bản ghi user. Việc nhìn thấy một số khóa ngoại thì không trực quan bằng việc thấy tên thực tế trong dropdown, nhưng tùy chọn `raw_id_fields` bổ sung hai tính năng để cải thiện điều này.

1. Có một biểu tượng tìm kiếm. Nếu người dùng nhấp vào biểu tượng này, một cửa sổ popup sẽ xuất hiện cho phép người dùng tìm kiếm bản ghi trong một giao diện chọn riêng.
2. Nếu bản ghi đã có khóa ngoại cho trường này, thì chuỗi biểu diễn của bản ghi sẽ hiển thị bên cạnh biểu tượng.

Một tùy chọn khác cũng hữu ích là `prepopulated_fields`. Ở phần bàn về URLs, chúng ta đã nói về trường slug. Slug thường được dùng để tạo URL thân thiện cho trang chi tiết hiển thị một instance model. Hãy thêm trường `SlugField` vào model `Book`.

```python
# application/models.py

class Book(models.Model):
    # ... title, author, category
    # published_date, editor như trước

    slug = models.SlugField()
```

Lợi ích của `prepopulated_fields` là gì? Bằng cách sử dụng tùy chọn này, chúng ta có thể hướng dẫn trang admin tự động điền trường `slug` dựa trên trường `title` của sách. Đây là cập nhật cho `ModelAdmin`.

```python
# application/admin.py

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    date_hierarchy = "published_date"
    list_display = ("id", "title")
    list_filter = ("category",)
    ordering = ("title",)
    prepopulated_fields = {"slug": ("title",)}
    raw_id_fields = ("editor",)
    search_fields = ("author",)
```

Bây giờ khi bạn muốn thêm một cuốn sách mới trong admin, Django sẽ sử dụng JavaScript để cập nhật trường slug động khi bạn gõ tiêu đề!

Đến đây, mọi thuộc tính mà chúng ta thêm vào admin đều là cấu hình tĩnh. Nếu bạn muốn thay đổi hành vi các trang admin dựa trên điều gì đó động thì sao?

May mắn thay, nhóm phát triển Django cũng đã nghĩ đến điều này. Tất cả các tùy chọn mà chúng ta vừa xem đều có một phương thức tương ứng mà bạn có thể override, được bắt đầu bằng tiền tố `get_`. Ví dụ, nếu bạn muốn kiểm soát các trường mà người dùng nhìn thấy trên trang danh sách dựa trên họ là ai, bạn sẽ cài đặt `get_list_display`. Trong phương thức đó, bạn sẽ trả về một tuple dựa trên quyền truy cập của người dùng.

```python
# application/admin.py
from django.contrib import admin

from .models import Book

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    ...

    def get_list_display(self, request):
        if request.user.is_superuser:
            return (
                'id',
                'title',
                'author',
                'category',
            )

        return ('id', 'title')
```

Một thuộc tính cuối cùng cần xem xét là `inlines`. Tôi không thường xuyên dùng tùy chọn này, nhưng nó là một cách tiện lợi để xem _các_ model khác có liên quan đến một model cụ thể.

Giả sử ứng dụng mẫu của chúng ta có các đánh giá cho sách. Chúng ta có thể thêm một model như sau:

```python
# application/models.py

class Review(models.Model):
    book = models.ForeignKey(
        Book,
        on_delete=models.CASCADE
    )
    rating = models.IntegerField()
    comment = models.TextField()
```

Để hiển thị các model khác trên trang chi tiết, chúng ta cần tạo một class inline và đưa nó vào `ModelAdmin`. Kết quả như sau:

```python
# application/admin.py
from django.contrib import admin

from .models import Book, Review

class ReviewInline(admin.TabularInline):
    model = Review

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    date_hierarchy = "published_date"
    inlines = [ReviewInline]
    list_display = ("id", "title")
    list_filter = ("category",)
    ordering = ("title",)
    raw_id_fields = ("editor",)
    prepopulated_fields = {"slug": ("title",)}
    search_fields = ("author",)
```

Bằng cách thêm class inline vào danh sách `inlines`, trang chi tiết sẽ hiển thị mọi đánh giá liên quan đến một cuốn sách. Ngoài ra, bạn cũng có thể tạo đánh giá mới ngay từ trang chi tiết vì admin sẽ tự động thêm một vài form trống.

Chúng ta đã đề cập đến nhiều tùy chọn của class `ModelAdmin` mà bạn có thể sử dụng để tùy biến trải nghiệm admin với các chức năng phổ biến mà nhiều công cụ quản trị yêu cầu. **Vậy còn các chức năng _không phổ biến_ thì sao?** Để tùy biến thêm, chúng ta có thể sử dụng các hành động admin.

## Thực Hiện Hành Động Trong Admin

Khi bạn muốn thực hiện các công việc liên quan đến các bản ghi cụ thể trong cơ sở dữ liệu, Django cung cấp một số kỹ thuật để tùy biến trang và cung cấp các khả năng đó. Các tùy biến này được gọi là _actions_ (hành động) và chúng xuất hiện trên trang danh sách phía trên danh sách bản ghi.

Trong trang admin mặc định, có một hành động cho phép quản trị viên xóa bản ghi. Nếu bạn chọn một số dòng bằng các checkbox ở bên trái, chọn “Delete selected <object type>”, rồi nhấn “Go”, bạn sẽ được chuyển đến một trang xác nhận việc xóa các dòng đã chọn.

Quy trình tương tự có thể áp dụng cho bất kỳ hành động nào bạn muốn thực hiện trên các bản ghi cơ sở dữ liệu. Chúng ta có thể làm điều này bằng cách thêm một phương thức vào `ModelAdmin`.

Phương thức này phải tuân theo giao diện sau:

```python
@admin.register(MyModel)
class MyModelAdmin(admin.ModelAdmin):
    actions = ['do_some_action']

    def do_some_action(
            self,
            request: HttpRequest,
            queryset: QuerySet
        ) -> Optional[HttpResponse]:
        # Thực hiện công việc ở đây.
        ...
```

Queryset sẽ đại diện cho tập hợp các bản ghi model mà người dùng đã chọn. Nếu phương thức trả về `None`, người dùng sẽ được trả về cùng trang admin. Nếu phương thức trả về một `HttpResponse`, người dùng sẽ thấy response đó (giống như trang xác nhận xóa của hành động xóa). Bạn có thể làm bất cứ điều gì giữa lúc phương thức được gọi và lúc nó trả về.

Có thể ứng dụng sách mẫu của chúng ta muốn đặt một cuốn sách làm sách nổi bật trên trang như một tựa sách mới quan trọng. Trong kịch bản giả định này, chúng ta có thể có mã để bỏ chọn các sách nổi bật cũ hoặc gửi email cho những người quan tâm khi có sách nổi bật mới.

Với kịch bản này, chúng ta có thể thêm một action để thực hiện các việc đó.

```python
# application/admin.py

def update_premiere(book):
    """Giả vờ cập nhật sách thành sách nổi bật.

    Hàm này chỉ để minh họa cho rõ ràng.
    Trong ứng dụng thực tế, có thể là một phương thức manager
    cập nhật sách và gửi thông báo email
    (ví dụ: `Book.objects.update_premiere(book)`).
    """
    print(f"Update {book.title} state to change premiere books.")
    print("Call some background task to notify interested users via email.")

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    actions = ["set_premiere"]
    date_hierarchy = "published_date"
    inlines = [ReviewInline]
    list_display = ("id", "title")
    list_filter = ("category",)
    ordering = ("title",)
    raw_id_fields = ("editor",)
    prepopulated_fields = {"slug": ("title",)}
    search_fields = ("author",)

    def set_premiere(
        self,
        request,
        queryset
    ):
        if len(queryset) == 1:
            book = queryset[0]
            update_premiere(book)
```

Django sẽ dùng tên phương thức để đặt nhãn cho dropdown trên trang danh sách. Trong trường hợp này, nhãn hành động sẽ là “Set premiere”.

Chúng ta đã có thể mở rộng admin và tích hợp vào giao diện người dùng của trang bằng cách định nghĩa một phương thức và khai báo nó là một action. Đây là một hệ thống mạnh mẽ để trao quyền kiểm soát cho quản trị viên và cho phép họ thao tác theo cách tùy biến trên dữ liệu trong ứng dụng.

## Tóm Tắt

Trong bài viết này, chúng ta đã xem xét trang quản trị tích hợp sẵn của Django. Phần mở rộng mạnh mẽ này cho phép chúng ta tạo, xem, chỉnh sửa và xóa các dòng khỏi các bảng cơ sở dữ liệu liên kết với các model của ứng dụng.

Chúng ta đã đề cập đến:

- Django admin là gì và cách thiết lập nó
- Cách làm cho model của bạn xuất hiện trong admin
- Cách tùy biến nhanh các trang admin với các tùy chọn do class `ModelAdmin` cung cấp
- Cách tạo các hành động bổ sung cho phép bạn thao tác trên các bản ghi model

Lần tới chúng ta sẽ tìm hiểu giải phẫu một ứng dụng Django. Một dự án Django được cấu thành từ nhiều ứng dụng. Chúng ta sẽ khám phá:

- Cấu trúc thông thường của một ứng dụng Django
- Cách Django nhận diện và tải các ứng dụng
- Tại sao các ứng dụng lại quan trọng đối với hệ sinh thái Django
