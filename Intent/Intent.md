# Intent trong Android

Intent là thành phần quan trọng của Android, cho phép các thành phần yêu cầu chức năng từ các thành phần Android khác. Ví dụ: thông qua Intent, một Activity có thể bắt đầu một Activity khác, hoặc lấy dữ liệu từ một Activity bên ngoài,...

## Phân loại Intent

Intent trong Android chia làm 2 loại: Intent tường minh (explicit) và Intent không tường minh (implicit)

## Intent tường minh

Là những Intent chỉ định rõ ràng tên của các thành phần mục tiêu để nó xử lý.

Ta tìm hiểu về cách sử dụng Intent để mở 1 Activity mới đồng thời gửi dữ liệu qua.

**Bước 1: Tạo 1 Activity mặc định, ở đây ta sử dụng là `MainActivity.java`**

Tại MainActivity, ta tạo 1 EditText và 1 Button mà khi nhấn vào Button này thì màn hình sẽ chuyển sang 1 Activity mới, đồng thời gửi dữ liệu là nội dung text của EditText.

`res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/etMessage"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginVertical="10dp"
        android:hint="Nhập thông điệp"
        android:inputType="text" />

    <Button
        android:id="@+id/btnSend"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Gửi" />


</LinearLayout>
```

`MainActivity.java`

```java
package com.vinh.itmcandroidcourse;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        EditText etMessage = findViewById(R.id.etMessage);
        Button btnSend = findViewById(R.id.btnSend);

        btnSend.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Gọi hàm gửi thông điệp
                sendMessage(etMessage.getText().toString());
            }
        });
    }

    private void sendMessage(String message) {
        // Tạo mới 1 Intent có mục tiêu là SecondActivity
        Intent intent = new Intent(MainActivity.this, SecondActivity.class);

        // Đẩy dữ liệu là nội dung của etMessage vào Intent
        intent.putExtra("mess", message);

        // Khởi chạy Activity theo cài đặt ở Intent
        startActivity(intent);
    }

}
```

**Bước 2: Tạo 1 Activity mới có tên là `SecondActivity.java` và xml layout tương ứng**

SecondActivity có 1 TextView hiển thị dòng chữ dữ liệu mà **MainActivity** gửi qua

`res/layout/activity_second.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    tools:context=".SecondActivity">

    <TextView
        android:id="@+id/tvMessage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:hint="Thông điệp"
        android:textAppearance="@style/TextAppearance.AppCompat.Medium"
        android:textColor="#000" />

</LinearLayout>
```

`SecondActivity.java`

```java
package com.vinh.itmcandroidcourse;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.widget.TextView;

public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        TextView tvMessage = findViewById(R.id.tvMessage);

        // Lấy Intent dữ liệu được gửi qua
        Intent data = getIntent();
        
        // Lấy dữ liệu theo khóa
        String message = data.getStringExtra("mess");

        tvMessage.setText(message);
    }

}
```

![MainActivity]()

![SecondActivity]()


Thông qua ví dụ ta có thể thấy, muốn mở 1 Activity mới, ta chỉ cần tạo 1 Intent, chỉ ra thành phần mục tiêu là tên Activity muốn đi đến, sau đó sử dụng phương thức `startActivity(intent);`

```java
Intent intent = new Intent(MainActivity.this, SecondActivity.class);

startActivity(intent);
```

Để đồng thời gửi dữ liệu vào Intent truyền qua SecondActivity, ta có thể *put* từng dữ liệu vào Intent, hoặc *put* vào 1 đối tượng `Bundle` sau đó *put* Bundle vào Intent.

```java
// Cách 1
intent.putExtra("mess", message);

// Cách 2
Bundle bundle = intent.getExtras();
bundle.putString("mess", message);
intent.putExtras(bundle);

// Cách 3
Bundle bundle = new Bundle();
bundle.putString("mess", message);
intent.putExtras(bundle);
```

Tương ứng ở `SecondActivity` để lấy dữ liệu, ta chỉ cần lấy Intent dữ liệu bằng phương thức `getIntent()`, sau đó *get* dữ liệu ra theo khóa.

```java
Intent data = getIntent();

// Cách 1
String message = data.getStringExtra("mess");

// Cách 2 & 3
Bundle bundle = data.getExtras();
String message = bundle.getString("mess");
```

***Tuy nhiên***, trong thực tế nhiều lúc ta mở 1 Activity mới nhằm mục đích chỉnh sửa dữ liệu được gửi qua. Điều đó có nghĩa khi SecondActivity bị đóng thì nó đồng thời cũng gửi về dữ liệu cho MainActivity.

Ta cùng tìm hiểu cách làm này thông qua ví dụ: 

**Bước 1:** Tại `MainActivity` tạo 1 bộ đếm counter bao gồm 2 Button `-` và `+`, 1 TextView hiển thị giá trị của counter và 1 Button `Gửi` dữ liệu là giá trị của counter sang SecondActivity.

`res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center">

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


    <Button
        android:id="@+id/btnSend"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:text="Gửi" />

</LinearLayout>
```

`MainActivity.java`

