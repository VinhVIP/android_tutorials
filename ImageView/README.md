# ImageView trong Android

ImageView là một View dùng để hiển thị hình ảnh.

*Trong xml*

```xml
<ImageView
    android:id="@+id/imgView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:src="@mipmap/ic_launcher" />
```

![intro](https://github.com/VinhVIP/android_tutorials/blob/main/ImageView/img_intro.png)

`android:src` dùng để thiết lập hình ảnh hiển thị cho ImageView. Hình ảnh có thể là 1 bitmap, file hình ảnh, drawable.

VD: Thêm vào `drawable` một file hình ảnh là `facebook.png`

![fb icon](https://github.com/VinhVIP/android_tutorials/blob/main/ImageView/facebook.png)

![fb drawable](https://github.com/VinhVIP/android_tutorials/blob/main/ImageView/db_drawable.png)

Sử dụng phương thước `setImageResource` để thiết lập hình ảnh cho ImageView

```java
ImageView imgView = findViewById(R.id.imgView);

imgView.setImageResource(R.drawable.facebook);
// hoặc
imgView.setImageDrawable(getDrawable(R.drawable.facebook));
```

![](https://github.com/VinhVIP/android_tutorials/blob/main/ImageView/img_fb.png)

## Thay đổi tỉ lệ và vị trí hiển thị

Sử dụng thuộc tính `android:scaleType` để thiết lập chế độ thu phóng hình ảnh.

Các giá trị bao gồm `center`, `centerCrop`, `centerInside`, `fitCenter`, `fitStart`,...

*Sử dụng Java code*

```java
imgView.setScaleType(ImageView.ScaleType.CENTER);
```

## Tự động co biên cho vừa với hình ảnh

```xml
android:adjustViewBounds="true"
```

VD:

```xml
<ImageView
	android:id="@+id/imgView"
	android:layout_width="220dp"
	android:layout_height="400dp"
	android:adjustViewBounds="true"
	android:src="@drawable/facebook" />
```

![image adjust bounds](https://github.com/VinhVIP/android_tutorials/blob/main/ImageView/adjust.png)