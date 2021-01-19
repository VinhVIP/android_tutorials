# TextView trong Android
TextView là một View cho phép hiển thị text trên màn hình. TextView có thể tùy chỉnh cỡ chữ, font chữ, màu sắc,...

**Khai báo 1 TextView**

*Trong xml:*

```xml
<TextView
    android:id="@+id/text_view"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="ITMC Android Course" />
```

*Lấy TextView từ layout bằng Java*

```java
TextView textView = findViewById(R.id.text_view);
```

**Một số thuộc tính của TextView**

## 1, ID

ID dùng để phân biệt giữa các View với nhau

```xml
android:id="@+id/text_view"
```

Sau khi tạo thuộc tính id, tên của id sẽ **tự động** được tạo tham chiếu trong class id thuộc class R. Do đó khi muốn lấy đối tượng TextView từ xml layout bằng java, ta sử dụng câu lệnh:

```java
TextView textView = findViewById(R.id.text_view);
```

Phương thức `findViewById` trả về 1 đối tượng View dựa vào resource id của đối tượng ta muốn lấy, View sẽ được tự động ép kiểu ngầm sang TextView.

## 2, Kích thước

`android:layout_width` thiết lập chiều rộng và
`android:layout_height` thiết lập chiều cao cho TextView

Giá trị gồm 3 loại:
 - `match_parent`: kích thước sẽ được mở rộng bằng với kích thước tối đa mà ViewGroup cha có thể chứa.
 - ```wrap_content```: kích thước vừa đủ để vừa với nội dung bên trong của TextView
 - giá trị cụ thể: vd `android:layout_width="100dp"`
 hoặc giá trị lấy từ `dimens.xml`

```xml
<resources>
    <dimen name="text_view_width">100dp</dimen>
</resources>
```

```xml
android:layout_width="@dimen/text_view_width"
```

## 3, Vị trí của chữ trong TextView

`android:gravity` xác định vị trí tương đối của chữ trong TextView, giá trị có thể là `top`, `bottom`, `left`, `right`, `center`, ....

VD: 

```xml
android:gravity="center"
```

Hoặc các giá trị tương ứng có thể kết hợp với nhau:

VD: 

```xml
android:gravity"bottom|right"
```

