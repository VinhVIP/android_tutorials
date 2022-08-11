# Button trong Android

Button là một View, hiển thị dưới dạng 1 nút bấm. Ví dụ như nút bấm đăng nhập, đăng ký, xác nhận, ...

*Trong xml*

```xml
<Button
	android:id="@+id/btnLogin"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:text="Đăng nhập" />
```

*Lấy Button từ Java code*

```java
Button btnLogin = findViewById(R.id.btnLogin);
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/Button/btn_intro.png?raw=true)

Do Button là 1 View kế thừa TextView nên Button cũng có thừa hưởng hầu hết các thuộc tính mà TextView có.

## Bắt sự kiện click

Mỗi View đều có thể bắt sự kiện click và long click, Button cũng như vậy (vì mục đích sinh ra của nó là để click mà ) bằng cách sử dụng phương thước `setOnClickListener` và tham số là 1 đối tượng khai triển interface `OnClickListener`

```java
Button btnLogin = findViewById(R.id.btnLogin);
btnLogin.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View v) {
        // Những việc cần làm khi click button

        // Hiển thị 1 Toast thông báo rằng Button đã được click
        Toast.makeText(MainActivity.this, "Clicked", Toast.LENGTH_SHORT).show();
    }
});
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/Button/btn_clicked.png?raw=true)

Tương tự với sự kiện long click

```java
btnLogin.setOnLongClickListener(new View.OnLongClickListener() {
	@Override
	public boolean onLongClick(View v) {
		Toast.makeText(MainActivity.this, "Long Clicked", Toast.LENGTH_SHORT).show();
		return true;
	}
});
```

`onLongClick(View v)` trả về kiểu `boolean`, nếu giá trị trả về là:
- `true` : các sự kiện diễn ra sau sự kiện onLongClick sẽ **KHÔNG** được thực thi.
- `false` : các sự kiện diễn ra sau sự kiện onLongClick vẫn sẽ được thực thi như dự kiến, ví dụ như onClick.

## Màu nền

```xml
android:backgroundTint="#d50000"
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/Button/btn_tint.png)

## Viết hoa/thường

Button mặc định viết hoa toàn bộ text, muốn tắt chế độ này ta chỉnh lại thuộc tính `android:textAllCaps="false"`

```xml
<Button
	android:id="@+id/btnLogin"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:textAllCaps="false"
	android:text="Đăng nhập" />
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/Button/btn_no_caps.png)

## Button không cho phép click

Trong một số trường hợp ta không muốn người dùng bấm vào Button vì một số lí do như: phải nhập đầy đủ username, password mới cho phép nhập, còn không thì không cho phép bấm.

Khi này, ta sử dụng thuộc tính `android:enabled="false"` và màu nền của Button sẽ chuyển sang màu xám.

```xml
<Button
	android:id="@+id/btnLogin"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
 	android:enabled="false"
	android:text="Đăng nhập" />
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/Button/btn_enabled.png)

