# CheckBox trong Android

CheckBox là 1 View mở rộng từ CompoundButton.

CompoundButton là 1 lớp abstract mở rộng từ Button, từ lớp này nó được sử dụng để xây dựng các View như: **CheckBox, RadioButton, Switch, ToggleButton**

Các View này có thêm 2 trạng thái là `checked` và `unchecked`

Một số thuộc tính chung:

*Trong xml*

- `android:checked` : thiết lập trạng thái checked ("true") và unchecked ("false")

- `android:button`: gán Drawable các trạng thái cho View

- `android:buttonTint` : gán màu

VD:

```xml
<CheckBox
    android:id="@+id/checkbox_id"
    android:text="ITMC Android"
    android:checked="true"  
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

*Trong Java*

- `isChecked()` : trả về trạng thái hiện tại là true (checked) hay false (unchecked)

- `setOnCheckedChangeListener` : bắt sự kiện khi trạng thái checked/unchecked thay đổi.

```java
button.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
    @Override
    public void onCheckedChanged(CompoundButton compoundButton, boolean b) {
        // b là trạng thái của button sau khi thay đổi
    }
});
```