# EditText trong Android

EditText là 1 View có dạng hình hộp chữ nhật cho phép người dùng nhập liệu, ví dụ nhập họ tên, password,...

```java
public class EditText extends TextView
```

Do EditText là 1 View kế thừa TextView nên EditText cũng sẽ có hầu hết các thuộc tính mà TextView có.

**Khai báo 1 EditText**

*Trong xml*

```xml
<EditText
    android:id="@+id/edit_text"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Xin chào!" />
```

*Lấy từ Java code*

```java
EditText edText = findViewById(R.id.edit_text);
```

# Một số thuộc tính

## 1, Kiểu nhập liệu

`android:inputType` dùng để thiết lập kiểu nhập liệu của EditText, các kiểu nhập liệu như `text`, `number`, `password`, `phone`, `time`, ...

VD:

```xml
android:inputType="text"
```

## 2, Số dòng nhập liệu

Hầu hết các kiểu nhập liệu đều chỉ cho phép nhập trên đúng 1 dòng, ngoại trừ `textMultiLine`.

VD: Chỉ hiển thị 3 dòng nhập liệu

```xml
android:inputType="textMultiLine"
android:lines="3"
```

VD: Chỉ hiển thị 3 dòng nhập liệu, nhưng nếu số dòng nhập liệu <= 2 thì chiều cao của EditText sẽ hiển thị 2 dòng. 

```xml
android:inputType="textMultiLine"
android:lines="3"
android:minLines="2"
```

VD: Hiển thị tối thiểu là 2, tối đa là 4 dòng nhập liệu

```xml
android:inputType="textMultiLine"
android:minLines="2"
android:maxLines="4"
```

**Tóm lại:** `android:lines` thiết lập số dòng nhập liệu ta có thể nhìn thấy trên màn hình, còn `android:maxLines` và `android:minLines` thiết lập chiều cao giới hạn của EditText. Và các thuộc tính này chỉ áp dụng khi `android:inputType="textMultiLine"`

## 3, Giới hạn kí tự nhập vào

Chỉ các kí tự được cho phép được nhập.

VD:

```xml
android:digits="0123abc"
```

## 4, Highlight Color

Thiết lập màu của đoạn text được chọn, mặc định khi không được thiết lập là màu của colorAccent

VD: Thiết lập màu highlight là đỏ

```xml
android:textColorHighlight="#ff0000"
```

![]()


## 5, Bắt sự kiện nội dung EditText thay đổi

Nếu muốn bắt sự kiện khi người dùng nhập liệu vào EditText, giám sát sự thay đổi nội dung của EditText, ta sử dụng phương thức `addTextChangedListener` với tham số là 1 đối tượng `TextWatcher`

```xml
<TextView
	android:id="@+id/text_view_name"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:hint="Nội dung của EditText sẽ hiển thị ở đây"
	android:textSize="18sp" />

<EditText
	android:id="@+id/edit_text_name"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:gravity="top|left"
	android:hint="Nhập họ tên"
	android:inputType="text"
	android:textSize="18sp" />
```

```java
EditText edText = findViewById(R.id.edit_text_name);
TextView tvName = findViewById(R.id.text_view_name);

edText.addTextChangedListener(new TextWatcher() {
	@Override
     public void beforeTextChanged(CharSequence s, int start, int count, int after) {

	}

	@Override
	public void onTextChanged(CharSequence s, int start, int before, int count) {

	}

	@Override
	public void afterTextChanged(Editable s) {
		// Gọi sau khi text thay đổi
		tvName.setText(s.toString());
	}
});
```

![]()

# TextInputLayout và TextInputEditText

TextInputEditText là lớp con mở rộng của EditText, còn TextInputLayout là layout bao bọc bên ngoài TextInputEditText nhằm hiển thị một số thông tin như hint, số lượng kí tự đếm, thông báo lỗi, ...

TextInputEditText và TextInputEditText được triển khai trong thư viện `com.android.support:design` hoặc `com.google.android.material:material`

Để sử dụng, ta cần đảm bảo tích hợp 1 trong 2 thư viện trên vào `build.grade`

VD: Đối với thư viện material

```xml
implementation 'com.google.android.material:material:1.2.1'
```

VD:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="10dp"
    tools:context=".MainActivity">


    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/txtUserLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:counterEnabled="true"
        app:hintEnabled="true"
        app:counterMaxLength="15">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/edUser"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Nhập họ tên"
            android:maxLength="15" />
    </com.google.android.material.textfield.TextInputLayout>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:counterEnabled="true"
        app:counterMaxLength="15"
        app:passwordToggleEnabled="true">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/edPass"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Nhập mật khẩu"
            android:inputType="textPassword"
            android:maxLength="15" />
    </com.google.android.material.textfield.TextInputLayout>


</LinearLayout>
```

`app:hintEnabled="true"` thiết lập cho phép hiển thị hint, mặc định giá trị là `true`

`app:counterEnabled="true"` thiết lập bộ đếm kí tự

`app:counterMaxLength="15"` số kí tự đếm tối đa là 15, bên trong `TextInputEditText` cần phải thiết lập thêm thuộc tính `android:maxLength="15"`

`app:passwordToggleEnabled="true"` thiết lập cho phép ẩn/hiện password thông qua icon con mắt mặc định.

![]()


Để thiết lập thông báo lỗi, ta sử dụng phương thức `setError(String mess)` của `TextInputLayout`. Nếu `mess` là null thì nghĩa là không có lỗi gì cả

```java
		TextInputLayout txtUserLayout = findViewById(R.id.txtUserLayout);
        TextInputEditText edUser = findViewById(R.id.edUser);

        edUser.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {

            }

            @Override
            public void afterTextChanged(Editable s) {
                if (s.toString().length() == 0) {
                    txtUserLayout.setError("User không được bỏ trống!");
                } else {
                    txtUserLayout.setError(null);
                }
            }
        });
```

![]()
