# Giải Phẫu Một Ứng Dụng

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã đi sâu vào trang quản trị Django. Chúng ta đã thấy trang này là gì và cách cấu hình cũng như tùy biến nó. Trong bài viết này, chúng ta sẽ xem xét những gì tạo nên một ứng dụng. Ứng dụng là các thành phần cốt lõi của một dự án Django.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/)
8. Giải Phẫu Một Ứng Dụng
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

## Ứng Dụng Là Gì?

Trước khi nói về một ứng dụng Django **là gì**, có lẽ chúng ta nên bắt đầu với nó **không phải là gì** vì thuật ngữ này khá dễ gây nhầm lẫn. Trong thế giới phát triển web, các lập trình viên có thể gọi một website là một “web application”.

Trong cách nói của Django, một “web application” là một _dự án_ Django. Tất cả các thành phần kết hợp lại để tạo thành một website được gọi là một dự án. Các thành phần chính bên trong dự án được gọi là _ứng dụng_. Nói cách khác, một dự án Django được xây dựng từ một hoặc nhiều ứng dụng Django.

Tình huống này khá giống với các package Python. Ngành phần mềm thường mô tả một đơn vị phần mềm là một “package”. Chúng ta nghĩ về `pip`, `npm` hoặc `apt` như là các trình quản lý “package”. Điều này dẫn đến một vấn đề đặt tên tương tự vì Python cũng gọi bất kỳ thư mục nào có file `__init__.py` là một “package”.

