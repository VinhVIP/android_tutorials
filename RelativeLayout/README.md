# RelativeLayout trong Android

RelativeLayout là layout mà các View con được định vị dựa vào mối liên hệ giữa các View con với nhau, hoặc giữa View con và layout cha.

Nếu View con không được định vị thì mặc định nó sẽ xuất hiện ở góc phía trên, bên trái màn hình.

## Định vị View con bằng liên hệ với View cha RelativeLayout

Các View con nằm bên trong RelativeLayout có thể được định vị bằng cách thiết lập các mối liên hệ với View cha như sau:

- `android:layout_alignParentLeft`, `android:layout_alignParentRight`, `android:layout_alignParentTop`, `android:layout_alignParentBottom` : căn thẳng cạnh trái, phải, trên, dưới của View con với cạnh tương ứng của View cha.

- `android:layout_centerInParent` : căn View con vào chính giữa View cha

- `android:layout_centerHorizontal`, `android:layout_centerVertical`: căn View con vào giữa View cha theo chiều ngang, dọc.

VD: 

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btn1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 1" />

    <Button
        android:id="@+id/btn2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:text="Button 2" />

    <Button
        android:id="@+id/btn3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true"
        android:text="Button 3" />

    <Button
        android:id="@+id/btn4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="Button 4" />

</RelativeLayout>
```

![parent](https://github.com/VinhVIP/android_tutorials/blob/main/RelativeLayout/parent.png)

## Định vị các View con bằng liên hệ giữa chúng với nhau

View con có thể thiết lập vị trí tương đối với nhau thông qua ID.

**Các thuộc tính:**

- `android:layout_below` : Nằm phía dưới View có ID được chỉ ra

- `android:layout_above` : Nằm phía trên View có ID được chỉ ra

- `android:layout_toLeftOf` : Nằm phía trái View có ID được chỉ ra

- `android:layout_toRightOf` : Nằm phía phải View có ID được chỉ ra

- `android:layout_alignBottom`:  Căn thẳng cạnh dưới với cạnh dưới của View có ID được chỉ ra

- `android:layout_alignLeft` : Căn thẳng cạnh trái với cạnh trái của View có ID được chỉ ra

- `android:layout_alignRight` : Căn thẳng cạnh phải với cạnh phải của View có ID được chỉ ra

- `android:layout_alignTop` : Căn thẳng cạnh trên với cạnh trên của View có ID được chỉ ra

VD: 

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btn1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button 1" />

    <Button
        android:id="@+id/btn2"
        android:layout_width="150dp"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/btn1"
        android:text="Button 2" />

    <Button
        android:id="@+id/btn3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/btn2"
        android:text="Button 3" />

    <Button
        android:id="@+id/btn4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/btn3"
        android:layout_alignRight="@id/btn2"
        android:text="Button 4" />

</RelativeLayout>
```

![children](https://github.com/VinhVIP/android_tutorials/blob/main/RelativeLayout/child.png)