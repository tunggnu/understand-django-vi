# Tương Tác Người Dùng Với Forms

Trong bài viết trước của loạt bài [Hiểu về Django](https://www.mattlayman.com/understand-django/), chúng ta đã thấy cách template của Django hoạt động để tạo ra giao diện người dùng. Điều đó rất ổn nếu bạn chỉ cần hiển thị giao diện, nhưng nếu bạn cần trang web của mình tương tác với người dùng thì sao? Bạn sẽ sử dụng hệ thống form của Django! Trong bài viết này, chúng ta sẽ tập trung vào cách làm việc với các form web bằng hệ thống form của Django.

1. [Từ Trình Duyệt Đến Django](https://www.mattlayman.com/understand-django/browser-to-django/)
2. [URLs Dẫn Đường](https://www.mattlayman.com/understand-django/urls-lead-way/)
3. [Góc Nhìn Về Views](https://www.mattlayman.com/understand-django/views-on-views/)
4. [Template Cho Giao Diện Người Dùng](https://www.mattlayman.com/understand-django/templates-user-interfaces/)
5. Tương Tác Người Dùng Với Forms
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

## Web Forms 101

Trước khi chúng ta đi sâu vào cách Django xử lý form, chúng ta cần hiểu về form HTML nói chung. Chức năng form của Django được xây dựng dựa trên form web nên chủ đề này sẽ không có ý nghĩa nếu bạn không có kiến thức nền tảng về nó.

HTML có thể mô tả loại dữ liệu mà bạn muốn người dùng gửi lên trang web của mình. Việc thu thập dữ liệu này được thực hiện với một vài thẻ. Các thẻ HTML chính cần quan tâm là `form`, `input` và `select`.

Thẻ `form` là vùng chứa cho tất cả dữ liệu mà bạn muốn người dùng gửi đến ứng dụng của mình. Thẻ này có hai thuộc tính quan trọng hướng dẫn trình duyệt cách gửi dữ liệu: `action` và `method`.

`action` sẽ hợp lý hơn nếu được đặt tên là “destination” hoặc “url”. Tuy nhiên, chúng ta phải làm quen với `action`. Thuộc tính này của thẻ `form` là nơi dữ liệu người dùng sẽ được gửi đến. Cũng cần biết rằng nếu bỏ qua `action` hoặc dùng `action=""` thì mọi dữ liệu form sẽ được gửi như một HTTP request đến đúng URL mà trình duyệt người dùng đang ở.

Thuộc tính `method` quyết định HTTP method nào sẽ được dùng và có thể nhận giá trị là `GET` hoặc `POST`. Khi kết hợp với `action`, trình duyệt sẽ biết cách gửi một HTTP request đúng định dạng.

Giả sử chúng ta có ví dụ sau.

```html
<form method="GET" action="/some/form/">
    <input type="text" name="message">
    <button type="submit">Send me!</button>
</form>
```

Khi method của form là `GET`, dữ liệu form sẽ được gửi như một phần của URL trong querystring. Request GET gửi lên server sẽ trông như `/some/form/?message=Hello`. Kiểu gửi form này phù hợp nhất khi chúng ta không cần lưu dữ liệu và chỉ muốn thực hiện một loại truy vấn nào đó. Ví dụ, bạn có thể thêm chức năng tìm kiếm cho ứng dụng với URL như `/search/?q=thing+to+search`. Các liên kết này có thể dễ dàng được bookmark và rất phù hợp cho chức năng như vậy.

Phương thức `POST` để gửi dữ liệu form dùng cho dữ liệu mà chúng ta muốn bảo mật hoặc lưu trữ trong ứng dụng. Với request GET, dữ liệu form trong querystring sẽ bị lộ ở nhiều nơi (xem [thông tin thêm](https://owasp.org/www-community/vulnerabilities/Information_exposure_through_query_strings_in_url) từ OWASP). Ngược lại, POST gửi dữ liệu trong body của HTTP request. Điều này có nghĩa là nếu trang web của bạn bảo mật (tức là dùng HTTPS), dữ liệu sẽ được mã hóa khi truyền từ trình duyệt đến server.

Nếu bạn từng đăng nhập vào một website và gửi mật khẩu qua form, gần như chắc chắn form đó được gửi bằng phương thức POST (và nếu không phải, hãy tránh xa trang đó!).

Chúng ta đã thấy `form` là vùng chứa hướng dẫn cách gửi dữ liệu form. `input` và `select` là các thẻ cho phép chúng ta hiển thị form ý nghĩa cho người dùng.

Thẻ phổ biến hơn là `input`. Với thẻ `input`, tác giả form sẽ chủ yếu đặt thuộc tính `type` và `name`. Thuộc tính `type` báo cho trình duyệt biết loại input nào sẽ được hiển thị.

- Bạn cần một checkbox? `type="checkbox"`
- Bạn cần trường mật khẩu để ẩn ký tự? `type="password"`
- Hoặc một ô nhập văn bản cổ điển? `type="text"`

Bạn có thể xem danh sách đầy đủ các loại tại [tài liệu MDN về input](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input).

Thuộc tính còn lại, `name`, là định danh mà form sẽ ghép với dữ liệu người dùng. Server dùng định danh này để phân biệt các phần dữ liệu mà một lần gửi form có thể bao gồm.

Một thẻ khác mà form của bạn có thể dùng là thẻ `select`. Thẻ này ít phổ biến hơn `input`. Thẻ `select` cho phép người dùng chọn một lựa chọn từ danh sách. Giao diện mặc định của trình duyệt cho thẻ này là một menu thả xuống.

Với các thành phần cốt lõi của form HTML này, chúng ta đã sẵn sàng để hiểu khả năng của form trong Django. Hãy cùng khám phá!

## Django Forms

Các tính năng form của Django đóng vai trò là cầu nối giữa form HTML và các class, kiểu dữ liệu Python. Khi hiển thị form cho người dùng qua view, hệ thống form có thể hiển thị đúng các thẻ HTML form và cấu trúc. Khi nhận dữ liệu form từ người dùng gửi lên, hệ thống form có thể chuyển đổi dữ liệu form thô từ trình duyệt thành dữ liệu Python gốc mà chúng ta có thể sử dụng.

Chúng ta có thể bắt đầu với class `Form`. Một class form đóng vai trò là khai báo dữ liệu mà chúng ta cần từ người dùng. Đây là một ví dụ để chúng ta xem xét.

```python
# application/forms.py

from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(
        max_length=100
    )
    email = forms.EmailField()
    message = forms.CharField(
        max_length=1000
    )
```

- Form do người dùng định nghĩa trong Django nên kế thừa class `Form`. Class này bổ sung rất nhiều chức năng mạnh mẽ sẽ hỗ trợ chúng ta khi tìm hiểu sâu hơn.
- Dữ liệu mà chúng ta muốn thu thập được liệt kê dưới dạng thuộc tính cấp class. Mỗi trường của form là một kiểu trường nhất định với các đặc điểm riêng để giúp chuyển đổi dữ liệu form thô thành kiểu dữ liệu mà chúng ta muốn làm việc trong view.

Nếu chúng ta lấy form này và thêm nó vào context của view dưới tên `form`, chúng ta có thể render nó trong template. Cách render mặc định của form sử dụng một bảng HTML, nhưng chúng ta có thể render các trường theo định dạng đơn giản hơn với phương thức `as_p`. Phương thức này sẽ dùng thẻ đoạn văn cho các phần tử form. Nếu template như sau:

```django
{{ form.as_p }}
```

Thì Django sẽ render ra:

```html
<p><label for="id_name">Name:</label>
  <input type="text" name="name" maxlength="100" required id="id_name"></p>
<p><label for="id_email">Email:</label>
  <input type="email" name="email" required id="id_email"></p>
<p><label for="id_message">Message:</label>
  <input type="text" name="message" maxlength="1000" required id="id_message">
</p>
```

Để có thể gửi form, chúng ta cần bọc phần render này bằng thẻ `form` và thêm nút submit cùng với CSRF token.

Hả? _CSRF token?_ Đáng buồn là thế giới đầy rẫy những kẻ xấu muốn hack ứng dụng của bạn để đánh cắp dữ liệu của người khác. CSRF token là một biện pháp bảo mật mà Django cung cấp để làm cho kẻ xấu khó can thiệp vào dữ liệu form của bạn hơn. Chúng ta sẽ nói thêm về bảo mật ở bài sau. Hiện tại, chỉ cần thêm token này vào form bằng tag template tích hợp của Django là mọi thứ sẽ hoạt động.

```django
<form action="{% url "some-form-url" %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <p><input
        type="submit"
        value="Send the form!"></p>
</form>
```

Đó là cách một form được hiển thị. Bây giờ hãy xem một view xử lý form đúng cách. Khi làm việc với view form, chúng ta thường dùng một view có thể xử lý cả request `GET` và `POST`. Đây là một view đầy đủ để chúng ta phân tích từng phần. Ví dụ này dùng view hàm cho đơn giản, nhưng bạn cũng có thể làm tương tự với view dựa trên class.

```python
# application/views.py

from django.http import HttpResponseRedirect
from django.shortcuts import render
from django.urls import reverse

from .forms import ContactForm

def contact_us(request):
    if request.method == "POST":
        form = ContactForm(request.POST)
        if form.is_valid():
            # Do something with the form data
            # like send an email.
            return HttpResponseRedirect(
                reverse('some-form-success-url')
            )
    else:
        form = ContactForm()

    return render(
        request,
        'contact_form.html',
        {'form': form}
    )
```

Nếu bắt đầu từ nhánh `else`, chúng ta sẽ thấy view này làm rất ít khi nhận request `GET`. Khi HTTP method là `GET`, nó tạo một form rỗng không có dữ liệu truyền vào constructor và render một template với `form` trong context.

`contact_form.html` chứa template Django ở trên để hiển thị form HTML cho người dùng. Khi người dùng nhấn “Send the form!”, một request khác sẽ đến cùng view này, nhưng lần này method là HTTP `POST` và chứa dữ liệu đã gửi.

Request `POST` sẽ tạo một `form`, nhưng có sự khác biệt trong cách khởi tạo. Dữ liệu gửi form được lưu trong `request.POST`, là một đối tượng giống dictionary mà chúng ta đã gặp ở bài về view. Bằng cách truyền `request.POST` vào constructor của form, chúng ta tạo ra một form có dữ liệu. Trong tài liệu Django, bạn sẽ thấy đây được gọi là _bound form_ vì dữ liệu đã được _gắn_ vào form.

Khi form đã sẵn sàng, view sẽ kiểm tra dữ liệu có hợp lệ không. Chúng ta sẽ nói chi tiết về kiểm tra hợp lệ ở phần sau. Trong trường hợp này, bạn có thể thấy `is_valid` sẽ trả về `False` nếu dữ liệu form chứa “I am not an email address” ở trường `email`, chẳng hạn.

- Khi form hợp lệ, view sẽ thực hiện công việc bổ sung được chú thích trong comment và chuyển hướng đến một view mới có thể hiển thị thông báo thành công.
- Khi form không hợp lệ, view sẽ thoát khỏi khối `if` và gọi `render`. Vì dữ liệu đã được gắn vào `form`, form liên hệ sẽ có đủ thông tin để hiển thị trường nào gây ra lỗi khiến form không hợp lệ.

Đó là cốt lõi của xử lý form! View được trình bày là mẫu phổ biến để xử lý view form trong Django. Thực tế, mẫu view này phổ biến đến mức Django cung cấp sẵn một view tích hợp để thực hiện những gì ví dụ trên làm, gọi là `FormView`.

```python
# application/views.py

from django.views.generic import FormView
from django.urls import reverse

from .forms import ContactForm

class ContactUs(FormView):
    form_class = ContactForm
    template_name = 'contact_form.html'

    def get_success_url(self):
        return reverse(
            'some-form-success-view'
        )

    def form_valid(self, form):
        # Do something with the form data
        # like send an email.
        return super().form_valid(form)
```

`FormView` mong đợi một class form và tên template, đồng thời cung cấp một số phương thức để bạn ghi đè cho các vị trí phổ biến mà logic ứng dụng của bạn nên nằm ở đó.

## Trường Form

Sau khi đã nắm được cơ bản về xử lý form, chúng ta có thể chuyển sang các loại trường mà form có thể dùng. Danh sách đầy đủ các trường có trong [tài liệu Django](https://docs.djangoproject.com/en/4.1/ref/forms/fields/), và chúng ta sẽ xem một vài trường phổ biến nhất trong bài này.

Điều đầu tiên cần nhớ về trường form trong Django là chúng chuyển đổi dữ liệu form HTML thành kiểu dữ liệu Python gốc. Nếu bạn kiểm tra dữ liệu của một lần gửi form, bạn sẽ thấy mỗi giá trị về cơ bản là một chuỗi. Nếu Django không làm gì cho bạn, bạn sẽ phải liên tục chuyển đổi sang kiểu dữ liệu mà bạn muốn. Khi làm việc với trường form, việc chuyển đổi dữ liệu đó sẽ được xử lý tự động cho bạn. Ví dụ, nếu bạn chọn `BooleanField`, sau khi form Django được kiểm tra hợp lệ, giá trị trường đó sẽ là `True` hoặc `False`.

Một điều quan trọng khác về trường là chúng gắn với các widget Django cụ thể. Widget là cách kiểm soát Django render gì khi bạn render một form. Mỗi trường form có một loại widget mặc định. Với `BooleanField`, widget mặc định là `CheckboxInput` sẽ render một thẻ `input` với type là `checkbox` (tức là checkbox tiêu chuẩn của form).

Trường là giao điểm quan trọng giữa thế giới trình duyệt và HTML với thế giới Python cùng các kiểu dữ liệu mạnh mẽ của nó.

Bạn sẽ thường dùng trường nào nhất? Và bạn cần thiết lập gì cho các trường đó?

### CharField

`CharField` là “trâu cày” thực sự cho form Django. `CharField` thu nhận input dạng text và dùng thẻ `input` tiêu chuẩn với type là `text`. Nếu bạn muốn thu thập nhiều text hơn, như trong form phản hồi, bạn có thể chuyển từ widget mặc định `TextInput` sang widget `Textarea`. Điều này sẽ khiến form render thẻ `textarea` cho phép nhập nhiều hơn.

```python
# application/forms.py

from django import forms

class FeedbackForm(forms.Form):
    email = forms.EmailField()
    comment = forms.CharField(
        widget=forms.Textarea
    )
```

### EmailField

`EmailField` giống như một phiên bản chuyên biệt của `CharField`. Trường này dùng thẻ `input` với type là `email`. Nhiều trình duyệt hiện đại có thể giúp kiểm tra địa chỉ email hợp lệ. Ngoài ra, khi trường này được kiểm tra hợp lệ trong framework, Django cũng sẽ cố kiểm tra địa chỉ email phòng trường hợp trình duyệt không làm được.

### DateField

`DateField` là một trường khác về cơ bản giống `CharField`. Trường này thậm chí còn dùng type `text` khi render. Sự khác biệt là ở kiểu dữ liệu mà form sẽ cung cấp sau khi kiểm tra hợp lệ. `DateField` sẽ chuyển đổi [nhiều định dạng chuỗi khác nhau](https://docs.djangoproject.com/en/4.1/ref/settings/#datetime-input-formats) thành đối tượng `datetime.date` của Python.

### ChoiceField

`ChoiceField` hữu ích khi bạn muốn người dùng chọn một lựa chọn từ danh sách. Với trường này, chúng ta phải cung cấp một danh sách các lựa chọn mà người dùng có thể chọn. Hãy tưởng tượng chúng ta muốn hỏi người dùng bữa ăn yêu thích nhất trong ngày là gì. Đây là một form có thể làm điều đó.

```python
# application/forms.py

from django import forms

class SurveyForm(forms.Form):
    MEALS = [
        ("b", "Breakfast"),
        ("l", "Lunch"),
        ("d", "Dinner")
    ]
    favorite_meal = forms.ChoiceField(
        choices=MEALS
    )
```

Form này sẽ chứa một thẻ `select` trông như sau:

```html
<p>
  <label for="id_favorite_meal">Favorite meal:</label>
  <select name="favorite_meal" id="id_favorite_meal">
    <option value="b">Breakfast</option>
    <option value="l">Lunch</option>
    <option value="d">Dinner</option>
  </select>
</p>
```

Một vài trường này sẽ đáp ứng hầu hết nhu cầu về form. Hãy khám phá danh sách đầy đủ để trang bị cho mình các loại hữu ích khác.

Các trường form chia sẻ một số thuộc tính chung cho những thứ mà mỗi trường cần.

Thuộc tính `required` là một boolean xác định trường đó có bắt buộc phải có giá trị hay không. Ví dụ, sẽ không hợp lý nếu trang web của bạn có một form hỗ trợ mà bạn dự định dùng để liên hệ với người dùng qua email, nhưng trường `email` trên form lại là tùy chọn.

`label` thiết lập văn bản dùng cho thẻ `label` được render cùng với thẻ `input` của form. Trong ví dụ về bữa ăn, chúng ta có thể dùng thuộc tính `label` để đổi “Favorite meal” thành “What is your favorite meal?” Điều này sẽ tạo trải nghiệm khảo sát tốt hơn nhiều.

Đôi khi form có thể không rõ ràng và người dùng cần trợ giúp. Bạn có thể thêm thuộc tính `help_text` sẽ render thêm văn bản bên cạnh trường form, được bọc trong thẻ `span` với class `helptext` nếu bạn muốn style bằng CSS.

Vì form là một trong những cách chính để người dùng cung cấp thông tin cho ứng dụng của bạn, hệ thống form rất giàu tính năng. Bạn có thể học được rất nhiều về Django bằng cách tìm hiểu sâu phần tài liệu này.

Hãy chuyển sang kiểm tra hợp lệ form vì tôi đã nhắc đến nó vài lần rồi.

## Kiểm Tra Hợp Lệ Form

Trong ví dụ về view, tôi đã trình bày một form gọi phương thức `is_valid`. Ở mức cao, chúng ta có thể hiểu phương thức này làm gì; nó xác định form có hợp lệ hay không.

Nhưng thực sự thì `is_valid` làm gì? Nó làm rất nhiều việc!

Phương thức này xử lý từng trường. Như chúng ta đã thấy, các trường có thể có kiểu dữ liệu cuối cùng (như với `BooleanField`) hoặc cấu trúc mong đợi (như với `EmailField`). Quá trình chuyển đổi kiểu dữ liệu và kiểm tra dữ liệu trường này gọi là cleaning. Thực tế, mỗi trường phải có một phương thức `clean` mà form sẽ gọi khi `is_valid` được gọi.

Khi `is_valid` là `True`, dữ liệu của form sẽ nằm trong một dictionary tên là `cleaned_data` với các key trùng với tên trường được khai báo trong form. Với dữ liệu đã kiểm tra hợp lệ, bạn truy cập `cleaned_data` để làm việc. Ví dụ, nếu chúng ta có tích hợp với hệ thống ticket hỗ trợ, có thể form `FeedbackForm` ở trên sẽ được xử lý trong view như sau:

```python
if form.is_valid():
    email = form.cleaned_data['email']
    comment = form.cleaned_data['comment']
    create_support_ticket(
        email,
        comment
    )
    return HttpReponseRedirect(
        reverse('feedback-received')
    )
```

Khi `is_valid` là `False`, Django sẽ lưu các lỗi tìm thấy trong thuộc tính `errors`. Thuộc tính này sẽ được dùng khi form được render lại trên trang (vì, như bạn nhớ từ ví dụ view, mẫu view form sẽ gửi một bound form trở lại qua lệnh `render` trong trường hợp thất bại).

Một lần nữa, Django đã làm rất nhiều việc nặng nhọc để giúp bạn làm việc với form dễ dàng hơn. Hệ thống _cũng_ cho phép lập trình viên thêm logic kiểm tra hợp lệ tùy chỉnh.

Nếu bạn có một trường form, bạn có thể thêm tùy biến bằng cách viết một phương thức trên class form. Định dạng phương thức phải khớp với tên trường và thêm tiền tố `clean_`. Hãy tưởng tượng chúng ta muốn một website chỉ dành cho những người tên Bob. Để đăng ký, email của bạn phải chứa “bob”. Chúng ta có thể viết một phương thức clean để kiểm tra điều đó.

```python
# application/forms.py

from django import forms

class SignUpForm(forms.Form):
    email = forms.EmailField()
    password = forms.CharField(
        widget=forms.PasswordInput
    )

    def clean_email(self):
        email = self.cleaned_data['email']
        if 'bob' not in email:
            raise forms.ValidationError(
                'Sorry, you are not a Bob.'
            )
        return email
```

Có một vài điểm quan trọng về điều này:

- `clean_email` sẽ chỉ cố gắng kiểm tra trường `email`.
- Nếu kiểm tra hợp lệ thất bại, mã của bạn nên raise một `ValidationError`. Django sẽ xử lý và đưa lỗi vào đúng định dạng trong thuộc tính `errors` của form.
- Nếu mọi thứ đều ổn, hãy chắc chắn trả về dữ liệu đã kiểm tra hợp lệ. Đó là một phần của interface mà Django mong đợi cho các phương thức clean.

Các phương thức `clean_<tên trường>` này là các hook cho phép bạn bổ sung kiểm tra thêm. Hệ thống hook này là nơi hoàn hảo để đặt logic kiểm tra hợp lệ cho dữ liệu đặc thù của ứng dụng bạn. Nhưng nếu cần kiểm tra nhiều dữ liệu liên quan thì sao? Điều này có thể xảy ra khi dữ liệu có mối quan hệ với nhau. Ví dụ, nếu bạn xây dựng một website phả hệ, bạn có thể có form ghi nhận ngày sinh và ngày mất. Bạn có thể muốn kiểm tra các ngày này.

```python
# application/forms.py

from django import forms

class HistoricalPersonForm(forms.Form):
    name = forms.CharField()
    date_of_birth = forms.DateField()
    date_of_death = forms.DateField()

    def clean(self):
        cleaned_data = super().clean()
        date_of_birth = cleaned_data.get('date_of_birth')
        date_of_death = cleaned_data.get('date_of_death')
        if (
            date_of_birth and
            date_of_death and
            date_of_birth > date_of_death
        ):
            raise forms.ValidationError(
                'Birth date must be before death date.'
            )
        return cleaned_data
```

Phương thức này giống với `clean_<tên trường>`, nhưng chúng ta phải cẩn thận hơn. Kiểm tra hợp lệ từng trường sẽ chạy trước, nhưng có thể đã thất bại! Khi phương thức clean thất bại, trường form sẽ bị loại khỏi `cleaned_data` nên chúng ta không thể truy cập trực tiếp bằng key. Phương thức clean kiểm tra xem hai ngày có giá trị không, rồi mới so sánh giữa chúng.

Kiểm tra hợp lệ tùy chỉnh là một tính năng tuyệt vời để nâng cao chất lượng dữ liệu bạn thu thập từ người dùng ứng dụng.

## Tóm Tắt

Đó là cách form giúp bạn thu thập dữ liệu từ người dùng để trang web có thể tương tác với họ. Chúng ta đã xem qua:

- Form web và thẻ HTML `form`
- Class `Form` mà Django dùng để xử lý dữ liệu form trong Python
- Cách form được Django render cho người dùng
- Kiểm soát các trường có trong form
- Cách kiểm tra hợp lệ cho form

Bây giờ chúng ta đã biết cách thu thập dữ liệu từ người dùng, làm sao để ứng dụng lưu trữ dữ liệu đó cho họ? Trong bài tiếp theo, chúng ta sẽ bắt đầu lưu trữ dữ liệu vào cơ sở dữ liệu. Chúng ta sẽ làm việc với:

- Cách thiết lập cơ sở dữ liệu cho dự án của bạn.
- Cách Django sử dụng các class đặc biệt gọi là model để lưu dữ liệu.
- Chạy các lệnh để chuẩn bị cơ sở dữ liệu cho các model bạn muốn dùng.
- Lưu thông tin mới vào cơ sở dữ liệu.
- Truy vấn cơ sở dữ liệu để lấy thông tin đã lưu.