Thực tế, mã mà bạn tải về bằng pip về mặt kỹ thuật được gọi là một “[distribution](https://packaging.python.org/overview/)”. Dù chúng ta thường nói về việc tải các package Python từ PyPI (Python Package Index), thực ra chúng ta đang nói về distribution, và một distribution là một đơn vị chứa một hoặc nhiều package Python.

Hy vọng đến đây bạn đã hiểu mối quan hệ của các ứng dụng trong Django.

> _“Web application” của bạn là một **dự án** Django được cấu thành từ một hoặc nhiều **ứng dụng** Django._

## Cấu Trúc Ứng Dụng

Hãy cùng xem một ứng dụng Django đầy đủ để thấy cấu trúc khá tiêu chuẩn mà bạn sẽ gặp trong các dự án Django.

Một ứng dụng thường cố gắng nắm bắt một khái niệm cốt lõi trong hệ thống của bạn. Trong bài viết này, chúng ta sẽ dùng phim ảnh làm ví dụ cho khái niệm muốn mô hình hóa.

Hãy xem một scaffold mặc định bao gồm những gì, rồi xây dựng thêm với các thành phần bổ sung.

```shell
(venv) $ ./manage.py startapp movies
(venv) $ tree movies
movies
├── __init__.py
├── admin.py
├── apps.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```

`admin.py`: File này là nơi chứa tất cả các class `ModelAdmin` để điều khiển cách ứng dụng movies sẽ xuất hiện trong trang quản trị Django. Bạn có thể tìm hiểu thêm về admin trong [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/).

`apps.py`: File này dành cho `AppConfig` của ứng dụng. Chúng ta sẽ bàn về `AppConfig` và cách sử dụng nó ở phần sau của bài viết này.

`migrations`: Thư mục này là nơi lưu trữ tất cả các migration cơ sở dữ liệu cho ứng dụng. Bất kỳ thay đổi model nào cho app này sẽ sinh ra một migration và tạo một file migration được đánh số trong thư mục này. Thông tin thêm về migration có trong [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/).

`models.py`: File này là nơi chứa tất cả các class `Model` của Django trong ứng dụng. Các model đại diện cho toàn bộ dữ liệu cơ sở dữ liệu của bạn. Tìm hiểu thêm về model trong [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/).

`tests.py`: File này dành cho các kiểm thử tự động. Chúng ta sẽ bàn về kiểm thử tự động trong Django ở một bài viết sau. Hiện tại, bạn chỉ cần biết rằng tôi _luôn_ **xóa** file này và thay bằng một package `tests`. Một package `tests` tốt hơn vì bạn có thể tách ra các file chuyên biệt như `test_models.py` để biết kiểm thử nào nằm ở đâu.

`views.py`: File này là nơi chứa các hàm hoặc class view của Django. View là mã “kết dính” kết nối các route URL với các model cơ sở dữ liệu của bạn. Tôi đã viết về view trong [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/).

Đó là tất cả những gì đi kèm với một ứng dụng được sinh ra, nhưng còn những file nào khác mà bạn thường thấy trong một ứng dụng Django?

`urls.py`: File này thường được dùng để tạo các route nhóm logic tất cả chức năng liên quan đến phim. File `urls.py` sẽ điều khiển tất cả các route như `www.mysite.com/movies/`. Thông tin về URL có trong [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/).

`forms.py`: Khi bạn dùng các class `Form` của Django để tương tác với người dùng, đây là file lưu trữ các form. Bạn có thể tìm hiểu thêm về form trong [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/).

`templatetags`: Thư mục này là một package Python sẽ chứa một module như `movies_tags.py` nơi bạn định nghĩa các template tag tùy biến để dùng khi render template. Tag tùy biến là chủ đề trong [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/).

`templates`: Thư mục này có thể lưu các template mà ứng dụng sẽ render. Cá nhân tôi thích dùng một thư mục `templates` toàn dự án như đã bàn trong [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/), nhưng các thư mục `templates` thường xuất hiện trong từng app Django, đặc biệt là với các ứng dụng bên thứ ba mà bạn có thể thêm vào dự án.

`static`: Đối với các file tĩnh bạn muốn hiển thị, như hình ảnh, bạn có thể dùng thư mục `static`. Chúng ta sẽ bàn về file tĩnh ở một bài viết sau.

`management`: Người dùng có thể mở rộng Django với các lệnh tùy biến gọi qua `manage.py`. Các lệnh đó được lưu trong package này. Lệnh tùy biến sẽ là chủ đề trong các bài sau của loạt bài này.

`locale`: Khi làm dịch thuật và quốc tế hóa, các file dịch thuật cần có chỗ lưu trữ. Đó là mục đích của thư mục `locale`.

`managers.py`: File này không phải lúc nào cũng có, nhưng nếu ứng dụng của bạn có nhiều manager tùy biến, bạn có thể tách chúng khỏi model trong file này. Manager là chủ đề trong [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/).

Hầu hết các ứng dụng sẽ _không_ có đầy đủ tất cả các thành phần này, nhưng điều này sẽ giúp bạn hình dung các thành phần khi khám phá các ứng dụng Django ngoài thực tế. Đây là cây thư mục mẫu cuối cùng của chúng ta.

```shell
(venv) $ tree movies
movies
├── __init__.py
├── admin.py
├── apps.py
├── forms.py
├── locale
│   └── es
│       └── LC_MESSAGES
│           ├── django.mo
│           └── django.po
├── management
│   ├── __init__.py
│   └── commands
│       ├── __init__.py
│       └── do_movie_stuff.py
├── managers.py
├── migrations
│   ├── 0001_initial.py
│   └── __init__.py
├── models.py
├── static
│   └── movies
│       └── moviereel.png
├── templates
│   └── movies
│       ├── index.html
│       └── movie_detail.html
├── templatetags
│   ├── __init__.py
│   └── movies_tags.py
├── tests
│   ├── __init__.py
│   ├── test_models.py
│   └── test_views.py
├── urls.py
└── views.py
```

## Nạp Ứng Dụng

Giờ chúng ta đã thấy bên trong một ứng dụng Django có gì và có ý tưởng về thành phần của một app. Django nạp ứng dụng như thế nào?

Django _không_ tự động phát hiện các ứng dụng Django trong dự án của bạn. Nếu bạn muốn Django đưa một app vào dự án, bạn _phải_ thêm app đó vào danh sách `INSTALLED_APPS` trong file settings.

Đây là một ví dụ điển hình cho việc Django tuân theo triết lý của Python là ưu tiên rõ ràng hơn ngầm định. Bằng cách rõ ràng, dự án của bạn sẽ không gặp nguy cơ đưa vào các app mà bạn không mong đợi. Điều này có thể nghe có vẻ ngớ ngẩn với các app bạn tự viết, nhưng bạn sẽ biết ơn điều này khi một package bên thứ ba nào đó trong môi trường ảo của bạn tình cờ có một app Django mà bạn không muốn đưa vào dự án.

Khi khởi động, nếu một ứng dụng nằm trong `INSTALLED_APPS`, Django sẽ tìm một class `AppConfig`. Class này được lưu trong `apps.py` từ lệnh `startapp` và chứa metadata về ứng dụng.

Khi Django khởi động, nó sẽ khởi tạo hệ thống bằng cách thực hiện các bước sau:

- Nạp settings
- Cấu hình logging (chủ đề sẽ bàn sau)
- Khởi tạo một registry ứng dụng
- Import từng package trong `INSTALLED_APPS`
- Import module models cho mỗi ứng dụng
- Gọi phương thức `ready` của mọi `AppConfig` tìm thấy

Phương thức `ready` là một hook hữu ích để thực hiện hành động khi khởi động. Vì các model đã được nạp khi phương thức này được gọi, đây là nơi an toàn để tương tác với Django.

Nếu bạn cố gắng chạy mã khởi tạo trước khi Django sẵn sàng, và bạn thử làm gì đó như dùng ORM để truy cập dữ liệu cơ sở dữ liệu, bạn có thể sẽ gặp exception `AppRegistryNotReady`. Hầu hết các app sẽ không cần chạy mã khởi động trực tiếp, nhưng biết về hook `ready` là một kiến thức hữu ích nên nhớ.

## Ứng Dụng Trong Hệ Sinh Thái

Một ứng dụng là công cụ quan trọng để nhóm các thành phần logic khác nhau trong dự án của bạn, nhưng app còn phục vụ một mục đích khác. App là nền tảng cho hầu hết các phần mở rộng bên thứ ba trong hệ sinh thái Django.

Một lý do lớn để dùng Django là framework này theo triết lý “batteries included” (tích hợp sẵn mọi thứ cần thiết). Hầu hết các công cụ bạn cần để xây dựng một website đều được tích hợp trực tiếp vào framework. Đây là một cách tiếp cận rất khác so với [Flask](https://flask.palletsprojects.com/en/2.2.x/) vốn cung cấp một API nhỏ và phụ thuộc nhiều vào các thư viện bên thứ ba.

Dù Django tích hợp hầu hết các thành phần lớn cho một ứng dụng web, framework _không_ tích hợp _mọi thứ_. Khi bạn muốn thêm nhiều tính năng hơn, các app Django sẽ lấp đầy những khoảng trống đó.

Trước khi bạn lên PyPI, chúng ta chỉ cần nhìn vào package `django.contrib`, một tập hợp các ứng dụng “đóng góp” được Django cung cấp sẵn. Khi bạn chạy lệnh `startproject`, Django sẽ đưa vào nhiều ứng dụng tích hợp sẵn thực hiện các chức năng khác nhau. Nếu bạn không cần một số chức năng, bạn có thể loại bỏ bằng cách xóa app khỏi danh sách trong `INSTALLED_APPS`.

Tôi nghĩ đây là điểm khác biệt lớn về triết lý của framework. Một số lập trình viên thích bắt đầu từ một nhân tối thiểu và xây dựng dần lên theo nhu cầu. Triết lý của Django dường như là bạn bắt đầu với một nền tảng có sẵn ý kiến và loại bỏ những gì không cần. Django không kỳ vọng bạn sẽ dùng mọi tính năng trong mọi app, nhưng nhiều tính năng bạn cần sẽ sẵn sàng khi bạn cần đến.

Theo quan điểm của tôi, tôi nghĩ triết lý của Django là đúng đắn (ngạc nhiên chưa!? 🤪). Lợi ích của triết lý Django là bạn tận dụng được kiến thức của những người đã xây dựng web app từ lâu. Không chỉ tận dụng kiến thức đó, bạn còn hưởng lợi từ sự trau chuốt của các lập trình viên Django khi tích hợp các hệ thống lớn thành một tổng thể nhất quán. Kết quả là bạn có một framework cảm giác như các thành phần thuộc về nhau, và tôi nghĩ điều đó giúp tăng năng suất của bạn.

Khi bạn xây dựng từ một nhân tối thiểu và phát triển lên, bạn phải biết mọi thứ cần thiết để đưa một thứ gì đó lên web. Điều đó nghĩa là bạn biết tất cả các thành phần và cách kết nối chúng. Nhưng hầu hết mọi người _không_ biết hết các thành phần (vì có quá nhiều!).

Nếu bạn bắt đầu tối giản và không biết hết các thành phần, bạn sẽ học dần trên đường đi, nhưng chuyện gì xảy ra khi bạn gặp một khái niệm mới không khớp với mô hình tư duy ban đầu? Ví dụ, bảo mật là một phần quan trọng có thể phá vỡ mô hình tư duy của bạn khi bạn biết về một lớp lỗ hổng có thể giới hạn những gì có thể làm an toàn. Khi bạn xây dựng từ đầu như vậy, tôi nghĩ kết quả cuối cùng sẽ là framework tùy biến của riêng bạn. Nếu đó là điều bạn thích, tuyệt vời. Hãy làm đi. Với tôi, tôi muốn một framework là hàng hóa và được nhiều người hiểu chung.

Vậy, điều này liên quan gì đến app Django? App là các module đóng gói và có thể tái sử dụng. Vì chúng có cấu trúc khá tiêu chuẩn, một dự án có thể tích hợp một app mới rất nhanh. Điều này nghĩa là bạn có thể tận dụng kiến thức và kinh nghiệm (đọc là: vết sẹo trận mạc) của các lập trình viên web khác. Các app đều tuân theo cùng một quy tắc nên bạn, với tư cách lập trình viên, sẽ tốn ít thời gian để “kết dính” app vào dự án và có nhiều thời gian hơn để hưởng lợi từ chức năng của nó.

Tôi nghĩ cấu trúc tiêu chuẩn này cũng giúp bạn dễ thử nghiệm với các app mới. Khi tôi cần một tính năng mới, tôi thường kiểm tra [Django Packages](https://djangopackages.org/) để tìm các app có thể đáp ứng nhu cầu. Theo kinh nghiệm của tôi, thêm một app mới thường chỉ là cài đặt package, thêm app vào danh sách `INSTALLED_APPS`, và thêm một `include` vào file `urls.py`. Một số package cần cấu hình nhiều hơn, nhưng tôi nghĩ chi phí tích hợp đủ thấp để tôi có thể thử nghiệm nhanh và rút lại quyết định nếu phát hiện app không đáp ứng nhu cầu.

Tóm lại, các ứng dụng Django giúp việc làm việc với hệ sinh thái Django trở nên thú vị hơn.

## Tóm Tắt

Trong bài viết này, chúng ta đã tìm hiểu về các ứng dụng Django.

Chúng ta đã thấy:

- Ứng dụng Django là gì
- Cách một ứng dụng Django được cấu trúc
- Hệ sinh thái Django hưởng lợi từ một định dạng chung tạo ra các thành phần có thể tái sử dụng

Lần tới chúng ta sẽ tìm hiểu về xác thực trong Django. Chúng ta sẽ nghiên cứu:

- Cách tạo và quản lý người dùng
- Cách xử lý quyền cho người dùng
- Cách làm việc với người dùng trong view và template của bạn
