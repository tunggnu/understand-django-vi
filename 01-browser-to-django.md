# Từ Trình Duyệt Đến Django

Có thể bạn đã nghe về [Django](https://www.djangoproject.com/) và rằng nó có thể giúp bạn xây dựng website. Bạn có thể mới làm quen với Python, mới phát triển web, hoặc mới lập trình.

Loạt bài mới này, [Hiểu về Django](https://www.mattlayman.com/understand-django/), sẽ cho bạn thấy Django là gì. Xuyên suốt loạt bài này, tôi sẽ chỉ ra Django là một công cụ mạnh mẽ có thể khai phá tiềm năng của bất kỳ ai quan tâm đến việc tạo ứng dụng trên internet. Django được sử dụng bởi các công ty như Instagram, Eventbrite, Disqus, và Udemy, đồng thời cũng là một công cụ tuyệt vời cho cá nhân như bạn.

Chúng ta sẽ tiếp cận Django ở mức độ tổng quan. Thay vì bắt đầu từ dưới lên với tất cả các thành phần của Django, tôi sẽ cho bạn cái nhìn tổng thể, sau đó khám phá từng lớp chi tiết hơn để chỉ ra Django làm được bao nhiêu cho lập trình viên và sức mạnh mà Django có bên trong.

Hãy bắt đầu từ trải nghiệm internet của người dùng: tại trình duyệt web.

1. Từ Trình Duyệt Đến Django
2. [URL Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Nhìn Nhận Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Templates Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. [Tương Tác Người Dùng Với Forms](https://www.mattlayman.com/understand-django/user-interaction-forms/)
6. [Lưu Trữ Dữ Liệu Với Models](https://www.mattlayman.com/understand-django/store-data-with-models/)
7. [Quản Trị Mọi Thứ](https://www.mattlayman.com/understand-django/administer-all-the-things/)
8. [Giải Phẫu Một Ứng Dụng](https://www.mattlayman.com/understand-django/anatomy-of-an-application/)
9. [Xác Thực Người Dùng](https://www.mattlayman.com/understand-django/user-authentication/)
10. [Middleware Đi Đâu?](https://www.mattlayman.com/understand-django/middleware-do-you-go/)
11. [Phục Vụ Tệp Tĩnh](https://www.mattlayman.com/understand-django/serving-static-files/)
12. [Kiểm Thử Ứng Dụng Của Bạn](https://www.mattlayman.com/understand-django/test-your-apps/)
13. [Triển Khai Trang Web Trực Tiếp](https://www.mattlayman.com/understand-django/deploy-site-live/)
14. [Dữ Liệu Theo Từng Khách Truy Cập Với Sessions](https://www.mattlayman.com/understand-django/sessions/)
15. [Hiểu Về Settings](https://www.mattlayman.com/understand-django/settings/)
16. [Sử Dụng Tệp Người Dùng](https://www.mattlayman.com/understand-django/media-files/)
17. [Điều Khiển Ứng Dụng Của Bạn](https://www.mattlayman.com/understand-django/command-apps/)
18. [Tăng Tốc Với Django](https://www.mattlayman.com/understand-django/go-fast/)
19. [Bảo Mật Và Django](https://www.mattlayman.com/understand-django/secure-apps/)
20. [Mẹo Và Kỹ Thuật Gỡ Lỗi](https://www.mattlayman.com/understand-django/debugging-tips-techniques/)

## Tạo Một Yêu Cầu Trình Duyệt

Django là một framework web, nhưng điều đó thực sự có nghĩa là gì? Website hoạt động như thế nào? Tôi sẽ không thể đi qua mọi chi tiết, nhưng bài viết này sẽ đặt nền móng để bạn xây dựng sự hiểu biết của mình. Chúng ta sẽ xem cách trình duyệt web của bạn yêu cầu dữ liệu từ internet và “đường ống” cần thiết để điều đó hoạt động. Được trang bị các từ khóa và từ viết tắt trong chương này, bạn sẽ có thể tự nghiên cứu thêm về các chủ đề này.

Internet hoạt động bằng cách đáp ứng mong muốn gửi và nhận thông tin của người dùng. “Thông tin” đó có nhiều dạng khác nhau. Nó có thể là:

- Video mèo trên YouTube
- Những dòng trạng thái chính trị trên mạng xã hội
- Hồ sơ của người khác trên các trang hẹn hò

Dù mọi người tìm kiếm gì, thông tin đều được truyền qua cùng một cơ chế. Trong ngôn ngữ internet, mọi loại thông tin và dữ liệu đều được gọi là _resource_ (tài nguyên).

Cách chúng ta lấy tài nguyên là thông qua [Uniform Resource Locators](https://en.wikipedia.org/wiki/URL) hay gọi tắt là URL. Bạn biết URL là gì, dù có thể bạn chưa biết tên gọi của nó.

- [https://en.wikipedia.org/](https://en.wikipedia.org/ "smartCard-inline")
- [https://www.djangoproject.com/](https://www.djangoproject.com/ "smartCard-inline")
- [https://www.mattlayman.com/img/django.png](https://www.mattlayman.com/img/django.png)

Đây đều là ví dụ về URL. Thường chúng ta gọi chúng là địa chỉ web vì chúng rất giống với địa chỉ bưu điện. Một URL là địa chỉ của một tài nguyên nào đó trên internet. Khi bạn nhấn _Enter_ trên thanh địa chỉ của trình duyệt, bạn đang nói “Làm ơn trình duyệt, hãy lấy cái này cho tôi.” Nói cách khác, chúng ta tạo ra một _yêu cầu_ từ trình duyệt. Yêu cầu này bắt đầu một chuỗi sự kiện lớn từ trình duyệt của bạn đến website tại URL đó để tài nguyên từ trang web có thể đến với bạn.

Có gì trong chuỗi sự kiện này? _Rất nhiều thứ!_ Chúng ta sẽ lướt qua nhiều lớp trong phần này vì tôi đoán bạn không định tìm hiểu đến mức tín hiệu điện hoạt động thế nào trong dây mạng. Thay vào đó, hãy tập trung vào hai phần chính của chuỗi này: **DNS** và **HTTP**.

### Tên, Tên, Tên

Một URL đại diện cho tài nguyên bạn muốn lấy từ internet. Làm sao internet biết nó đến từ đâu? Đó là lúc DNS xuất hiện. DNS là viết tắt của [Domain Name System](https://en.wikipedia.org/wiki/Domain_Name_System). Từ quan trọng ở đây là “Name” (Tên). Hãy quay lại ví dụ về địa chỉ bưu điện.

Trong một địa chỉ bưu điện (ít nhất là ở Mỹ), có đường, thành phố và bang. Chúng ta có thể viết như sau:

```txt
123 Main St., Springfield, IL
```

Địa chỉ này đi từ hẹp nhất đến rộng nhất. 123 Main St. nằm ở thành phố Springfield, thuộc bang Illinois (IL).

Tương tự, một URL cũng có định dạng như vậy.

```txt
www.example.com
```

Thuật ngữ thì khác, nhưng ý tưởng từ hẹp đến rộng là giống nhau. Mỗi phần giữa các dấu chấm là một loại _domain_ (miền). Hãy xem chúng theo thứ tự ngược lại.

- `com` được coi là [Top Level Domain](https://en.wikipedia.org/wiki/Top-level_domain), TLD. Các TLD được quản lý chặt chẽ bởi một nhóm đặc biệt gọi là [ICANN](https://www.icann.org/).
- `example` là tên miền. Đây là nhận diện chính của một dịch vụ trên internet vì nó là định danh cụ thể mà người dùng có thể nhận ra.
- `www` được coi là _subdomain_ (tên miền phụ) của một domain. Một domain có thể có nhiều subdomain như `www`, `m`, `mail`, `wiki` hoặc bất cứ tên gì chủ sở hữu domain muốn. Subdomain cũng có thể nhiều cấp, nên `a.b.example.com` là hợp lệ, và `a` là subdomain của `b.example.com`, còn `b` là subdomain của `example.com`.

Tên miền _không phải_ là cách máy tính giao tiếp. Tên miền là thứ “thân thiện” cho con người. Hệ thống mạng được thiết kế để làm việc với các con số nên các tên miền phải được chuyển thành thứ mà hệ thống mạng có thể dùng. Để làm điều này, internet sử dụng một hệ thống các máy chủ DNS để làm lớp chuyển đổi giữa tên miền và các con số mà mạng máy tính sử dụng. Một server là máy tính chuyên dụng cung cấp dịch vụ cho các thiết bị khác gọi là client.

Có thể bạn đã từng thấy các con số mạng này. Chúng được gọi là địa chỉ IP, viết tắt của [Internet Protocol](https://en.wikipedia.org/wiki/Internet_Protocol). Một số ví dụ phổ biến:

- `127.0.0.1` là địa chỉ mà máy tính của bạn _tự cấp_ cho chính nó trên mạng nội bộ.
- `192.168.0.1` là địa chỉ mặc định mà router gia đình thường dùng.

Các địa chỉ IP trên là đặc biệt vì chúng nằm trong các [subnetworks](https://en.wikipedia.org/wiki/Subnetwork) được chỉ định riêng, nhưng chúng ta sẽ không bàn sâu về chủ đề đó ở đây. Bạn có thể tự tìm hiểu thêm nếu muốn.

Mạng riêng có các địa chỉ IP như hai ví dụ trên. Máy trên mạng công cộng cũng có địa chỉ IP. Ví dụ, `172.253.115.105` là địa chỉ IP của `www.google.com` tại thời điểm tôi viết bài này.

Nếu bạn muốn biết địa chỉ IP của một tên miền, bạn có thể cài công cụ phổ biến tên là `dig`. Tôi đã tìm địa chỉ IP của Google bằng lệnh sau:

```shell
dig www.google.com
```

Hệ thống sẽ lấy tên miền và giữ một bảng định tuyến phân tán từ tên đến địa chỉ IP trên tập hợp các máy chủ DNS. **Khoan, cái gì vậy?**

Các máy chủ DNS xếp thành một hệ thống phân cấp khổng lồ. Khi trình duyệt của bạn gửi yêu cầu, nó hỏi máy chủ DNS gần nhất với máy của bạn về địa chỉ IP của tên miền bạn yêu cầu. Máy chủ DNS giữ một bảng tra cứu tên miền đến địa chỉ IP trong một khoảng thời gian. Nếu tên miền không có trong bảng, nó có thể hỏi một máy chủ DNS khác trong chuỗi, tiếp tục tìm kiếm địa chỉ IP của tên miền đó. Điều này dẫn đến hai kết quả:

- Nếu không máy chủ nào tìm thấy tên miền, trình duyệt sẽ bỏ cuộc và hiển thị thông báo như “Hmm. Chúng tôi gặp sự cố khi tìm trang web đó.” (từ trang Server Not Found của Firefox).
- Nếu trình duyệt nhận được địa chỉ IP từ máy chủ DNS, nó có thể tiếp tục gửi yêu cầu.

Hệ thống phân cấp này rất lớn, nhưng rộng chứ không sâu. Nói cách khác, có rất nhiều máy tham gia vào DNS (như router nhà bạn), nhưng số lượng liên kết trong chuỗi từ máy tính của bạn lên đến các máy chủ gốc của hệ thống là khá nhỏ.

Đây là phần đơn giản hóa, bỏ qua một số góc cạnh phức tạp của DNS. Trang Wikipedia mà tôi liên kết ở đầu phần này có giải thích chi tiết hơn nhiều nếu bạn muốn tìm hiểu thêm.

### Chúng Ta Đang Gửi Gì?

Phần quan trọng còn lại mà chúng ta cần khám phá là HTTP, hay [Hypertext Transfer Protocol](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol). Đây là phần mô tả cách nội dung được truyền giữa trình duyệt và máy chủ, hoặc nói chung là giữa bất kỳ máy tính nào sử dụng giao thức này.

Giao thức này sử dụng một định dạng chuẩn và một tập hợp các lệnh để giao tiếp. Một số lệnh phổ biến là:

- `GET` - Lấy một tài nguyên có sẵn
- `POST` - Tạo mới hoặc cập nhật một tài nguyên
- `DELETE` - Xóa một tài nguyên
- `PUT` - Cập nhật một tài nguyên

Một yêu cầu HTTP giống như gửi một tệp văn bản qua mạng. Nếu bạn truy cập trang web của tôi tại `https://www.mattlayman.com/about/`, trình duyệt của bạn sẽ gửi một yêu cầu như sau:

```txt
GET /about/ HTTP/1.1
Host: www.mattlayman.com
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

Còn nhiều phần khác tôi đã lược bỏ, nhưng như vậy là đủ để bắt đầu. Dòng đầu tiên cung cấp lệnh, đường dẫn đến tài nguyên cụ thể trên trang (tức là `/about/`), và phiên bản giao thức sử dụng.

Sau dòng đầu tiên là danh sách các _header_. Header là dữ liệu bổ sung cho máy chủ biết thêm về yêu cầu. Header `Host` là bắt buộc vì nó chỉ tên website cần lấy (nhiều website có thể cùng tồn tại trên một địa chỉ IP), còn các header khác là tùy chọn.

Trong ví dụ, tôi cũng hiển thị header `Accept`. Header này cho máy chủ biết trình duyệt có thể nhận loại nội dung nào làm phản hồi. Có các header khác cho máy chủ biết thêm thông tin. Các header này có thể:

- Chỉ ra loại trình duyệt đang gửi yêu cầu (đây là header `User-Agent`).
- Cho biết khi nào tài nguyên được yêu cầu lần trước để xác định có cần trả về phiên bản mới không (header `Last-Modified`).
- Khai báo rằng trình duyệt có thể nhận dữ liệu nén và sẽ giải nén sau khi nhận để tiết kiệm băng thông (header `Accept-Encoding`).

Hầu hết các header được trình duyệt và máy chủ xử lý tự động, nhưng chúng ta sẽ thấy những trường hợp cần tự sử dụng các header này nên biết chúng tồn tại là điều tốt.

## Phục Vụ Một Phản Hồi

Đã đến lúc nói về Django! Giờ chúng ta đã có ý tưởng sơ bộ về những gì trình duyệt làm. Trình duyệt gửi một yêu cầu HTTP đến một URL được hệ thống DNS phân giải. Yêu cầu đó đến một máy chủ kết nối với địa chỉ IP của tên miền. Django sống trên máy chủ đó và chịu trách nhiệm trả lời các yêu cầu bằng một _phản hồi_ HTTP.

Phản hồi là thứ người dùng trình duyệt muốn nhận. Phản hồi có thể là hình ảnh, trang web, video, hoặc bất kỳ định dạng nào mà trình duyệt có thể xử lý.

Trước khi Django có thể xử lý yêu cầu, còn một lớp nữa cần đi qua: máy chủ web Python.

### Nơi HTTP Gặp Python

Máy chủ web là phần mềm trên máy tính được thiết kế để xử lý các yêu cầu HTTP đến. Đôi khi thuật ngữ này gây nhầm lẫn vì mọi người cũng có thể gọi cả _máy_ phục vụ web là “web server”. Ở đây, tôi nói đến chương trình thực sự lắng nghe và phản hồi các yêu cầu web.

Một framework web Python như Django chạy cùng với máy chủ web. Vai trò của máy chủ web là chuyển đổi yêu cầu HTTP thô thành định dạng mà framework hiểu được. Trong thế giới Python, có một định dạng cụ thể để bất kỳ máy chủ web nào cũng có thể nói chuyện với bất kỳ framework web Python nào. Định dạng đó là [Web Server Gateway Interface](https://wsgi.readthedocs.io/en/latest/what.html), hay WSGI (thường được phát âm là “wiz-gee”).

Web Server Gateway Interface

![Web Server Gateway Interface](https://www.mattlayman.com/img/2020/wsgi.jpg)

WSGI cho phép các máy chủ web phổ biến như [Gunicorn](https://gunicorn.org/), [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/), hoặc [mod_wsgi](https://modwsgi.readthedocs.io/en/develop/) giao tiếp với các framework web Python phổ biến như Django, [Flask](https://palletsprojects.com/p/flask/), hoặc [Pyramid](https://trypyramid.com/). Nếu bạn thực sự muốn tìm hiểu sâu, bạn có thể xem chi tiết định dạng đó trong [PEP 3333](https://www.python.org/dev/peps/pep-3333/).

### Nhiệm Vụ Của Django

Khi máy chủ web gửi yêu cầu, Django cần trả về một _phản hồi_. Vai trò của bạn với tư cách là lập trình viên Django là định nghĩa các tài nguyên sẽ có sẵn từ máy chủ. Điều đó nghĩa là bạn phải:

- Mô tả tập hợp các URL mà Django sẽ phản hồi.
- Viết mã cho các URL đó và trả về phản hồi.

Có rất nhiều thứ để bàn trong hai câu này nên chúng ta sẽ khám phá từng chủ đề trong các bài tiếp theo. Đến đây, tôi hy vọng bạn đã hình dung được cách một yêu cầu đi từ trình duyệt đến máy chạy Django.

Vòng đời của một yêu cầu trình duyệt

![Life of a browser request](https://www.mattlayman.com/img/2020/request-response.jpg)

Bài viết này hầu như không có ví dụ mã nguồn, và có lý do cho điều đó. Đã có đủ khái niệm để bạn phải suy nghĩ và tôi không muốn thêm sự phức tạp của mã nguồn vào lúc này. Việc viết mã sẽ là trọng tâm của loạt bài này để chúng ta có thể trả lời các câu hỏi như:

- Làm sao xây dựng trang web và tạo giao diện đồng nhất?
- Làm sao người dùng tương tác với ứng dụng và gửi dữ liệu để ứng dụng phản hồi?
- Django lưu trữ và truy xuất dữ liệu thế nào để làm cho trang web động?
- Ai có thể truy cập ứng dụng và kiểm soát quyền truy cập ra sao?
- Cần thêm bảo mật gì để đảm bảo thông tin người dùng an toàn và riêng tư?

Django có câu trả lời cho tất cả những điều này và còn nhiều hơn nữa. Triết lý của Django là bao gồm tất cả các thành phần cần thiết để tạo một ứng dụng web hoàn chỉnh cho internet. Triết lý “pin kèm theo” này làm Django rất mạnh mẽ. Nhưng chính triết lý này cũng có thể khiến Django trở nên quá tải với người mới. Mục tiêu của tôi trong loạt bài này là giới thiệu từng phần một để bạn hiểu về Django và có thể bắt đầu xây dựng ứng dụng web của riêng mình.

Trong bài tiếp theo, chúng ta sẽ tập trung vào các URL mà ứng dụng sẽ phản hồi. Chúng ta sẽ xem:

- cách khai báo các URL.
- cách nhóm các URL liên quan.
- cách trích xuất thông tin từ URL để sử dụng trong mã trả về phản hồi.

Nếu bạn muốn theo dõi loạt bài này, hãy đăng ký nhận bản tin của tôi, nơi tôi thông báo mọi nội dung mới. Nếu bạn có câu hỏi khác, bạn có thể liên hệ với tôi trên X tại [\@mblayman](https://x.com/mblayman).

Cuối cùng, còn một chủ đề bổ sung nữa…

## Cài Đặt Django

Trong loạt bài này, chúng ta sẽ xem nhiều ví dụ mã nguồn, nhưng sẽ không thiết lập Django từ đầu mỗi lần. Các hướng dẫn thiết lập sau sẽ giúp bạn bắt đầu với mọi ví dụ trong tương lai.

> _Mục tiêu của phần này không phải là mô tả đầy đủ cách thiết lập môi trường Python. Tôi giả định bạn đã biết cách chạy mã Python. Nếu bạn cần hướng dẫn chi tiết hơn, tôi gợi ý bài viết _[_Installing Python 3_](https://training.talkpython.fm/installing-python)_ của Michael Kennedy và bài _[_primer on virtual environments_](https://realpython.com/python-virtual-environments-a-primer/)_ của Real Python. Những bài này giải thích về thiết lập kỹ hơn nhiều so với những gì tôi trình bày ở đây._

Chúng ta sẽ dùng terminal để chạy lệnh. Windows, macOS và Linux hơi khác nhau. Tôi minh họa trên macOS vì đó là hệ điều hành tôi dùng. Dấu đô la (`$`) là ký tự bắt đầu truyền thống cho terminal bash nên khi tôi liệt kê lệnh, bạn không cần gõ ký tự đó. Tôi sẽ cố gắng đưa ra gợi ý và chỉ ra sự khác biệt khi có thể.

Chúng ta cần một nơi để lưu công việc. Vì loạt bài này tên là “Understand Django”, tôi sẽ dùng tên đó. Bạn có thể đặt tên dự án khác nếu muốn.

```shell
mkdir understand-django
cd understand-django
```

Tiếp theo, chúng ta cài Django vào một môi trường ảo để giữ các phụ thuộc của dự án tách biệt với các gói Python khác trên máy. Việc tách biệt này giúp tránh xung đột với các dự án Python khác mà bạn có thể đang chạy trên máy tính.

```shell
python3 -m venv venv
source venv/bin/activate
```

Điều này có thể làm thay đổi dấu nhắc terminal của bạn, giờ sẽ bắt đầu bằng `(venv)` để báo rằng môi trường ảo đang được sử dụng. Các hệ điều hành khác kích hoạt môi trường ảo theo cách khác. Xem [tài liệu module venv](https://docs.python.org/3/library/venv.html) để biết thêm thông tin cho hệ điều hành của bạn.

Giờ bạn có thể cài Django, và mã nguồn framework Django sẽ được thêm vào môi trường ảo.

```shell
(venv) $ pip install Django
```

Django bao gồm một số công cụ giúp chúng ta khởi tạo dự án nhanh chóng. Chúng ta sẽ chạy một lệnh duy nhất để bắt đầu.

```shell
(venv) $ django-admin startproject project .
```

Lệnh này có nghĩa là “khởi tạo một dự án _tên_ ‘project’ trong thư mục hiện tại (`.`).” Việc chọn tên “project” là có chủ ý. `startproject` sẽ tạo một thư mục tên `project` chứa các tệp mà bạn sẽ dùng để cấu hình toàn bộ ứng dụng web. Bạn có thể đặt tên dự án theo ý mình, nhưng tôi thấy dùng tên chung giúp tôi dễ dàng chuyển đổi giữa các ứng dụng Django khác nhau. Tôi luôn biết các tệp liên quan đến dự án nằm ở đâu. Sau khi chạy lệnh, bạn sẽ có một số tệp và cấu trúc như sau:

```shell
(venv) $ ls
manage.py project venv
```

Lưu ý rằng ngoài thư mục `project`, Django còn tạo tệp `manage.py`. Đây là một script giúp bạn tương tác với Django. Bạn sẽ học thêm về `manage.py` ở các phần sau. Để kiểm tra mọi thứ đã hoạt động chưa, hãy thử:

```shell
(venv) $ python manage.py runserver
...
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Khi bạn khởi động máy chủ web, có thể bạn sẽ thấy thông báo như:

```txt
You have ## unapplied migration(s).
Your project may not work properly
until you apply the migrations for app(s):
<a list of names here>
```

Chúng ta sẽ tìm hiểu về migrations sau, nên đừng lo về thông báo này lúc này.

Nếu bạn sao chép và dán URL đó (tức là `http://127.0.0.1:8000/`) vào trình duyệt, bạn sẽ thấy trang chào mừng! Ngoài ra, nếu bạn nhìn lại terminal, bạn sẽ thấy `"GET / HTTP/1.1"`. Thông báo này cho thấy Django đã phản hồi một yêu cầu HTTP. Thật tuyệt!

Điều tiếp theo chúng ta cần là một “app”. Đây là (có thể gây nhầm lẫn) tên của một thành phần Django trong dự án. Điều bạn cần nhớ là một dự án Django _chứa_ một hoặc nhiều app. App sẽ chứa hầu hết mã nguồn bạn cần viết khi làm việc với Django.

Sau khi dừng server, bạn có thể tạo một app để làm việc:

```shell
(venv) $ python manage.py startapp application
```

Lệnh này sẽ tạo ra một bộ tệp khác theo cấu trúc chuẩn của một thành phần ứng dụng Django bên trong thư mục `application`. Ví dụ này dùng tên nhàm chán, nhưng, không giống như `project`, bạn nên chọn tên phù hợp với ứng dụng web của mình (ví dụ, `movies` là tên hay cho ứng dụng về phim). Tất cả các tệp này sẽ được bàn chi tiết ở chủ đề sau.

Cuối cùng, chúng ta phải kết nối app đó vào cài đặt của dự án Django. Cài đặt dự án cho phép bạn cấu hình Django theo nhu cầu. Mở `project/settings.py`, tìm `INSTALLED_APPS` và thêm vào danh sách để nó trông như sau:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'application',
]
```

Như vậy là đủ để bắt đầu với các ví dụ mã nguồn ở bài tiếp theo. `application` sẽ là app tham chiếu của chúng ta. Mã nguồn ở các chủ đề sau không phải là hướng dẫn từng bước, nhưng tôi sẽ dùng `application` để bạn biết các tệp nằm ở đâu trong ứng dụng Django của mình. Chúng ta đã có một dự án Django có thể chạy cục bộ để kiểm thử và đã cấu hình với app đầu tiên. Hẹn gặp lại bạn ở bài tiếp theo để nói về cách tạo URL và tài nguyên!
