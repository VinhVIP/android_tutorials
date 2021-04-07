# ListView trong Android

ListView là một ViewGroup hiển thị các item theo 1 danh sách, có thể cuộn theo chiều dọc.

## Adapter

Adapter là cầu nối giữa các View và các *dữ liệu* kết nối cơ bản của View đó

## 1, ListView cơ bản nhất với ArrayAdapter

ArrayAdapter là một adapter mà các item của nó làm từ duy nhất 1 TextView, hoặc 1 CheckedTextView, hoặc EditText, ...

Android xây dựng 1 số layout cơ bản để làm việc với với ArrayAdapter như:

- `android.R.layout.simple_list_item_1` : Layout chỉ được tạo duy nhất bởi 1 TextView

- `android.R.layout.simple_list_item_checked` : Layout chỉ có 1 CheckedTextView

- `android.R.layout.simple_list_item_multiple_choice` : Tương tự layout trên

## 2, Tùy biến ListItem sử dụng BaseAdapter

