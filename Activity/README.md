# Activity trong Android

Activity là thành phần rất quan trọng cửa ứng dụng Android, là nơi diễn ra mọi hoạt động tương tác với người dùng.

Có thể xem Activity là một cái màn hình, ở đó ứng dụng sẽ dựng các thành phần giao diện người dùng (UI). Một ứng dụng có thể có nhiều Activity, một Activity có thể gọi và kích hoạt một Activity khác.

Activity là 1 trong 4 thành phần quan trọng của ứng dụng Android gồm: **Activity, Service, Content Provider, Broadcast receivers**


## Vòng đời của Activity

![Activity lifecycle](https://github.com/VinhVIP/android_tutorials/blob/main/Activity/lifecycle.png)

Vòng đời của Activity trải qua nhiều giai đoạn, nhưng để Activity có thể hoạt động thì tối thiểu ta cần override phương thức `onCreate()` , phương thức này được gọi ở giai đoạn đầu tiên của chu kì vòng đời Activity, vì thế ta sẽ nạp giao diện cho nó bằng phương thức `setContentView()`

**Chi tiết từng trạng thái của Activity**

- `onCreate()` : Được gọi bởi khi Activity được tạo(chỉ gọi duy nhất một lần). Tại đây, ta có thể khởi tạo giao diện hoặc dữ liệu để dùng cho sau này. Phương thức này có tham số savedInstanceState chính là trạng thái trước đó của Activity. Mục đích là để ta có thể khôi phục lại trạng thái của Activity

- `onStart()` : Ngay trước khi Activity được chạy và tương tác với người dùng thì hàm được gọi. Thông thường, tại hàm này chúng ta sẽ khởi tạo animation cho UI( nếu có), Audio, hay bất kì đối tượng nào cần thiết để activity có thể hiển thị cho người dùng

- `onResume()` : Hàm này được gọi khi activity chuẩn bị chạy hoặc activity được kích hoạt trở lại từ trạng thái stop (Ví dụ: người dùng ấn phím HOME hoặc có cuộc gọi đến…). Tại hàm này, thường chúng ta sẽ khởi động lại animation, cập nhật UI, hay khởi động lại camera, Audio/Video playback… Nói chung là khỏi động lại tất cả những mà ta đã release ở hàm onPause()

- `onPause()` : Hàm này được gọi khi ứng dụng chuyển sang chạy nền (Tức là ứng dụng không hiển thị trên màn hình). Ngược với onResume(), tại hàm này chúng ta nên stop Animation, Audio/video playback, tạm dừng camera… Lý do đơn giản vì lúc này ứng dụng không hiển thị và tương tác với người dùng nữa.

- `onStop()` : Hàm này được ngay sau onPause(), khi activity không còn tương tác với người dùng. Tại đây, tốt nhất là ta nên tiến hành sao lưu dữ liệu để có thể khôi phục lại khi activity được kích hoạt lại.

- `onRestart()` : Được gọi sau khi activity bị tạm dừng và giờ được kích hoạt trở lại

- `onDestroy()` : Đây là hàm cuối cùng trong vòng đời của activity. Có 2 cách để hàm được gọi: một là bạn gọi hàm finish() để chủ động tắt một activity, hai là được gọi tự động bởi OS khi tài nguyên cạn kiệt. Tại hàm này, bạn nên giải phóng toàn bộ những task nào đang chạy nền, hoặc những đối tượng(object) toàn cục để tránh bị lỗi Memory Leak.

## Cách tạo Activity

Một Activity được tạo ra bằng cách tạo 1 lớp kế thừa (extends) lớp Activity (hoặc AppCompatActivity)

Mỗi Activity sẽ có 1 file xml layout:

`res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal"
    tools:context=".MainActivity">


    <TextView
        android:id="@+id/text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/hello"
        android:textAppearance="@style/TextAppearance.AppCompat.Medium" />

</LinearLayout>
```

`MainActivity.java`

```java
package com.vinh.itmcandroidcourse;

import android.os.Bundle;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        TextView textView = findViewById(R.id.text_view);
    }

}
```

***Giải thích:***

- `setContentView(R.layout.activity_main)` : lệnh này yêu cầu nạp UI có tên là `activity_main`, tương ứng với file `res/layout/activity_main`

- `findViewById(R.id.text_view)` : lệnh này dùng để lấy đối tượng theo resource id truyền vào

![intro](https://github.com/VinhVIP/android_tutorials/blob/main/Activity/intro.png)

## Cấu hình Activity trong AndroidManifest.xml

Để có thể sử dụng 1 Activity thì ta cần phải khai báo nó trong `AndroidManifest.xml`

![manifest](https://github.com/VinhVIP/android_tutorials/blob/main/Activity/manifest_path.png)

```xml
<activity
	android:name=".NewActivity"
	android:label="Tên Activity" >
		
</activity>
```

Để thiết lập Activity là Activity mặc định (chạy đầu tiên sau khi ứng dụng được mở) thì ta thêm `<intent-filter>` vào bên trong phần tử activity đó

```xml
<activity android:name=".MainActivity">
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
</activity>
```


## Lưu và phục hồi trạng thái Activity

Ví dụ ta xây dựng 1 app Counter gồm 2 Button cộng và trừ, 1 TextView hiển thị giá trị đếm hiện tại

`layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btnSubtract"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:backgroundTint="#2196F3"
        android:minWidth="0dp"
        android:text="-"
        android:textSize="20sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/tvCounter"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:gravity="center"
        android:minWidth="40dp"
        android:text="0"
        android:textColor="@color/black"
        android:textSize="20sp"
        android:textStyle="bold" />

    <Button
        android:id="@+id/btnAdd"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:backgroundTint="#2196F3"
        android:minWidth="0dp"
        android:text="+"
        android:textSize="20sp"
        android:textStyle="bold" />

</LinearLayout>
```

`MainActivity.java`

```java
package com.vinh.itmcandroidcourse;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {


    TextView tvCounter;
    Button btnSub, btnAdd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvCounter = findViewById(R.id.tvCounter);
        btnSub = findViewById(R.id.btnSubtract);
        btnAdd = findViewById(R.id.btnAdd);

        btnSub.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = Integer.parseInt(tvCounter.getText().toString());
                count--;
                tvCounter.setText(String.valueOf(count));
            }
        });

        btnAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = Integer.parseInt(tvCounter.getText().toString());
                count++;
                tvCounter.setText(String.valueOf(count));
            }
        });

    }

}
```

Run app và nhấn button `+` lên vài lần

![](https://github.com/VinhVIP/android_tutorials/blob/main/Activity/activity_1.png)

Sau đó, ta thử xoay màn hình

![](https://github.com/VinhVIP/android_tutorials/blob/main/Activity/activity_2.png)

Giá trị counter bị reset về 0, có nghĩa là sau khi ta xoay màn hình thì Activity đã bị hủy (onDestroy()) và sau đó được khởi chạy lại (onCreate()). Điều này khiến cho những dữ liệu của trạng thái trước khi xoay màn hình bị mất đi.

**Cách khắc phục**

Activity có cơ chế lưu lại trạng thái trước đó. Khi Activity đi vào trạng thái `onPause()`, nó sẽ tự động gọi phương thức `onSaveInstanceState(Bundle outState)`, ta cần Override lại phương thức này là lưu lại các giá trị trạng thái vào `Bundle outState`

```java
@Override
protected void onSaveInstanceState(@NonNull Bundle outState) {
	super.onSaveInstanceState(outState);

	int count = Integer.parseInt(tvCounter.getText().toString());
        
	// lưu lại giá trị của bộ đếm với key là "count"
	outState.putInt("count", count);
}
```

`Bundle outState` cho phép lưu dữ liệu theo cặp tên / giá trị bằng các phương thức như: **putInt(), putString(), putFloat(), putIntArray(),...**

***Phục hồi lại trạng thái tại onCreate***

Khi vòng đời mới của Activity diễn ra, các giá trị ta lưu ở `onSaveInstanceState` sẽ quay trở lại với hình thức là tham số `Bundle savedInstanceState` của phương thức `onCreate`. Do đó, ta chỉ cần đọc và phục hồi lại trạng thái của counter.

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvCounter = findViewById(R.id.tvCounter);
        btnSub = findViewById(R.id.btnSubtract);
        btnAdd = findViewById(R.id.btnAdd);

        // Phục hồi dữ liệu
        if(savedInstanceState != null){
            int count = savedInstanceState.getInt("count");
            tvCounter.setText(String.valueOf(count));
        }

        btnSub.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = Integer.parseInt(tvCounter.getText().toString());
                count--;
                tvCounter.setText(String.valueOf(count));
            }
        });

        btnAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = Integer.parseInt(tvCounter.getText().toString());
                count++;
                tvCounter.setText(String.valueOf(count));
            }
        });

    }
