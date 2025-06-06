# Phục Vụ Static Files

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), tôi đã mô tả cách Django cung cấp cho chúng ta các công cụ để chạy mã cho bất kỳ request nào thông qua hệ thống middleware. Tiếp theo, chúng ta sẽ tập trung vào static files. Static files rất quan trọng với ứng dụng của bạn, nhưng chúng lại ít liên quan đến mã Python. Chúng ta sẽ xem static files là gì và chúng làm gì.

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
11. Phục Vụ Static Files
12. [Kiểm Thử Ứng Dụng](https://www.mattlayman.com/understand-django/test-your-apps/)
13. [Triển Khai Trang Web](https://www.mattlayman.com/understand-django/deploy-site-live/)
14. [Dữ Liệu Theo Từng Người Dùng Với Sessions](https://www.mattlayman.com/understand-django/sessions/)
15. [Hiểu Về Settings](https://www.mattlayman.com/understand-django/settings/)
16. [Quản Lý File Người Dùng](https://www.mattlayman.com/understand-django/media-files/)
17. [Lệnh Cho Ứng Dụng](https://www.mattlayman.com/understand-django/command-apps/)
18. [Tăng Tốc Với Django](https://www.mattlayman.com/understand-django/go-fast/)
19. [Bảo Mật Và Django](https://www.mattlayman.com/understand-django/secure-apps/)
20. [Mẹo Và Kỹ Thuật Gỡ Lỗi](https://www.mattlayman.com/understand-django/debugging-tips-techniques/)

## Static Files Là Gì?

Static files là các file không thay đổi khi ứng dụng của bạn đang chạy.

Những file này đóng vai trò lớn trong việc cải thiện ứng dụng của bạn, nhưng chúng không được sinh ra động bởi web server Python như một response HTML thông thường. Trong một ứng dụng web điển hình, các static files phổ biến nhất sẽ là:

- Cascading Style Sheets, CSS
- JavaScript
- Hình ảnh

Hãy nhớ rằng dù Django sẽ phục vụ các file này một cách tĩnh, có thể có một quy trình phức tạp để tạo ra các file đó. Ví dụ, các ứng dụng JavaScript hiện đại thường dùng các công cụ build phức tạp như [webpack](https://webpack.js.org/) để tạo ra các file JavaScript cuối cùng phục vụ cho người dùng.

Static files rất quan trọng với dự án Django của bạn vì web hiện đại đòi hỏi nhiều hơn là chỉ HTML sinh động. Bạn có từng truy cập website nào mà _không có_ bất kỳ style nào cho HTML không? Những trang như vậy vẫn tồn tại và có thể rất tuyệt để làm công cụ nhanh, nhưng hầu hết người dùng mong đợi website phải đẹp mắt. Với chúng ta, điều đó nghĩa là nên chuẩn bị để thêm một chút CSS tối thiểu.

Hãy xem một số cấu hình để biết static files nằm ở đâu trong dự án, rồi bắt đầu làm việc với một số ví dụ.

## Cấu Hình

Để sử dụng static files trong dự án, bạn cần app `django.contrib.staticfiles` trong danh sách `INSTALLED_APPS` của dự án. Đây là một trong những ứng dụng mặc định mà Django sẽ thêm nếu bạn khởi tạo dự án bằng lệnh `startproject`.

App `staticfiles` có một số [thiết lập](https://docs.djangoproject.com/en/4.1/ref/settings/#settings-staticfiles) mà chúng ta cần quan tâm để bắt đầu.

Tôi sẽ lặp lại khuyến nghị về static files giống như với templates. Tôi khuyên bạn nên tạo một thư mục `static` ở gốc dự án để chứa các static files. Tương tự như templates, app `staticfiles` sẽ tìm các thư mục `static` trong từng app Django để lấy file, nhưng tôi thấy dễ làm việc và dễ tìm hơn nếu tất cả static files nằm cùng một chỗ.

Để thiết lập như vậy, hãy dùng thiết lập `STATICFILES_DIRS`. Thiết lập này cho Django biết các vị trí bổ sung cho static files ngoài việc tìm trong thư mục `static` của từng app.

```python
# project/settings.py

...

STATICFILES_DIRS = [BASE_DIR / "static"]
```

Tiếp theo, chúng ta có thể định nghĩa tiền tố đường dẫn URL mà Django sẽ dùng khi phục vụ static file. Giả sử bạn có file `site.css` trong thư mục `static` ở gốc dự án. Có lẽ bạn không muốn file này truy cập được qua `mysite.com/site.css`. Làm vậy sẽ khiến static files có thể xung đột với các đường dẫn URL mà app của bạn cần chuyển đến view. Thiết lập `STATIC_URL` giúp chúng ta đặt namespace cho static files và như [Zen of Python](https://www.python.org/dev/peps/pep-0020/) nói:

> _Namespaces are one honking great idea – let’s do more of those!_

```python
# project/settings.py

...

STATICFILES_DIRS = [BASE_DIR / "static"]
STATIC_URL = '/static/'
```

Với `STATIC_URL` đã đặt, chúng ta có thể truy cập `site.css` qua `mysite.com/static/site.css`.

Có một thiết lập quan trọng nữa cần đặt, đó là `STATIC_ROOT`. Khi triển khai dự án Django, Django muốn tìm tất cả static files từ một thư mục duy nhất. Lý do là để tăng hiệu quả. Django có thể tìm qua tất cả các thư mục `static` của app và các thư mục trong `STATICFILES_DIRS` mỗi khi tìm file để phục vụ, nhưng như vậy sẽ chậm.

Thay vào đó, Django sẽ gom tất cả static files vào một thư mục duy nhất để việc tìm kiếm file chỉ còn là tìm trong một cây thư mục. Chúng ta sẽ xem kỹ hơn về cách này ở phần triển khai phía sau.

Khi đã đặt `STATIC_ROOT`, Django sẽ biết nơi xuất ra static files. Nếu bạn đặt đường dẫn này trong repository, đừng quên thêm đường dẫn đó vào `.gitignore` nếu bạn dùng version control với [Git](https://git-scm.com/) (và tôi rất khuyến khích bạn làm vậy!). Nếu không thêm vào `.gitignore`, bạn sẽ vô tình đưa các file sinh ra vào version control. Tôi thường đặt `STATIC_ROOT` là thư mục `staticfiles`.

```python
# project/settings.py

...

STATICFILES_DIRS = [BASE_DIR / "static"]
STATIC_ROOT = BASE_DIR / "staticfiles"
STATIC_URL = '/static/'
```

Giờ chúng ta đã biết cách cấu hình static files, hãy xem cách sử dụng chúng trong mã Django.

## Làm Việc Với Static Files

Cách chính để làm việc với static files là dùng template tag. Template tag `static` sẽ giúp render đúng URL cho static file trên trang của bạn.

Đây là một ví dụ template:

```django
{% load static %}
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" type="text/css" href="{% static "css/site.css" %}">
</head>
<body>
  <h1>Example of static template tag!</h1>
</body>
</html>
```

Trong ví dụ này, tôi giả định có một thư mục `css` trong thư mục `static` với file `site.css` bên trong. Django sẽ render template tag này thành `/static/css/site.css` ở dạng cơ bản nhất. Cũng cần lưu ý là tôi phải thêm `{% load static %}` để đảm bảo template tag `static` khả dụng.

Thực tế, tôi thấy yêu cầu `load` này thường làm tôi quên. May mắn thay, `TemplateSyntaxError` mà Django raise sẽ gợi ý rõ cách sửa lỗi này. Exception sẽ nói “Did you forget to register or load this tag?” Thật tuyệt khi các lập trình viên Django nhắc nhở chúng ta điều còn thiếu!

Vì chúng ta biết `STATIC_URL` là `/static/` từ phần cấu hình, tại sao tôi không hardcode đường dẫn trong thẻ link thành `/static/css/site.css`? Bạn có thể làm vậy, và có thể nó sẽ hoạt động, nhưng về lâu dài bạn sẽ gặp một số vấn đề.

- Nếu bạn muốn đổi `STATIC_URL` thì sao? Có thể bạn muốn đổi thành `/s/` cho ngắn hơn. Nếu bạn hardcode tên, giờ bạn phải sửa ở nhiều chỗ.
- Khi dùng một số tính năng bổ sung, Django có thể đổi tên file thành một tên duy nhất bằng cách thêm hash vào tên file. Nếu bạn hardcode đường dẫn `/static/css/site.css`, điều này có thể dẫn đến lỗi 404 nếu Django mong đợi tên file duy nhất. Chúng ta sẽ xem tên file duy nhất này dùng để làm gì ở phần sau.

Chúng ta nên nhớ dùng tag `static` giống như dùng tag `url` khi muốn resolve một đường dẫn URL Django. Cả hai tag này giúp tránh hardcode các đường dẫn có thể thay đổi.

Ít phổ biến hơn, chúng ta có thể tham chiếu static file từ mã Python. Bạn có thể làm điều này bằng cách gọi hàm `static` được định nghĩa cùng nơi với template tag `static`, nhưng hàm này không nằm ở nơi bạn nghĩ. Thay vì import từ app `staticfiles`, Django định nghĩa các hàm này trong `django.templatetags.static`.

Ví dụ, nếu bạn muốn phục vụ một view JSON trả về đường dẫn đến file CSS cho ứng dụng JavaScript client, bạn có thể viết:

```python
# application/views.py

from django.http import JsonResponse
from django.templatetags.static import (
    static
)

def get_css(request):
    return JsonResponse(
        {'css': static('css/site.css')}
    )
```

Trong nhiều năm làm Django, tôi chỉ thấy `static` dùng trong view vài lần. `static` chắc chắn phổ biến hơn nhiều trong template.

Khi dùng static files, có một số lưu ý quan trọng khi triển khai ứng dụng ra internet. Triển khai là một chủ đề lớn sẽ bàn ở bài sau, nhưng chúng ta sẽ tập trung vào các vấn đề triển khai static files ngay sau đây.

## Lưu Ý Khi Triển Khai

Ở phần cấu hình, chúng ta đã thấy tùy chọn `STATIC_ROOT`. Tùy chọn này sẽ gom tất cả static files vào một thư mục, nhưng _khi nào_ nó làm vậy? Và static files hoạt động thế nào khi chạy ở chế độ phát triển mà không có tất cả file trong `STATIC_ROOT`?

Khi bạn triển khai ứng dụng lên server, một thiết lập quan trọng cần tắt là `DEBUG`. Nếu `DEBUG` bật, mọi loại dữ liệu bí mật có thể bị lộ từ ứng dụng, nên các lập trình viên Django _mong đợi_ `DEBUG` là `False` cho trang live. Vì kỳ vọng này, một số phần của Django sẽ hoạt động khác khi `DEBUG` thay đổi, và app `staticfiles` là một trong số đó.

Khi `DEBUG` là `True` và bạn dùng lệnh `runserver` để chạy web server phát triển, Django sẽ tìm file bằng một tập các “finder” mỗi khi người dùng yêu cầu static file. Các finder này được định nghĩa bởi thiết lập `STATICFILES_FINDERS`, mặc định là:

```python
[
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
]
```

Như bạn đoán, `FileSystemFinder` sẽ tìm static files trong thư mục file system mà ta liệt kê trong `STATICFILES_DIRS`. `AppDirectoriesFinder` sẽ tìm static files trong thư mục `static` của từng app Django bạn có. Bạn có thể thấy quá trình này sẽ chậm nếu Django phải duyệt qua `len(STATICFILES_DIRS) + len(INSTALLED_APPS)` trước khi bỏ cuộc để tìm một file.

Để làm quá trình này nhanh hơn, ta chuyển `DEBUG` thành `False`. Khi `DEBUG` là `False`, tất cả cơ chế tìm kiếm chậm chạp này sẽ bị tắt khi xử lý static file. Django chỉ tìm file trong thư mục `STATIC_ROOT`.

Vì finder bị tắt khi `DEBUG` là `True`, ta phải đảm bảo `STATIC_ROOT` chứa đầy đủ các file cần thiết. Để gom tất cả static files vào đúng chỗ, bạn có thể dùng lệnh `collectstatic`.

`collectstatic` sẽ copy tất cả file nó tìm được từ từng finder và gom lại từ những gì finder liệt kê. Trong ví dụ dưới đây, thư mục dự án Django của tôi là `myproject`, và tôi đặt `STATIC_ROOT` là `staticfiles`.

```shell
$ ./manage.py collectstatic

42 static files copied to '/Users/matt/myproject/staticfiles'.
```

Khi triển khai ứng dụng lên server, bạn sẽ chạy `collectstatic` trước khi khởi động web server. Làm vậy sẽ đảm bảo web server có thể truy cập mọi static file mà app Django có thể yêu cầu.

Có thể làm tốt hơn không? Chắc chắn rồi!

### Tối Ưu Hiệu Năng Trong Django

`staticfiles` có một thiết lập nữa đáng cân nhắc. Tôi không nhắc đến nó ở phần cấu hình vì nó không bắt buộc để static files hoạt động, nhưng chúng ta nên nghĩ đến nó khi tối ưu hóa. Chúng ta thực sự nên cân nhắc thiết lập này vì Django khá chậm khi phục vụ static files so với một số lựa chọn khác trong hệ sinh thái.

Thiết lập cuối cùng chúng ta sẽ xét là `STATICFILES_STORAGE`. Thiết lập này kiểm soát cách static files được lưu trữ và truy cập bởi Django. Ta có thể muốn thay đổi `STATICFILES_STORAGE` để tăng hiệu quả cho ứng dụng. Lợi ích lớn nhất từ thiết lập này là giúp cache file.

Trong thế giới lý tưởng, ứng dụng của bạn chỉ cần phục vụ một static file _một lần duy nhất_ cho trình duyệt người dùng. Khi đó, nếu ứng dụng cần dùng lại file, trình duyệt sẽ dùng lại file _đã cache_ mà nó đã lấy. Thách thức là static files (nghe mỉa mai nhỉ?) lại thay đổi theo thời gian.

Ví dụ, bạn thay đổi `site.css` để đổi style cho ứng dụng. Bạn không muốn trình duyệt dùng lại phiên bản cũ vì nó thiếu các thay đổi mới nhất bạn vừa làm. Làm sao để vừa tận dụng cache lâu dài cho file để đạt hiệu năng tối đa, vừa linh hoạt cập nhật và buộc trình duyệt lấy phiên bản mới?

“Bí quyết” là phục vụ một phiên bản file “có dấu vân tay” (fingerprinted). Trong quá trình triển khai, ta muốn nhận diện duy nhất từng file bằng một thông tin phiên bản nào đó. Cách dễ nhất là lấy nội dung file và tính giá trị hash. Ta có thể cho mã lấy `site.css`, tính hash, và tạo ra file cùng nội dung nhưng tên khác như `site.abcd1234.css` nếu `abcd1234` là hash sinh ra.

Bước tiếp theo là làm cho template render dùng tên `site.abcd1234.css`. Nhớ cách ta dùng template tag `static` thay vì hardcode `/static/css/site.css` chứ? Đây là lý do tuyệt vời để làm vậy. Nhờ dùng tag `static`, Django có thể render tên file có hash thay vì chỉ dùng `site.css`.

Cuối cùng, ta cần báo cho trình duyệt cache `site.abcd1234.css` rất lâu bằng cách gửi header cache phù hợp trong HTTP response.

Giờ ta có được cả hai điều tốt nhất.

- Nếu người dùng lấy `site.abcd1234.css`, trình duyệt sẽ giữ nó rất lâu và không cần tải lại. File này có thể dùng lại mỗi lần người dùng truy cập trang trong app của bạn.
- Nếu bạn thay đổi `site.css`, quá trình triển khai sẽ sinh ra file mới như `site.ef567890.css`. Khi người dùng truy cập, HTML sẽ chứa phiên bản mới, trình duyệt chưa có file này trong cache và sẽ tải về phiên bản mới với thay đổi của bạn.

Tuyệt vời! Làm sao để có được điều này và tốn bao nhiêu công? Câu trả lời nằm ở thiết lập `STATICFILES_STORAGE` và một công cụ tên là [WhiteNoise](http://whitenoise.evans.io/en/stable/) (bạn hiểu chứ!? “white noise” _là_ “static.” ha ha).

WhiteNoise là một phần mềm tuyệt vời. Thư viện này sẽ xử lý _toàn bộ_ cơ chế cache mà tôi vừa mô tả.

Để cài đặt WhiteNoise, bạn dùng `pip install whitenoise`. Sau đó, bạn cần thay đổi danh sách `MIDDLEWARE` và thiết lập `STATICFILES_STORAGE`.

```python
# project/settings.py

...

MIDDLEWARE = [
  'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  # ...
]

STATICFILES_STORAGE = \
    'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

Chỉ vậy thôi! Với thiết lập này, WhiteNoise sẽ làm rất nhiều việc khi chạy lệnh `collectstatic`. Thư viện sẽ sinh ra các file có dấu vân tay như `site.abcd1234.css`, và tạo ra các phiên bản nén của file đó bằng thuật toán nén gzip (và tùy chọn cả [brotli](https://en.wikipedia.org/wiki/Brotli)). Các file bổ sung này sẽ có dạng như `site.abcd1234.css.gz` hoặc `site.abcd1234.css.br`.

Khi ứng dụng chạy, middleware WhiteNoise sẽ xử lý việc phục vụ file nào. Vì file là tĩnh và không cần xử lý động, ta nên đưa middleware này lên cao trong danh sách để bỏ qua nhiều xử lý Python không cần thiết. Trong ví dụ cấu hình của tôi, tôi để `SecurityMiddleware` trên WhiteNoise để app vẫn hưởng lợi từ một số bảo vệ bảo mật.

Khi trình duyệt người dùng gửi request cho file có dấu vân tay, trình duyệt có thể gửi header yêu cầu các định dạng nén mà nó hỗ trợ. Gửi file nén _nhanh hơn rất nhiều_ so với gửi file chưa nén qua mạng. WhiteNoise sẽ đọc header phù hợp và cố gắng trả về phiên bản gzip hoặc brotli.

Cơ chế tôi mô tả không phải là cách duy nhất để xử lý static files. Thực tế, có một số đánh đổi cần cân nhắc:

1. Dùng WhiteNoise nghĩa là chỉ cần triển khai một app và để Python xử lý mọi thứ.
2. Python, dù có nhiều lợi ích, không phải là ngôn ngữ nhanh nhất. Để Python phục vụ static files sẽ chậm hơn một số phương pháp khác. Ngoài ra, các process web server của bạn phải dành thời gian phục vụ static files thay vì tập trung hoàn toàn cho các request động.

### Tối Ưu Hiệu Năng Với Reverse Proxy

Một cách tiếp cận khác thay vì để Django phục vụ static files là dùng một chương trình khác làm _reverse proxy_. Thiết lập này phức tạp hơn, nhưng có thể mang lại hiệu năng tốt hơn nếu bạn cần. Reverse proxy là phần mềm nằm giữa người dùng và application server Django của bạn. CloudFlare có một [bài viết hay](https://www.cloudflare.com/learning/cdn/glossary/reverse-proxy/) nếu bạn muốn hiểu vì sao lại gọi là “reverse”.

Nếu bạn thiết lập reverse proxy, bạn có thể chỉ định nó xử lý nhiều thứ, bao gồm các đường dẫn URL đến domain của trang. Đây là lúc `STATIC_ROOT` và `collectstatic` hữu ích ngoài Django. Bạn có thể cấu hình reverse proxy để phục vụ tất cả file mà Django đã gom vào `STATIC_ROOT`.

Quy trình đại khái là:

1. Chạy `collectstatic` để gom file vào `STATIC_ROOT`.
2. Cấu hình reverse proxy để xử lý mọi URL bắt đầu bằng `STATIC_URL` (ví dụ `/static/`) và chuyển các request đó đến cấu trúc thư mục của `STATIC_ROOT`.
3. Bất cứ thứ gì không giống static file (ví dụ `/accounts/login/`) sẽ được chuyển cho app server chạy Django.

Với thiết lập này, app Django không cần lo phục vụ static files vì reverse proxy đã xử lý các request đó trước khi đến app server. Hiệu năng tăng lên nhờ reverse proxy. Hầu hết reverse proxy được viết bằng các ngôn ngữ hiệu năng cao như C vì chúng được thiết kế để xử lý một vấn đề cụ thể: định tuyến request. Quy trình này giúp Django chỉ xử lý các request động cần thiết và tránh để các process Python chậm hơn phải làm việc mà reverse proxy sinh ra để làm.

Nếu bạn thích kiểu thiết lập này, một reverse proxy bạn có thể cân nhắc là [Nginx](https://www.nginx.com/). Việc cấu hình Nginx vượt ngoài phạm vi loạt bài này, nhưng có rất nhiều hướng dẫn tốt chỉ cách cấu hình Django với Nginx.

## Tóm Tắt

Trong bài viết này, chúng ta đã tìm hiểu về static files.

Chúng ta đã xem:

- Cách cấu hình static files
- Cách làm việc với static files
- Cách xử lý static files khi triển khai trang web lên internet

Nhìn về phía trước, bài tiếp theo chúng ta sẽ tìm hiểu về kiểm thử tự động cho ứng dụng Django. Kiểm thử là một trong những chủ đề tôi yêu thích nên tôi rất háo hức chia sẻ với bạn về nó. Chúng ta sẽ bàn về:

- Tại sao lại muốn viết kiểm thử tự động
- Những loại kiểm thử nào hữu ích cho ứng dụng Django
- Những công cụ nào giúp kiểm thử dễ dàng hơn
