# LinearLayout trong Android

LinearLayout là một ViewGroup mà các View con của nó được sắp xếp 1 cách liên tục theo chiều ngang hoặc chiều dọc.

VD: LinearLayout có chứa 4 Button sắp xếp theo chiều ngang `android:orientation="horizontal"`

![horizontal]()

VD: Trường hợp sắp xếp theo chiều dọc `android:orientation="vertical"`

![vertical]()

## Trọng số weight

Các View con trong LinearLayout có thể gán cho một giá trị trọng số `android:layout_weight`, nếu View không được gán coi như trọng số bằng 0.

Giá trị trọng số sẽ được LinearLayout sử dụng để điều chỉnh kích thước View con, nếu set orientation là `vertical` thì sẽ điều chỉnh chiều cao của View, `horizontal` thì sẽ điều chỉnh chiều rộng của View.

![3 button có trọng số]()

VD: LinearLayout chứa 4 Button, trong đó chỉ có 3 Button là thiết lập trọng số

![3-1]()

Nhận xét:
- View con không gán trọng số thì kích thước sẽ được giữ nguyên
- Các View con còn lại sẽ chia nhau phần không gian dư thừa. Nếu gọi phần không gian còn dư thừa là `size`, tổng các trọng số là `SUM` thì View con có trọng số `weight` sẽ có kích thước là `weight/SUM * size`

## Tổng trọng số

Sử dụng thuộc tính `android:weightSum` của LinearLayout để thiết lập giá trị `SUM` (tổng trọng số) của các View con thuộc LinearLayout. Công thức tính vẫn giữ nguyên

![]()