# FrameLayout trong Android

FrameLayout là loại Layout đơn giản, mục đích chính khi sử dụng FrameLayout là nhằm cho các View con nằm chồng lên nhau, View được đưa vào sau khi hiển thị sẽ đè lên View đưa vào trước.

## Điều chỉnh vị trí của View con

Sử dụng thuộc tính `android:layout_gravity` để xác định vị trí hiển thị của View con trong FrameLayout, kết hợp với các thuộc tính liên quan đến ***margin*** để điều chỉnh vị trí cho hợp lý.

Các giá trị của thuộc tính `layout_gravity` bao gồm: `top`, `bottom`, `left`, `right`, `center`, `center_horizontal`, `center_vertical`, `start`, `end`.

Các giá trị có thể kết hợp với nhau thông qua toán tử `|`

VD: 

![]()