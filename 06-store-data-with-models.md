# Lưu Trữ Dữ Liệu Với Models

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã tìm hiểu về form và cách form cho phép ứng dụng của bạn nhận dữ liệu từ người dùng sử dụng trang web. Trong bài viết này, bạn sẽ thấy cách lấy dữ liệu đó và lưu nó vào cơ sở dữ liệu để ứng dụng của bạn có thể sử dụng hoặc hiển thị lại sau này.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. Lưu Trữ Dữ Liệu Với Models
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

## Thiết Lập

Hãy xác định dữ liệu của bạn sẽ đi đâu trước khi đi sâu vào cách làm việc với nó. Django sử dụng cơ sở dữ liệu để lưu trữ dữ liệu. Cụ thể hơn, Django sử dụng _cơ sở dữ liệu quan hệ_. Việc tìm hiểu về cơ sở dữ liệu quan hệ là một chủ đề rất lớn nên bạn sẽ chỉ nhận được một phiên bản **rất** rút gọn.

Một cơ sở dữ liệu quan hệ giống như một tập hợp các bảng tính. Mỗi bảng tính thực ra được gọi là một bảng (table). Một bảng có một tập các cột để lưu các phần dữ liệu khác nhau. Mỗi hàng trong bảng sẽ đại diện cho một nhóm dữ liệu liên quan. Ví dụ, hãy tưởng tượng chúng ta có một bảng nhân viên cho một công ty. Các cột của bảng nhân viên có thể bao gồm tên, họ và chức danh công việc. Mỗi hàng sẽ đại diện cho một nhân viên cụ thể.

```text
First name | Last name | Job title
-----------|-----------|----------
John       | Smith     | Software Engineer
-----------|-----------|----------
Peggy      | Jones     | Software Engineer
```

Phần “quan hệ” của cơ sở dữ liệu quan hệ xuất hiện vì nhiều bảng có thể _liên kết_ với nhau. Trong ví dụ về công ty, cơ sở dữ liệu có thể có một bảng số điện thoại để lưu số điện thoại của từng nhân viên.

Tại sao không đặt số điện thoại vào cùng bảng nhân viên? Vậy nếu một công ty cần cả số di động và số nhà thì sao? Bằng cách có các bảng riêng biệt, chúng ta có thể hỗ trợ lưu nhiều loại số điện thoại khác nhau. Có rất nhiều sức mạnh đến từ việc tách biệt các loại dữ liệu này. Chúng ta sẽ thấy sức mạnh của cơ sở dữ liệu quan hệ khi khám phá cách Django khai thác nó.

Django sử dụng cơ sở dữ liệu quan hệ nên framework phải có khả năng thiết lập cơ sở dữ liệu đó. Cấu hình cơ sở dữ liệu nằm trong thiết lập `DATABASES` trong file `settings.py`. Sau khi chạy `startproject`, bạn sẽ thấy:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

