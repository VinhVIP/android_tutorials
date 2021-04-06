# RecyclerView trong Android

RecyclerView dùng để xây dựng UI theo dạng danh sách, tương tự với ListView, GridView.

Recycler có thể biểu diễn danh sách theo nhiều kiểu khác nhau: theo chiều đứng, chiều ngang.

RecyclerView là thư viện được hỗ trợ tốt hơn ListView

## Tích hợp thư viện vào build.grade

```xml
implementation 'com.android.support:recyclerview-v7:26.1.0'
```

Khi sử dụng RecyclerView thì chúng ta cũng cần làm việc với:

- `RecyclerView.Adapter` : Quản lý và cập nhật dữ liệu cần hiển thị vào RecyclerView

- `RecyclerView.LayoutManager` : Lớp này quy định cách trình bày các phần tử trong RecyclerView. Có thể sử dụng các lớp kế thừa có sẵn như LinearLayoutManager, GridLayoutManager

- `RecyclerView.ItemAnimator` : Lớp xây dựng các hiệu ứng động animation cho các sự kiện trên các item, ví dụ hiệu ứng thêm xóa phần tử

- `RecyclerView.ViewHolder` : Lớp dùng để gán/cập nhật dữ liệu vào các phần tử

## Sử dụng Adapter thay đổi phần tử trong RecyclerView

Các phương thức Adapter thông baos đến RecyclerView:

- `notifyItemChanged(int position)` : Cho biết phần tử tại vị trí position đã bị thay đổi

- `notifyItemInserted(int position)` : Thông báo phần tử ở vị trí position vừa được thêm vào

- `notifyItemRemoved(int position)` : Thông báo phần tử ở vị trí position vừa bị loại bỏ

- `notifyDataSetChanged()` : Thông báo toàn bộ dữ liệu bị thay đổi

- `notifyItemRangeChanged(int positionStart, int itemCount)` : Thông báo có số lượng phần tử **itemCount** tính từ vị trí **positionStart** bị thay đổi

- `notifyItemRangeInserted(int positionStart, int itemCount)`: Thông báo có số lượng phần tử **itemCount** tính từ vị trí **positionStart** được thêm vào

- `notifyItemRangeRemoved(int positionStart, int itemCount)` : Thông báo có số lượng phần tử **itemCount** tính từ vị trí **positionStart** bị loại bỏ