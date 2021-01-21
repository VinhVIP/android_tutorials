# LinearLayout trong Android

LinearLayout là một ViewGroup mà các View con của nó được sắp xếp 1 cách liên tục theo chiều ngang (*horizontal*) hoặc chiều dọc (*vertical*) dựa theo thuộc tính `android:orientation`.

VD: LinearLayout có chứa 3 Button sắp xếp theo chiều ngang `android:orientation="horizontal"`

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    android:paddingHorizontal="8dp"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 1" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 2" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 3" />

</LinearLayout>
```

![horizontal](https://github.com/VinhVIP/android_tutorials/blob/main/LinearLayout/horizontal.png)

VD: Trường hợp sắp xếp theo chiều dọc `android:orientation="vertical"`

![vertical](https://github.com/VinhVIP/android_tutorials/blob/main/LinearLayout/vertical.png)

## Trọng số weight

Các View con trong LinearLayout có thể gán cho một giá trị trọng số `android:layout_weight`, nếu View không được gán coi như trọng số bằng 0.

Giá trị trọng số sẽ được LinearLayout sử dụng để điều chỉnh kích thước View con, nếu set orientation là `vertical` thì sẽ điều chỉnh chiều cao của View, `horizontal` thì sẽ điều chỉnh chiều rộng của View.

VD: LinearLayout chứa 4 Button, trong đó chỉ có 3 Button là thiết lập trọng số

![3-1](https://github.com/VinhVIP/android_tutorials/blob/main/LinearLayout/layout_weight.png)

Nhận xét:
- View con không gán trọng số thì kích thước sẽ được giữ nguyên
- Các View con còn lại sẽ chia nhau phần không gian dư thừa. Nếu gọi phần không gian còn dư thừa là `size`, tổng các trọng số là `SUM` thì View con có trọng số `weight` sẽ có kích thước là `weight/SUM * size`

## Tổng trọng số

Sử dụng thuộc tính `android:weightSum` của LinearLayout để thiết lập giá trị `SUM` (tổng trọng số) của các View con thuộc LinearLayout. Công thức tính vẫn giữ nguyên

VD: Tương tự trên nhưng LinearLayout có thêm thuộc tính `android:weightSum="6"` 

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingHorizontal="8dp"
    android:weightSum="6"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 1" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="Button 2" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="Button 3" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="2"
        android:text="Button 4" />

</LinearLayout>
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/LinearLayout/weightSum.png)


## Layout bên trong Layout

Ta có thể cho 1 hay nhiều Layout vào bên trong 1 Layout khác.

VD: 1 LinearLayout cha có `andorid:orientation="vertical"` và 1 LinearLayout con có `android:orientation="horizontal"`

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingHorizontal="8dp"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 1" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 2" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Button 3" />

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Button 4" />
    </LinearLayout>


</LinearLayout>
```

![linearlayout inside](https://github.com/VinhVIP/android_tutorials/blob/main/LinearLayout/inside.png)