Như chúng ta đã thấy với hệ thống template, Django hỗ trợ nhiều cơ sở dữ liệu. Không giống như hệ thống template, thiết lập cơ sở dữ liệu gọi mỗi backend hỗ trợ là một “engine” thay vì “backend”. Engine cơ sở dữ liệu mặc định từ `startproject` được thiết lập để dùng [SQLite](https://www.sqlite.org/index.html). SQLite là một lựa chọn khởi đầu tuyệt vời vì nó gói toàn bộ cơ sở dữ liệu quan hệ vào một file duy nhất mà thiết lập đặt tên là `db.sqlite3`. Lựa chọn engine này thực sự giúp bạn dễ dàng bắt đầu với Django vì các lập trình viên mới không cần tải thêm công cụ nào để thử Django.

SQLite là một cơ sở dữ liệu nhỏ tuyệt vời và có lẽ là cơ sở dữ liệu được sử dụng rộng rãi nhất trên thế giới. Cơ sở dữ liệu này tồn tại trong mọi chiếc smartphone mà bạn có thể nghĩ đến. Dù SQLite rất tuyệt, nó không phù hợp với nhiều trường hợp bạn muốn dùng Django. Đầu tiên, cơ sở dữ liệu chỉ cho phép một người dùng ghi dữ liệu vào cùng lúc. Đó là một vấn đề lớn nếu bạn định xây dựng một trang phục vụ nhiều người cùng lúc.

Vì SQLite không phải là lựa chọn tốt nhất cho ứng dụng web, bạn có thể sẽ cần chuyển sang một cơ sở dữ liệu quan hệ khác. Tôi khuyên bạn nên dùng [PostgreSQL](https://www.postgresql.org/). Postgres (thường được viết tắt như vậy) là một cơ sở dữ liệu mã nguồn mở rất phổ biến và được hỗ trợ tốt. Kết hợp với [psycopg2](https://www.psycopg.org/docs/) làm engine cho Django, bạn sẽ thấy nhiều nơi có thể host ứng dụng Django của bạn sẽ hoạt động tốt với Postgres.

Chúng ta có thể tìm hiểu thêm về cấu hình cơ sở dữ liệu trong một bài viết về triển khai sau này. Hiện tại, khi bạn đang học, SQLite hoàn toàn phù hợp cho mục đích này.

## Mô Hình Hóa Dữ Liệu Của Bạn

Bây giờ bạn đã biết Django sẽ lưu dữ liệu ở đâu, hãy tập trung vào _cách_ Django lưu dữ liệu.

Django biểu diễn dữ liệu cho cơ sở dữ liệu bằng các class Python gọi là **model**. Model Django khá giống với các class form mà chúng ta đã thấy ở bài trước. Một model Django khai báo dữ liệu mà bạn muốn lưu vào cơ sở dữ liệu dưới dạng thuộc tính cấp class, giống như class form. Thực tế, các loại trường rất giống với các trường form, và điều này có lý do! Chúng ta thường muốn lưu dữ liệu từ form nên việc model có cấu trúc giống form là hợp lý. Hãy xem một ví dụ.

```python
# application/models.py
from django.db import models

class Employee(models.Model):
    first_name = models.CharField(
        max_length=100
    )
    last_name = models.CharField(
        max_length=100
    )
    job_title = models.CharField(
        max_length=200
    )
```

Class model này mô tả các phần mà chúng ta muốn đưa vào một bảng trong cơ sở dữ liệu. Mỗi class model đại diện cho một bảng trong cơ sở dữ liệu. Nếu chúng ta muốn lưu số điện thoại như đã đề cập ở trên, chúng ta sẽ tạo một class `PhoneNumber` riêng. Theo thông lệ, chúng ta dùng tên số ít thay vì số nhiều khi đặt tên class. Chúng ta làm vậy vì mỗi _hàng_ trong bảng sẽ được biểu diễn dưới dạng một instance của object.

```text
>>> from application.models import Employee
>>> employee = Employee(
...     first_name='Tom',
...     last_name='Bombadil',
...     job_title='Old Forest keeper')
>>> employee.first_name
'Tom'
```

Ví dụ này dường như tạo ra một nhân viên mới, nhưng nó thiếu một yếu tố quan trọng. Chúng ta chưa lưu instance `employee` vào cơ sở dữ liệu. Chúng ta có thể làm điều đó với `employee.save()`, nhưng nếu bạn thử gọi ngay bây giờ, nó sẽ báo lỗi rằng bảng employee chưa tồn tại.

Vì cơ sở dữ liệu là một công cụ bên ngoài Django, nó cần được chuẩn bị một chút trước khi có thể nhận dữ liệu từ Django.

## Chuẩn Bị Cơ Sở Dữ Liệu Với Migrations

Chúng ta đã biết model Django là các class Python ánh xạ tới các bảng trong cơ sở dữ liệu. Các bảng không tự nhiên xuất hiện. Chúng ta cần khả năng thiết lập các bảng để chúng khớp với cấu trúc được định nghĩa trong class Python. Công cụ mà Django cung cấp để đồng bộ model Django và cơ sở dữ liệu gọi là hệ thống migration.

Migration là các file Python mô tả chuỗi các thao tác trên cơ sở dữ liệu cần thiết để làm cho cơ sở dữ liệu khớp với bất kỳ định nghĩa model nào bạn có trong dự án.

Vì Django làm việc với nhiều loại cơ sở dữ liệu, các thao tác này được định nghĩa trong các file Python để có thể trừu tượng hóa. Bằng cách dùng các thao tác trừu tượng, hệ thống migration của Django có thể cắm vào các lệnh cơ sở dữ liệu cụ thể cho bất kỳ loại cơ sở dữ liệu nào bạn đang dùng. Nếu bạn bắt đầu với SQLite, rồi chuyển sang PostgreSQL khi triển khai ứng dụng lên internet, hệ thống migration sẽ cố gắng làm mượt sự khác biệt để bạn không phải làm nhiều việc khi chuyển đổi.

Ban đầu, bạn có thể tiến khá xa mà không cần hiểu nội dung bên trong các file migration. Ở mức cơ bản, bạn chỉ cần biết một vài lệnh Django: `makemigrations` và `migrate`.

### `makemigrations`

Lệnh `makemigrations` sẽ tạo các file migration nếu có thay đổi model đang chờ xử lý. Để tạo file migration cho model `Employee`, chúng ta có thể chạy:

```shell
(venv) $ ./manage.py makemigrations
Migrations for 'application':
  application/migrations/0001_initial.py
    - Create model Employee
```

Điều quan trọng cần lưu ý là chúng ta cần migration mới mỗi khi thay đổi model làm thay đổi các trường. Điều này bao gồm:

- Thêm model mới hoặc trường mới
- Sửa đổi trường hiện có
- Xóa trường hiện có
- Thay đổi một số metadata của model và một vài trường hợp đặc biệt khác

Nếu bạn không tạo migration, bạn có thể gặp lỗi khi truy vấn dữ liệu từ cơ sở dữ liệu. Điều này là do Django chỉ xây dựng truy vấn dựa trên những gì được định nghĩa trong mã Python. Hệ thống giả định rằng cơ sở dữ liệu đã ở trạng thái đúng. Django sẽ cố truy vấn các bảng ngay cả khi chúng chưa tồn tại!

### `migrate`

Lệnh còn lại, `migrate`, sẽ lấy các file migration và áp dụng chúng vào cơ sở dữ liệu. Ví dụ:

```shell
(venv) $ ./manage.py migrate
Operations to perform:
  Apply all migrations: admin, application, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying application.0001_initial... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK
```

Đây là kết quả khi áp dụng migration mới. Tất cả những thứ này là gì vậy!?

Hệ thống migration cũng được dùng bởi các ứng dụng tích hợp sẵn của Django. Trong dự án mẫu của tôi, tôi dùng `startproject` nên có một tập hợp các ứng dụng phổ biến trong danh sách `INSTALLED_APPS`. Chúng ta có thể thấy ứng dụng mẫu `application` đã áp dụng migration của nó, và các migration từ các ứng dụng Django khác cũng được áp dụng.

Nếu bạn chạy lại lệnh `migrate`, bạn sẽ không thấy kết quả như trên nữa. Đó là vì Django theo dõi migration nào đã được áp dụng. Hệ thống migration chỉ thực thi các migration _chưa áp dụng_.

Bạn cũng có thể giới hạn migration cho một ứng dụng Django cụ thể.

```shell
(venv) $ ./manage.py migrate application
```

Đó là những kiến thức cơ bản về migration. Bạn cũng có thể dùng migration để thực hiện các thao tác phức tạp hơn như các hành động đặc thù cho cơ sở dữ liệu bạn chọn. Bạn có thể tìm hiểu thêm về migration trong [tài liệu Migrations](https://docs.djangoproject.com/en/4.1/topics/migrations/).

## Làm Việc Với Models

Sau khi chạy migration, cơ sở dữ liệu của bạn sẽ sẵn sàng giao tiếp đúng với Django.

Để tạo các hàng mới trong bảng cơ sở dữ liệu, chúng ta có thể dùng phương thức `save` của model. Khi bạn lưu một instance model, Django sẽ gửi một thông điệp tới cơ sở dữ liệu, về bản chất là “thêm dữ liệu mới này vào bảng cơ sở dữ liệu này.” Những “thông điệp” này thực ra được gọi là **truy vấn** (query).

Như tôi đã đề cập ở phần settings, Django giao tiếp với cơ sở dữ liệu thông qua một engine cơ sở dữ liệu. Engine này sử dụng Structured Query Language (SQL) để giao tiếp với cơ sở dữ liệu thực tế. SQL là tiêu chuẩn chung mà tất cả các cơ sở dữ liệu được Django hỗ trợ đều “nói”. Vì Django dùng SQL, nên một thông điệp được gọi là “query”.

Một truy vấn SQL trông như thế nào? Nếu chúng ta lưu model ví dụ ở trên, nó sẽ trông như sau:

```sql
INSERT INTO "application_employee"
    ("first_name", "last_name", "job_title")
VALUES
    ('Tom', 'Bombadil', 'Old Forest keeper')
```

Lưu ý rằng ví dụ này là truy vấn `INSERT` khi dùng engine SQLite. Giống như ngôn ngữ tự nhiên, SQL có nhiều “phương ngữ” tùy vào cơ sở dữ liệu bạn chọn. Các cơ sở dữ liệu cố gắng tuân thủ một số tiêu chuẩn, nhưng mỗi loại lại có những điểm riêng, và nhiệm vụ của engine là làm cho các khác biệt này trở nên đồng nhất nhất có thể.

Đây là một lĩnh vực nữa mà Django làm rất nhiều việc nặng nhọc cho bạn. Engine cơ sở dữ liệu sẽ chuyển lệnh `save` của bạn thành truy vấn SQL phù hợp. SQL là một chủ đề rất sâu mà chúng ta không thể đề cập hết trong bài này. May mắn thay, chúng ta không cần vì đã có ORM của Django!

ORM là viết tắt của Object Relational Mapper. Nhiệm vụ của ORM là ánh xạ (hoặc chuyển đổi) từ các _object_ Python sang cơ sở dữ liệu _quan hệ_. Điều này có nghĩa là chúng ta chỉ cần làm việc với mã Python, còn Django sẽ lo việc lấy và lưu dữ liệu vào cơ sở dữ liệu.

Việc dùng `save` trên một bản ghi model chỉ là một ví dụ nhỏ về ORM của Django. Chúng ta còn có thể làm gì nữa? Chúng ta có thể:

- Lấy tất cả các hàng từ cơ sở dữ liệu.
- Lấy một tập hợp các hàng dựa trên tiêu chí lọc.
- Cập nhật một nhóm hàng cùng lúc.
- Xóa các hàng khỏi cơ sở dữ liệu.

Hầu hết các thao tác này với ORM Django đều thông qua class `Manager`. Ví dụ trước chỉ cho thấy cách thao tác với một hàng, còn manager của model có các phương thức thiết kế để làm việc với nhiều hàng.

Chúng ta hãy phân tích bảng nhân viên giả định. Manager cho một model được gắn vào class model dưới thuộc tính tên là `objects`. Hãy xem một đoạn mã:

```text
>>> from application.models import Employee
>>> bobs = Employee.objects.filter(first_name='Bob')
>>> for bob in bobs:
...     print(f"{bob.first_name} {bob.last_name}")
...
Bob Ross
Bob Barker
Bob Marley
Bob Dylan
>>> print(bobs.query)
SELECT
    "application_employee"."id",
    "application_employee"."first_name",
    "application_employee"."last_name",
    "application_employee"."job_title"
FROM "application_employee"
WHERE "application_employee"."first_name" = Bob
```

Trong ví dụ này, chúng ta dùng manager để lọc ra một tập con nhân viên trong bảng. Biến `bobs` trả về từ phương thức `filter` là một `QuerySet`. Như bạn có thể đoán, nó đại diện cho một tập các hàng mà một truy vấn SQL sẽ trả về. Bất cứ khi nào bạn có một queryset, bạn có thể in ra truy vấn để xem chính xác câu lệnh SQL mà Django sẽ chạy cho bạn.

Nếu bạn muốn xóa một bản ghi nhân viên thì sao?

```text
>>> from application.models import Employee
>>> # The price is wrong, Bob!
>>> Employee.objects.filter(
... first_name='Bob',
... last_name='Barker').delete()
(1, {'application.Employee': 1})
```

Một queryset có thể thực hiện các thao tác hàng loạt. Trong trường hợp này, bộ lọc đủ hẹp để chỉ xóa một bản ghi, nhưng nó có thể xóa nhiều hơn nếu truy vấn SQL khớp với nhiều hàng trong bảng.

Class `QuerySet` có nhiều phương thức hữu ích khi làm việc với bảng. Một số phương thức còn có đặc điểm thú vị là trả về một queryset mới. Đây là khả năng hữu ích khi bạn cần áp dụng thêm logic cho truy vấn của mình.

```python
from application.models import Employee

# employees là một QuerySet của tất cả các hàng!
employees = Employee.objects.all()

if should_find_the_bobs:
    # QuerySet mới!
    employees = employees.filter(
        first_name='Bob'
    )
```

Dưới đây là một số phương thức `QuerySet` mà tôi thường xuyên sử dụng:

- `create` - Thay vì tạo một instance bản ghi rồi gọi `save`, manager có thể tạo bản ghi trực tiếp.

```python
Employee.objects.create(
    first_name='Bobby',
    last_name='Tables'
)
```

- `get` - Dùng phương thức này khi bạn muốn lấy _chính xác một_ bản ghi. Nếu truy vấn không khớp hoặc trả về nhiều bản ghi, bạn sẽ nhận được exception.

```python
the_bob = Employee.objects.get(
    first_name='Bob',
    last_name='Marley'
)

Employee.objects.get(first_name='Bob')
# Raises
# application.models.Employee.MultipleObjectsReturned

Employee.objects.get(
    first_name='Bob',
    last_name='Sagat'
)
# Raises application.models.Employee.DoesNotExist
```

- `exclude` - Phương thức này cho phép bạn loại trừ các hàng có thể nằm trong queryset hiện tại.

```python
the_other_bobs = (
    Employee.objects.filter(first_name='Bob')
    .exclude(last_name='Ross')
)
```

- `update` - Với phương thức này, bạn có thể cập nhật một nhóm hàng trong một thao tác duy nhất.

```python
Employee.objects.filter(
    first_name='Bob'
).update(first_name='Robert')
```

- `exists` - Dùng phương thức này nếu bạn muốn kiểm tra xem có hàng nào trong cơ sở dữ liệu khớp với điều kiện bạn muốn kiểm tra không.

```python
has_bobs = Employee.objects.filter(
    first_name='Bob').exists()
```

- `count` - Kiểm tra có bao nhiêu hàng khớp với điều kiện. Do cách SQL hoạt động, lưu ý rằng cách này hiệu quả hơn so với dùng `len` trên một queryset.

```python
how_many_bobs = Employee.objects.filter(
    first_name='Bob').count()
```

- `none` - Trả về một queryset rỗng cho model. Điều này hữu ích khi bạn cần bảo vệ truy cập dữ liệu nhất định.

```python
employees = Employee.objects.all()

if not is_hr:
    employees = Employee.objects.none()
```

- `first` / `last` - Các phương thức này sẽ trả về một instance model nếu có bản ghi phù hợp. Các phương thức này dùng sắp xếp trên model để lấy kết quả mong muốn. Chúng ta dùng `order_by` để chỉ định cách sắp xếp kết quả.

```text
>>> a_bob = Employee.objects.filter(
...     first_name='Bob').order_by(
...     'last_name').last()
>>> print(a_bob.last_name)
Ross
```

Một thao tác `order_by` cũng có thể đảo ngược thứ tự kết quả. Để làm điều này, thêm dấu trừ trước tên trường như `order_by('-last_name')`.

Với kiến thức về cách bạn có thể tương tác với model, chúng ta có thể tập trung hơn vào loại dữ liệu có thể lưu trong model (và do đó là trong cơ sở dữ liệu).

## Các Loại Dữ Liệu Model

Bảng `Employee` mà tôi dùng làm ví dụ trong bài này chỉ có ba trường `CharField` trên model. Sự lựa chọn này là có chủ ý vì tôi muốn bạn có thời gian làm quen với ORM Django và làm việc với queryset trước khi xem các kiểu dữ liệu khác.

Chúng ta đã thấy trong bài về form rằng hệ thống form của Django có rất nhiều loại trường. Nếu bạn xem [tài liệu Form fields](https://docs.djangoproject.com/en/4.1/ref/forms/fields/) và so sánh với [tài liệu Model field](https://docs.djangoproject.com/en/4.1/ref/models/fields/), bạn sẽ thấy rất nhiều điểm tương đồng.

Giống như các trường form, model có `CharField`, `BooleanField`, `DateField`, `DateTimeField` và nhiều loại khác tương tự. Các loại trường này chia sẻ nhiều thuộc tính chung. Thường gặp nhất, tôi nghĩ bạn sẽ dùng hoặc gặp các thuộc tính sau.

- `default` - Nếu bạn muốn có thể tạo bản ghi model mà không cần chỉ định một số giá trị nhất định, bạn có thể dùng `default`. Giá trị này có thể là một giá trị cụ thể hoặc một hàm callable trả về giá trị.

```python
# application/models.py
import random

from django.db import models

def strength_generator():
    return random.randint(1, 20)

class DungeonsAndDragonsCharacter(
    models.Model
):
    name = models.CharField(
        max_length=100,
        default='Conan'
    )
    # Lưu ý: Truyền vào hàm, không gọi hàm!
    strength = models.IntegerField(
        default=strength_generator
    )
```

- `unique` - Khi một trường phải có giá trị duy nhất cho tất cả các hàng trong bảng, hãy dùng `unique`. Đây là thuộc tính tốt cho các định danh mà bạn không muốn trùng lặp.

```python
class ImprobableHero(models.Model):
    name = models.CharField(
        max_length=100,
        unique=True
    )

# Chỉ có thể có một người.
ImprobableHero.objects.create(
    name='Connor MacLeod'
)
```

- `null` - Cơ sở dữ liệu quan hệ có khả năng lưu trữ sự vắng mặt của dữ liệu. Trong cơ sở dữ liệu, giá trị này được gọi là `NULL`. Đôi khi đây là sự khác biệt quan trọng so với một giá trị rỗng. Ví dụ, trên model `Person`, một trường số nguyên như `number_of_children` sẽ có ý nghĩa rất khác nhau giữa giá trị 0 và giá trị `NULL`. 0 nghĩa là người đó không có con, còn `NULL` nghĩa là không biết số con. Sự xuất hiện của điều kiện null đòi hỏi bạn phải kiểm tra nhiều hơn trong mã nên Django mặc định `null` là `False`. Điều này nghĩa là trường không cho phép `NULL`. Null có thể hữu ích nếu cần, nhưng tôi nghĩ tốt hơn là tránh dùng nếu có thể và cố gắng lưu dữ liệu thực sự về trường đó.

```python
class Person(models.Model):
    # Trường này luôn có giá trị vì không cho phép null.
    # Số 0 được tính là giá trị và không phải NULL.
    age = models.IntegerField()
    # Trường này có thể không biết và chứa NULL.
    # Trong Python, giá trị NULL trong db sẽ là None.
    weight = models.IntegerField(
        null=True
    )
```

- `blank` - Thuộc tính `blank` thường được dùng cùng với `null`. Trong khi `null` cho phép cơ sở dữ liệu lưu `NULL` cho trường, `blank` cho phép _kiểm tra hợp lệ form_ chấp nhận trường rỗng. Điều này được dùng bởi các form tự động sinh ra bởi Django như trong trang quản trị mà chúng ta sẽ nói ở bài sau.

```python
class Pet(models.Model):
    # Không phải thú cưng nào cũng có đuôi,
    # nên chúng ta muốn form tự động cho phép không nhập giá trị.
    length_of_tail = models.IntegerField(
        null=True,
        blank=True
    )
```

- `choices` - Chúng ta đã thấy `choices` trong bài về form như một kỹ thuật giúp người dùng chọn giá trị đúng từ một tập hợp giới hạn. `choices` có thể được thiết lập trên model. Django có thể kiểm tra hợp lệ trên model để đảm bảo chỉ các giá trị nhất định được lưu vào trường.

```python
class Car(models.Model):
    COLOR_CHOICES = [
        (1, 'Black'),
        (2, 'Red'),
        (3, 'Blue'),
        (4, 'Green'),
        (5, 'White'),
    ]
    color = models.IntegerField(
        choices=COLOR_CHOICES,
        default=1
    )
```

- `help_text` - Khi ứng dụng lớn lên hoặc bạn làm việc trong một nhóm đông người cùng tạo model Django, nhu cầu tài liệu hóa tăng lên. Django cho phép thêm help text có thể hiển thị cùng trường trong trang quản trị. Help text này hữu ích để nhắc nhở chính bạn trong tương lai hoặc hướng dẫn đồng nghiệp.

```python
class Policy(models.Model):
    is_section_987_123_compliant = models.BooleanField(
        default=False,
        help_text=(
        'For policies that only apply'
        ' on leap days in accordance'
        ' with Section 987.123'
        ' of the Silly Draconian Order'
        )
    )
```

Đó là những thuộc tính mà tôi nghĩ bạn sẽ thường gặp nhất. Ngoài ra còn có một số loại trường quan trọng cần chú ý: các trường quan hệ.

## Điều Gì Làm Cho Cơ Sở Dữ Liệu “Quan Hệ”?

Cơ sở dữ liệu quan hệ có khả năng liên kết các loại dữ liệu khác nhau với nhau. Chúng ta đã có một ví dụ ngắn về điều này ở đầu bài khi xét một nhân viên có nhiều số điện thoại.

Một model đơn giản hóa quá mức cho nhân viên có nhiều số điện thoại có thể như sau:

```python
# application/models.py
from django.db import models

class Employee(models.Model):
    first_name = models.CharField(
        max_length=100
    )
    last_name = models.CharField(
        max_length=100
    )
    job_title = models.CharField(
        max_length=200
    )
    phone_number_1 = models.CharField(
        max_length=32
    )
    phone_number_2 = models.CharField(
        max_length=32
    )
```

Bảng đơn này có thể lưu một vài số, nhưng giải pháp này có một số hạn chế.

- Nếu một nhân viên có hơn hai số điện thoại thì sao? Một người có thể có nhiều điện thoại di động, một số bàn ở nhà, một số máy nhắn tin, số fax, v.v.
- Làm sao biết loại số điện thoại nào nằm ở `phone_number_1` và `phone_number_2`? Nếu bạn lấy bản ghi nhân viên để gọi cho họ mà lại gọi nhầm vào số fax thì sẽ khó nói chuyện với họ.

Thay vào đó, nếu chúng ta có hai model riêng biệt thì sao?

```python
# application/models.py
from django.db import models

class Employee(models.Model):
    first_name = models.CharField(
        max_length=100
    )
    last_name = models.CharField(
        max_length=100
    )
    job_title = models.CharField(
        max_length=200
    )

class PhoneNumber(models.Model):
    number = models.CharField(
        max_length=32
    )
    PHONE_TYPES = (
        (1, 'Mobile'),
        (2, 'Home'),
        (3, 'Pager'),
        (4, 'Fax'),
    )
    phone_type = models.IntegerField(
        choices=PHONE_TYPES,
        default=1
    )
```

Chúng ta có hai bảng riêng biệt. Làm sao liên kết các bảng để một nhân viên có thể có một, hai hoặc hai trăm số điện thoại? Để làm điều đó, chúng ta có thể dùng trường quan hệ `ForeignKey`. Đây là phiên bản cập nhật nhẹ của `PhoneNumber`.

```python
...

class PhoneNumber(models.Model):
    number = models.CharField(
        max_length=32
    )
    PHONE_TYPES = (
        (1, 'Mobile'),
        (2, 'Home'),
        (3, 'Pager'),
        (4, 'Fax'),
    )
    phone_type = models.IntegerField(
        choices=PHONE_TYPES,
        default=1
    )
    employee = models.ForeignKey(
        Employee,
        on_delete=models.CASCADE
    )
```

Bản cập nhật này nói rằng mỗi số điện thoại giờ phải liên kết với một bản ghi nhân viên. `on_delete` là thuộc tính bắt buộc xác định điều gì sẽ xảy ra khi bản ghi nhân viên bị xóa. Ở đây, `CASCADE` nghĩa là khi xóa một hàng nhân viên thì tất cả số điện thoại liên quan cũng bị xóa theo.

Chìa khóa để hiểu điều này nằm ở khái niệm _key_. Khi bạn tạo một model Django, framework sẽ tự động thêm một trường vào model của bạn. Trường này gọi là `AutoField`.

```python
# Đây là trường Django tự thêm vào model.
id = models.AutoField(primary_key=True)
```

`AutoField` thêm một cột vào bảng cơ sở dữ liệu để gán cho mỗi hàng một số nguyên duy nhất. Mỗi hàng mới sẽ tăng lên từ hàng trước và bắt đầu từ một. Số này là định danh cho hàng và gọi là _primary key_.

Nếu Tom Bombadil là nhân viên đầu tiên trong bảng, thì giá trị `id` của hàng đó sẽ là `1`.

Biết về primary key, chúng ta có thể hiểu trường foreign key. Trong trường hợp trường foreign key `PhoneNumber.employee`, bất kỳ hàng số điện thoại nào cũng sẽ lưu giá trị của primary key của một hàng nhân viên nào đó. Đây thường được gọi là quan hệ một-nhiều.

`ForeignKey` là quan hệ một-nhiều vì nhiều hàng từ một bảng (ở đây là `PhoneNumber`) có thể tham chiếu tới một hàng trong bảng khác, cụ thể là `Employee`. Nói cách khác, một nhân viên có thể có nhiều số điện thoại. Nếu chúng ta muốn lấy số điện thoại của Tom, một cách có thể là:

```python
tom = Employee.objects.get(
    first_name='Tom',
    last_name='Bombadil'
)
phone_numbers = PhoneNumber.objects.filter(employee=tom)
```

Truy vấn cho `phone_numbers` sẽ là:

```sql
SELECT
    "application_phonenumber"."id",
    "application_phonenumber"."number",
    "application_phonenumber"."phone_type",
    "application_phonenumber"."employee_id"
FROM "application_phonenumber"
WHERE "application_phonenumber"."employee_id" = 1
```

Trong cơ sở dữ liệu, Django sẽ lưu cột bảng cho foreign key là `employee_id`. Truy vấn này hỏi tất cả các hàng số điện thoại mà có employee ID là 1. Vì primary key phải duy nhất, giá trị 1 chỉ có thể khớp với Tom Bombadil nên các hàng trả về sẽ là số điện thoại liên kết với nhân viên đó.

Có một loại trường quan hệ nữa mà chúng ta nên tìm hiểu. Đó là trường `ManyToManyField`. Như bạn có thể đoán, trường này dùng khi hai loại dữ liệu liên kết với nhau theo kiểu nhiều-nhiều.

Hãy nghĩ về khu phố. Khu phố có thể có nhiều loại nhà ở như nhà riêng, căn hộ, chung cư, v.v., nhưng để đơn giản, ta giả sử khu phố chỉ gồm các ngôi nhà. Mỗi ngôi nhà trong khu phố là nơi ở của một hoặc nhiều người.

Nếu chúng ta thử mô hình hóa điều này với trường `ForeignKey` thì sao?

```python
# application/models.py
from django.db import models

class Person(models.Model):
    name = models.CharField(
        max_length=128
    )

class House(models.Model):
    address = models.CharField(
        max_length=256
    )
    resident = models.ForeignKey(
        Person,
        on_delete=models.CASCADE
    )
```

Phiên bản này cho thấy một ngôi nhà chỉ có một cư dân. Một người có thể là cư dân của nhiều nhà, nhưng những ngôi nhà đó sẽ khá cô đơn. Nếu chúng ta đặt foreign key ở phía còn lại của mối quan hệ thì sao?

```python
# application/models.py
from django.db import models

class House(models.Model):
    address = model.CharField(
        max_length=256
    )

class Person(models.Model):
    name = models.CharField(
        max_length=128
    )
    house = models.ForeignKey(
        House,
        on_delete=models.CASCADE
    )
```

Trong phiên bản này, một ngôi nhà có thể có nhiều cư dân, nhưng một người chỉ có thể thuộc về một ngôi nhà.

Cả hai kịch bản này đều không mô hình hóa thế giới thực tốt. Trong thực tế, nhà có thể và thường chứa nhiều người. Đồng thời, nhiều người trên thế giới có nhà thứ hai như nhà nghỉ biển hoặc nhà gỗ mùa hè. Cả hai phía của mối quan hệ đều có thể có nhiều phía còn lại.

Với `ManyToManyField`, bạn có thể thêm trường này vào bất kỳ phía nào. Đây là mô hình mới.

```python
# application/models.py
from django.db import models

class Person(models.Model):
    name = models.CharField(
        max_length=128
    )

class House(models.Model):
    address = models.CharField(
        max_length=256
    )
    residents = models.ManyToManyField(
        Person
    )
```

Điều này hoạt động thế nào ở mức cơ sở dữ liệu? Chúng ta đã thấy với foreign key là một bảng có thể lưu primary key của một hàng bảng khác trong dữ liệu của nó. Không may, một cột cơ sở dữ liệu không thể lưu nhiều foreign key. Điều đó nghĩa là mô hình trên _không_ thêm `residents` vào bảng `House`. Thay vào đó, mối quan hệ được xử lý bằng cách thêm một bảng cơ sở dữ liệu _mới_. Bảng mới này chứa ánh xạ giữa người và nhà, lưu các hàng chứa primary key từ mỗi model.

Hãy nghĩ về một ví dụ để xem nó trông như thế nào. Giả sử có ba bản ghi `Person` với primary key là 1, 2, và 3. Giả sử cũng có ba bản ghi `House` với primary key là 97, 98, và 99. Để chứng minh quan hệ nhiều-nhiều hoạt động hai chiều, ta giả định các điều kiện sau:

- Người có primary key 1 và 2 sống ở nhà 97.
- Người có primary key 3 sở hữu nhà 98 và 99.

Dữ liệu trong bảng ánh xạ mới giữa `Person` và `House` sẽ như sau:

```text
Person | House
-------|------
1      | 97
2      | 97
3      | 98
3      | 99
```

Nhờ bảng liên kết này, Django có thể truy vấn từ bất kỳ phía nào để lấy nhà hoặc cư dân liên quan.

Chúng ta có thể truy cập phía “nhiều” của mỗi model bằng queryset. `residents` sẽ là một `ManyRelatedManager` và, giống như các manager khác, có thể cung cấp queryset bằng các phương thức manager nhất định.

Lấy chiều ngược lại thì ít rõ ràng hơn. Django sẽ tự động thêm một `ManyRelatedManager` khác vào model `Person`. Tên của manager này là tên model ghép với `_set`. Trong trường hợp này, tên đó là `house_set`. Bạn cũng có thể cung cấp thuộc tính `related_name` cho trường `ManyToManyField` nếu muốn tên khác, ví dụ như `houses`.

```python
house = House.objects.get(
    address='123 Main St.'
)
# Lưu ý dùng `all()`!
for resident in house.residents.all():
    print(resident.name)

person = Person.objects.get(name='Joe')
for house in person.house_set.all():
    print(house.address)
```

Hiểu về các trường model Django như `ForeignKey` và `ManyToManyField` là một bước quan trọng để mô hình hóa tốt miền vấn đề của bạn. Với các công cụ này, bạn có thể bắt đầu tạo ra nhiều mối quan hệ dữ liệu phức tạp tồn tại trong các vấn đề thực tế.

## Tóm Tắt

Trong bài viết này, chúng ta đã khám phá:

- Cách thiết lập cơ sở dữ liệu cho dự án của bạn.
- Cách Django sử dụng các class đặc biệt gọi là model để lưu dữ liệu.
- Chạy các lệnh để chuẩn bị cơ sở dữ liệu cho các model bạn muốn dùng.
- Lưu thông tin mới vào cơ sở dữ liệu.
- Truy vấn cơ sở dữ liệu để lấy thông tin đã lưu.
- Các loại trường phức tạp để mô hình hóa các vấn đề thực tế.

Với khả năng lưu dữ liệu vào cơ sở dữ liệu, **bạn đã có tất cả công cụ cốt lõi để xây dựng một trang web tương tác cho người dùng!** Trong loạt bài này, chúng ta đã xem xét:

- Xử lý URL
- view để chạy mã và logic nghiệp vụ
- template để hiển thị giao diện người dùng
- form để cho phép người dùng nhập và tương tác với trang
- model để lưu dữ liệu vào cơ sở dữ liệu cho lưu trữ lâu dài

Đây là tập hợp tính năng cốt lõi mà hầu hết các trang web đều có. Vì chúng ta đã thấy các chủ đề cốt lõi giúp Django hoạt động, chúng ta đã sẵn sàng tập trung vào một số công cụ tuyệt vời khác giúp Django nổi bật.

Đầu tiên trong danh sách là trang quản trị tích hợp sẵn của Django cho phép bạn khám phá dữ liệu đã lưu trong cơ sở dữ liệu. Chúng ta sẽ tìm hiểu:

- Trang quản trị Django là gì
- Cách làm cho model của bạn xuất hiện trong admin
- Cách tạo các hành động bổ sung mà người dùng admin có thể thực hiện