```java
package com.vinh.itmcandroidcourse;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    public static final int REQUEST_COUNTER = 69;

    private TextView tvCounter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvCounter = findViewById(R.id.tvCounter);
        Button btnSub = findViewById(R.id.btnSubtract);
        Button btnAdd = findViewById(R.id.btnAdd);
        Button btnSend = findViewById(R.id.btnSend);

        btnSub.setOnClickListener(this);
        btnAdd.setOnClickListener(this);
        btnSend.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        int count = Integer.parseInt(tvCounter.getText().toString());

        switch (v.getId()) {
            case R.id.btnSubtract:
                count--;
                tvCounter.setText(String.valueOf(count));
                break;
            case R.id.btnAdd:
                count++;
                tvCounter.setText(String.valueOf(count));
                break;
            case R.id.btnSend:
                // Gửi giá trị count sang SecondActivity
                sendCounter(count);
                break;
            default:
                break;
        }
    }

    private void sendCounter(int count){
        Intent intent = new Intent(MainActivity.this, SecondActivity.class);

        // Đẩy giá trị count vào intent
        intent.putExtra("count", count);

        // chạy Activity mới với requestCode = REQUEST_COUNTER
        startActivityForResult(intent, REQUEST_COUNTER);
    }


    /*
    Phương thức này được gọi khi màn hình từ SecondActivity quay về MainActivity
     */
    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if(requestCode == REQUEST_COUNTER){
            // Nếu như resultCode là có sự thay đổi giá trị count
            if (resultCode == RESULT_OK){
                // Lấy giá trị count mà SecondActivity trả về
                int newCounterValue = data.getIntExtra("count", 0);

                tvCounter.setText(String.valueOf(newCounterValue));
            }
        }
    }
}
```

**Bước 2:** Tại SecondActivity, ta thiết kế 1 giao diện gồm 1 TextView hiển thị giá trị của counter được gửi qua từ MainActivity, 1 Button `+5` mà khi nhấn thì sẽ quay về MainActivity đồng thời gửi trả giá trị là **count + 5**, 1 Button `Cancel` khi nhấn thì sẽ quay trở về MainActivity mà không làm thay đổi giá trị count.

`res/layout/activity_second.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    tools:context=".SecondActivity">

    <TextView
        android:id="@+id/tvSecondCounter"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:hint="0"
        android:textAppearance="@style/TextAppearance.AppCompat.Medium"
        android:textColor="#000" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="20dp"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnAddFive"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginRight="20dp"
            android:text="+5" />

        <Button
            android:id="@+id/btnCancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Cancel" />
    </LinearLayout>


</LinearLayout>
```

`SecondActivity.java`

```java
package com.vinh.itmcandroidcourse;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class SecondActivity extends AppCompatActivity {

    private TextView tvSecondCounter;
    private Button btnAddFive, btnCancel;

    private int count;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        tvSecondCounter = findViewById(R.id.tvSecondCounter);
        btnAddFive = findViewById(R.id.btnAddFive);
        btnCancel = findViewById(R.id.btnCancel);

        // Lấy dữ liệu được gửi từ MainActivity
        getIntentData();

        btnAddFive.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                count += 5;
                // Tạo mới 1 Intent để đẩy dữ liệu vào
                Intent reIntent = new Intent();
                reIntent.putExtra("count", count);

                // Thiết lập giá trị trả về là RESULT_OK và Intent dữ liệu trả về là reIntent
                setResult(RESULT_OK, reIntent);

                // Đóng Activity hiện tại
                finish();
            }
        });

        btnCancel.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                finish();
            }
        });

    }

    private void getIntentData() {
        Intent data = getIntent();
        count = data.getIntExtra("count", 0);
        tvSecondCounter.setText(String.valueOf(count));
    }

}
```

Ta tiến hành kiểm tra kết quả.

Mở app lên, tăng giá trị Counter lên 5

![counter 5]()

Sau đó nhấn button `Gửi`

![second]()

***Trường hợp 1:*** Nhấn button `+5`

![btn+5]()

***Trường hợp 2:*** Nhấn button `Cancel`

![cancel]()

***Trường hợp 3:*** Nhấn nút Back

![back]()

**Nhận xét:**

Qua ví dụ trên ta có thể thấy:

- `startActivityForResult(intent, REQUEST_COUNTER);` : lệnh này giúp cho 2 Activity có thể gửi dữ liệu qua lại cho nhau, đồng thời chỉ khi có phương thức này được sử dụng thì khi SecondActivity đóng, phương thức `onActivityResult` mới tự động được gọi.

- `onActivityResult(int requestCode, int resultCode, @Nullable Intent data)` : Đây là phương thức tự động được gọi sau khi SecondActivity đóng. Trong đó:

	- `requestCode` : dùng để phân biệt giữa các request `startActivityForResult`, bởi vì từ 1 Activity ta có thể gửi nhiều loại request đến nhiều Activity khác nhau.

	- `resultCode` : là kết quả trả về của request tương ứng. Có 2 giá trị có sẵn thường được sử dụng là `RESULT_OK` và `RESULT_CANCELED`. Ta có có thể tự định nghĩa các hằng số giá trị này tưng tự như ta đinh nghĩa request với `public static final int REQUEST_COUNTER = 69;`

	- `Intent data` : Là data được trả về, theo ví dụ là `reIntent` từ SecondActivity trong trường hợp ta nhân button `+5`, nếu không có dữ liệu trả về thì Intent data có giá trị là `null`

- `setResult(RESULT_OK, reIntent);` : phương thức này trả về kết quả là `RESULT_OK` và dữ liệu là `reIntent` cho MainActivity xử lý.

- `finish()` : Đóng ngay lập tức Activity hiện hành.

**Để ý:** Khi ta nhấn button `Cancel` hay nhấn phím cứng `Back` thì kết quả đều như nhau, điều đó có nghĩa là khi nhấn phím `Back` thì cũng sẽ không có dữ liệu gì trả về cho MainActivity (*RESULT_CANCELED*). Tuy nhiên trong một số trường hợp, ta muốn kiểm soát phím `Back` để làm 1 điều gì đấy, ví dụ như cho giá trị *count - 1* thì ta override lại phương thức `onBackPressed()` bên trong **SecondActivity.java**

```java
@Override
public void onBackPressed() {
	count--;
	Intent reIntent = new Intent();
	reIntent.putExtra("count", count);
	setResult(RESULT_OK, reIntent);
	finish();
}
```

