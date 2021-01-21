# CheckBox trong Android

CheckBox là 1 View mở rộng từ CompoundButton.

CompoundButton là 1 lớp abstract mở rộng từ Button, từ lớp này nó được sử dụng để xây dựng các View như: **CheckBox, RadioButton, Switch, ToggleButton**

Các View này có thêm 2 trạng thái là `checked` và `unchecked`

![checked](https://github.com/VinhVIP/android_tutorials/blob/main/CheckBox/cb_checked.png)

![unchecked](https://github.com/VinhVIP/android_tutorials/blob/main/CheckBox/cb_default.png)

Một số thuộc tính chung:

*Trong xml*

- `android:checked` : thiết lập trạng thái checked ("true") và unchecked ("false")

- `android:button`: gán Drawable các trạng thái cho View

- `android:buttonTint` : gán màu cho ô trạng thái, mặc định là màu của colorAccent

VD:

```xml
<CheckBox
	android:id="@+id/check_box"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:checked="true"
	android:buttonTint="#000"
	android:text="ITMC" />
```

![tint](https://github.com/VinhVIP/android_tutorials/blob/main/CheckBox/cb_tint.png)

*Trong Java*

- `isChecked()` : trả về trạng thái hiện tại là true (checked) hay false (unchecked)

- `setOnCheckedChangeListener` : bắt sự kiện khi trạng thái checked/unchecked thay đổi.

```java
CheckBox checkBox = findViewById(R.id.check_box);

checkBox.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
    @Override
    public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
    	// isChecked là trạng thái sau khi CheckBox được nhấn
        Toast.makeText(MainActivity.this, buttonView.getText().toString() + " | "+isChecked, Toast.LENGTH_SHORT).show();
     }
 });
```

![checked change](https://github.com/VinhVIP/android_tutorials/blob/main/CheckBox/cb_listener.png)

## ToggleButton, Switch, RadioButton

- `ToggleButton` : là loại Button chỉ có 2 kiểu trạng thái là ON (true) và OFF (false), không có text hiển thị

- `Switch` : có thêm text hiển thị ở bên trái ô trạng thái. Switch còn có thêm các lớp mở rộng như `SwitchCompat` và `SwitchMaterial`

- `RadioButton`: thường sử dụng kết hợp nhiều RadioButton với nhau, gom thành 1 nhóm được bọc trong `RadioGroup`. Trong nhóm này, chỉ có *duy nhất* 1 RadioButton được *checked*, những RadioButton còn lại sẽ được tự động chuyển sang *unchecked*. Ví dụ như chọn giới tính, chọn năm sinh,....

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="6dp"
    tools:context=".MainActivity">

    <CheckBox
        android:id="@+id/check_box"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:checked="false"
        android:text="ITMC" />

    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true" />

    <Switch
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="false"
        android:text="Dark mode" />

    <RadioGroup
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <RadioButton
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:checked="true"
            android:text="Nam" />

        <RadioButton
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Nữ" />
    </RadioGroup>

</LinearLayout>
```

![tổng họp](https://github.com/VinhVIP/android_tutorials/blob/main/CheckBox/all.png)