```

***Phục hồi lại trạng thái tại onRestoreInstanceState()***

Mỗi khi onCreate được chạy, nếu có trạng thái được lưu thì sau khi gọi onCreate, Activity sẽ tự động gọi phương thức `onRestoreInstanceState(Bundle savedInstanceState)` . Ta cũng có thể Override phương thức này để phục hồi dữ liệu.

```java
public class MainActivity extends AppCompatActivity {

    TextView tvCounter;
    Button btnSub, btnAdd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvCounter = findViewById(R.id.tvCounter);
        btnSub = findViewById(R.id.btnSubtract);
        btnAdd = findViewById(R.id.btnAdd);
        
        btnSub.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = Integer.parseInt(tvCounter.getText().toString());
                count--;
                tvCounter.setText(String.valueOf(count));
            }
        });

        btnAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = Integer.parseInt(tvCounter.getText().toString());
                count++;
                tvCounter.setText(String.valueOf(count));
            }
        });

    }


    @Override
    protected void onSaveInstanceState(@NonNull Bundle outState) {
        super.onSaveInstanceState(outState);

        int count = Integer.parseInt(tvCounter.getText().toString());

        // lưu lại giá trị của bộ đếm với key là "count"
        outState.putInt("count", count);
    }

    @Override
    protected void onRestoreInstanceState(@NonNull Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);

        // đọc giá trị count từ Bundle
        int count = savedInstanceState.getInt("count");
        tvCounter.setText(String.valueOf(count));
    }
}
```