![Gravity](https://github.com/VinhVIP/blog/blob/gh-pages/img/tv_gravity.png?raw=true)

**Thiết lập bằng Java**

```java
textView.setGravity(Gravity.BOTTOM | Gravity.RIGHT);
```

## 4, Gán text cho TextView

```xml
android:text="Xin chào!"
```

hoặc lấy text từ resource được định nghĩa trong `strings.xml`

```xml
<resources>
    <string name="hello">Xin chào!</string>
</resources>
```

```xml
android:text="@string/hello"
```

**Thiết lập bằng Java**
```java
textView.setText("Xin chào!");
// hoặc lấy String từ resource
textView.setText(R.string.hello);
```

## 5, Thiết lập màu chữ

```xml
android:textColor="#ff0000"
```

hoặc lấy mã màu từ resource được định nghĩa trong `colors.xml`

```xml
<resources>
    <color name="red">#ff0000</string>
</resources>
```

```xml
android:textColor="@color/red"
```

**Thiết lập bằng Java**
```java
// Sử dụng các màu có sẵn được định nghĩa trong lớp Color
textView.setTextColor(Color.RED);

// Hoặc sử dụng mã màu hexa cụ thể
textView.setTextColor(Color.parseColor("#ff0000"));

// Hoặc lấy từ resource
textView.setTextColor(getColor(R.color.red));
```
## 6, Thiết lập kiểu chữ

`android:textStyle` bao gồm 3 giá trị: `bold`, `italic`, `normal`

Các giá trị cũng có thể kết hợp với nhau, vd kiểu chữ đậm và in nghiêng:

```xml
android:textStyle="bold|italic"
```

**Thiết lập bằng Java**
```java
textView.setTypeface(null, Typeface.BOLD_ITALIC);
```

## 7, Thiết lập cỡ chữ

`android:textSize` có thể nhận các giá trị theo đơn vị như sp, dp, px,...

VD:

```xml
android:textSize="18sp"
```

**Thiết lập bằng Java**
```java
// Cỡ chữ 18sp = 18dp
textView.setTextSize(18);

// Cỡ chữ 20 pixels
mytextview.setTextSize(TypedValue.COMPLEX_UNIT_PX , 20); //20px
```

## 8, Thiết lập font chữ

`android:fontFamily` dùng để thiết lập font chữ hiển thị cho text, một số font có sẵn như: `sans-serif`, `monospace`, `casual`, `cursive`, ...

VD:

```xml
android:fontFamily="sans-serif"
```

## 9, Link

Nội dung của TextView có thể là đường dẫn 1 trang web, địa chỉ email, số điện thoại,... TextView cho phép ta hiển thị các link đó nổi bật hơn bằng cách thêm đường kẻ chân.

`android:autoLink` thiết lập các kiểu giá trị nhận để highlight, bao gồm `web`, `phone`, `map`, `email`, `none`, `all`.

`android:linksClickable="true"` cho phép ta click vào đường dẫn link, mặc định giá trị là true.

`android:textColorLink` thiết lập màu sắc của đường dẫn, mặc định là màu colorAccent.

![link trong TextView](https://github.com/VinhVIP/blog/blob/gh-pages/img/tv_link.png?raw=true)

## 10, Drawable

TextView cho phép thiết lập nền là drawable, sử dụng các thuộc tính `android:drawableLeft`, `android:drawableRight`, `android:drawableTop`, `android:drawableBottom` để thiết lập drawable vào bên trái, phải, trên, dưới nội dung text của TextView

Drawable có thể tạo từ Vector Asset hoặc từ code xml sử dụng shape drawable.

VD: Tạo 1 hình chữ nhật 100x15 có màu hồng - `drawable/line.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <size android:height="15dp" android:width="100dp" />
    <solid android:color="#ff00ff" />
</shape>
``` 

Tạo 1 Vector Asset bằng cách click chuột phải vào mục `drawable -> New -> Vector Asset`

![](https://github.com/VinhVIP/blog/blob/gh-pages/img/ic_cloud.png?raw=true)

```xml
android:drawableLeft="@drawable/ic_cloud"
android:drawableRight="@drawable/ic_cloud"
android:drawableTop="@drawable/line"
android:drawableBottom="@drawable/line"
android:drawablePadding="10dp"
```

`android:drawablePadding` dùng để thiết lập khoảng cách giữa drawable là nội dung text.

![Drawable](https://github.com/VinhVIP/blog/blob/gh-pages/img/tv_drawable.png?raw=true)

**Thiết lập bằng Java**
```java
Drawable leftDrawable = getDrawable(R.drawable.ic_cloud);
Drawable topDrawable = getDrawable(R.drawable.line);
Drawable rightDrawable = getDrawable(R.drawable.ic_cloud);
Drawable bottomDrawable = getDrawable(R.drawable.line);
        
textView.setCompoundDrawables(leftDrawable, topDrawable, rightDrawable, bottomDrawable);
```

## 11, Màu nền

`android:background` dùng để thiết lập màu nền cho TextView, background có thể là mã màu hoặc drawable

VD: 

```xml
android:background="#f0f0f0"
```

hoặc: 

```xml
android:background="@drawable/line"
```

**Thiết lập bằng Java**
```java
// sử dụng màu
textView.setBackgroundColor(Color.RED);

// sử dụng resources
textView.setBackgroundResource(R.drawable.line);
// hoặc
textView.setBackground(getDrawable(R.drawable.line));

```

## 12, Margin

`android:layout_margin` dùng để thiết lập căn lề bên ngoài xung quanh của TextView.

Hoặc có thể thiết lập căn lề cho từng phía như `android:layout_marginLeft`, `android:layout_marginRight`, `android:layout_marginTop`, `android:layout_marginBottom`, 
`android:layout_marginVertical`, `android:layout_marginHorizontal`,...

VD: 

```xml
android:layout_margin="10dp"
```

## 13, Padding

`android:padding` dùng để thiết lập khoảng cách giữa viền biên ngoài của TextView với nội dung text bên trong của TextView, có thể thiết lập theo từng phía tương tự với margin

VD: 

```xml
android:padding="10dp"
```

**Thiết lập bằng Java**
```java
// set padding 4 hướng theo pixels
textView.setPadding(int left, int top, int right, int bottom);
```