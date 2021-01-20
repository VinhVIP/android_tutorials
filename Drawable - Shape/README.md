# Drawable Shape trong Android

Drawable là một khái niện chung để chỉ đối tượng đồ họa nào đó được vẽ ra màn hình.
Thông thường, tài nguyên drawable được đặt trong thư mục **res/drawable**

## Truy cập đến drawable

Trong xml, ta truy cập đến drawable resource với dạng `"@drawable/filename"`

VD:

```xml
android:background="@drawable/example"
```

## Vẽ ShapeDrawable

Tạo file XML với phần tử root là <shape>, tham số `android:shape` là loại hình muốn vẽ, bao gồm:

- `rectangle`: Hình chữ nhật

- `ring` : Hình khuyên

- `oval` : Hình oval (elip)

- `line` : Đường thẳng

VD:

```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
</shape>
```

Bên trong <shape>, ta có các phần tử con là dữ liệu để vẽ hình như:

### Thiết lâp nét vẽ với stroke

```xml
<stroke
    android:width="10dp"
    android:color="#000"
    android:dashWidth="1dp"
    android:dashGap="10dp" />
```

Trong đó:

- `android:width` : độ rộng của nét vẽ

- `android:color` : màu sắc của nét vẽ

- `android:dashGap` : độ dài khoảng trống để tạo nét đứt

- `android:dashWidth` : độ dài nét đứt

![stroke]()

### Thiết lập tô màu với solid hoặc gradient

Phần tử **solid** dùng để thiết lập 1 màu nền.

```xml
<solid
	android:color="#ff0000" />
```


Phần tử **gradient** dùng tô màu theo gradient - sinh ra bằng cách biến đổi màu liên tục thông qua 3 màu được thiết lập bằng 3 thuộc tính `startColor`, `centerColor`, `endColor`.

Thiết lập kiểu biến đổi bằng thuộc tính `type` với giá trị là `linear` `radial` hoặc `sweep`, mặc định là `linear`

Thiết lập góc biến đổi bằng thuộc tính `angle` với đơn vị là độ


VD: 

```xml
<gradient
	android:startColor="#9C27B0"
    android:endColor="#FFEB3B" />
```

![default]()

VD: angle có giá trị là 45, lưu ý giá trị angle phải bội số của 45

```xml
<gradient
    android:angle="45"
    android:startColor="#9C27B0"
    android:endColor="#FFEB3B" />
```

![angle 45]()

VD: Kiểu biến đổi là `sweep` - vẽ theo chiều kim đồng hồ xung quanh tâm

```xml
<gradient
	android:endColor="#FFEB3B"
	android:startColor="#9C27B0"
	android:type="sweep" />
```

![sweep]()

Sử dụng kiểu biến đổi `radial` cần thiết lập bán kính của vùng tô bằng thuộc tính `android:gradientRadius`

Giá trị có thể là giá trị cụ thể như `100dp` hoặc tỉ lệ `50%p` (50%).

Mặc định tâm của hình tròn là ở chính giữa 

```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient
        android:endColor="#FFEB3B"
        android:gradientRadius="50%p"
        android:startColor="#9C27B0"
        android:type="radial" />
</shape>
```

![radial]()

Để thay đổi tâm hình tròn vẽ, ta thiết lập 2 thuộc tính là `android:centerX` và `android:centerY` nhận giá trị từ **0** đến **1** , ví dụ ở giữa là **0.5**

```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient
        android:centerX="0.7"
        android:centerY="0.3"
        android:endColor="#FFEB3B"
        android:gradientRadius="50%p"
        android:startColor="#9C27B0"
        android:type="radial" />
</shape>
```

![radial centerXY]()


### Thiết lập kích thước vẽ với <size>

```xml
<size 
    android:height="20dp"
    android:width="100dp" />
```

### Thiết lập padding vùng vẽ với <padding>

```xml
<padding
    android:bottom="10dp"
    android:left="10dp"
    android:right="10dp"
    android:top="10dp" />
```

### Thiết lập bo tròn góc với <corners>

Sử dụng thuộc tính `android:radius` để thiết lập bán kính bo tròn 4 góc.

Hoặc thiết lập bo tròn cho từng góc với `android:topLeftRadius`, `android:topRightRadius`, `android:bottomLeftRadius`, `android:bottomRightRadius`

```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient
        android:endColor="#FFEB3B"
        android:gradientRadius="50%p"
        android:startColor="#8BC34A" />
    <size
        android:width="100dp"
        android:height="20dp" />
    <stroke
        android:width="1dp"
        android:color="#673AB7" />
    <corners android:radius="10dp" />
</shape>
```

![corner]()