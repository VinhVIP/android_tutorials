# RelativeLayout trong Android

RelativeLayout là layout mà các View con được định vị dựa vào mối liên hệ giữa các View con với nhau, hoặc giữa View con và layout cha.

## Định vị View con bằng liên hệ với View cha RelativeLayout

Các View con nằm bên trong RelativeLayout có thể được định vị bằng cách thiết lập các mối liên hệ với View cha như sau:

- `android:layout_alignParentLeft`, `android:layout_alignParentRight`, `android:layout_alignParentTop`, `android:layout_alignParentBottom` : căn thẳng cạnh trái, phải, trên, dưới của View con với cạnh tương ứng của View cha.

- `android:layout_centerInParent` : căn View con vào chính giữa View cha

- `android:layout_centerHorizontal`, `android:layout_centerVertical`: căn View con vào giữa View cha theo chiều ngang, dọc.

VD: 

```xml

```

![]()

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

```

![]()