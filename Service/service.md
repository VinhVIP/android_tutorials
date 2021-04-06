# Service trong Android

Service là 1 trong 4 thành phần của ứng dụng Android, có thể thực hiện các tác vụ cần nhiều thời gian, và không hề có giao diện người dùng.

## Tại sao phải tạo ra Service

Việc xử lý các tác vụ cần nhiều thời gian trên main UI sẽ làm cho ứng dụng dễ bị treo, nên những tác vụ cần nhiều thời gian xử lý thì Service là một lựa chọn hợp lý.

- Service không có UI, không thể giao tiếp trực tiếp với Activity

- Service có thể chạy dưới background ngay khi ứng dụng đã bị tắt.

- Thông thường Service thực hiện công việc đơn lẻ và tự dừng lại khi hoàn thành nhiệm vụ.

Về bản chất, Service vẫn chạy trên main thread của ứng dụng (mỗi khi ứng dụng chạy, Android sẽ tạo 1 thread cho ứng dụng đó). Do đó, nếu Service bị treo thì nó có thể khiến ứng dụng cũng bị treo. Để khắc phục điều này, ta nên tạo 1 Thread nên trong Service.

# 2. Các loại Service 

## 2.1 Unbound Service (không ràng buộc)

Một Activity có thể khởi chạy service bằng `startService()` và không quan tâm Service đó chạy như thế nào, bao giờ thì kết thúc ngay cả khi Activity đã bị destro

Unbound Service có thể chia ra thành 2 loại nhỏ hơn:

- **Foreground Service** : Thực hiện các tác vụ mà người dùng có thể chú ý, nhận biết và **phải hiển thị 1 Notification**. Ví dụ như ứng dụng chơi nhạc cần Notification hiển thị tên bài hát hiện tại, cho phép dừng, chuyển bài hát,...

- **Background Service** : Thực hiện các tác vụ mà người dùng không cần chú ý trực tiếp. 

***Lưu ý:*** Từ Andorid 8.0 :

    - Service chạy trong background bị giới hạn. 

    - Unbound Service mà không có 1 Notification đi kèm thì sẽ không được phép hoạt động khi activity bị destroy.

    - Khởi chạy Service bằng phương thức `startForeGroundService()`

## 2.2 Bound Service (ràng buộc)

Với service loại này thì chúng ta sử dụng `bindService()` để khởi chạy Service

Một Activity khi khởi chạy 1 Service thì sẽ có mối liên kết dạng client-server. Lúc này, Activity có tương tác với Service để gửi và nhận kết quả trả về từ Service.

***Độ ưu tiên:*** `Bound > Foreground > Background`

Khi hệ thống thiếu hụt bộ nhớ thì Background Service dễ bị kill nhất, tiếp đến là Foreground và Bound


# 3. Vòng đời của Service

## 3.1 Unbound Service

`startService() -> onCreate() -> onStartCommand() -> (Service đang chạy) -> onDestroy() -> (Service dừng)`

- Nếu Service đã được tạo thì sẽ gọi trực tiếp tới `onStartCommand()` luông (bỏ qua `onCreate())` , do đó dù start bao nhiêu lần thì cũng chỉ có duy nhất 1 instance của Service được tạo ra.

- Phương thức `onStartCommand()` trả về một giá trị int:

    + `START_STICKY`: Khi hệ thống hết bộ nhớ và bắt đầu kill các service, thông báo cho hệ điều hành tạo lại service sau khi có đủ bộ nhớ và gọi `onStartCommand()` 1 lần nữa với 1 Intent `null`

    + `START_NOT_STICKY`: Hệ điều hành không cần tái tạo lại service khi service đã bị kill trước đó

    + `START_REDELIVER_INTENT`: Tái tạo lại service với 1 Intent là Intent cuối cùng mà Service nhận được (thích hợp với download file)

    + `START_STICKY_COMPATIBILITY`: giống `START_STICKY` nhưng không chắn chắn có thể khởi tạo lại Service

```java
public @StartResult int onStartCommand(Intent intent, @StartArgFlags int flags, int startId) {
                    onStart(intent, startId);
                    return mStartCompatibility ? START_STICKY_COMPATIBILITY : START_STICKY;
 }
 ```

**Để hủy service dạng này, ta gọi:**

- `stopSelf()` : dùng bên trong Service, sau mỗi lần gọi thì *startId* bên trong `onStartCommand(Intent intent, int flags, int startId)` sẽ tăng lên 1 đơn vị.

- `stopSelf(startId)` : dừng Service hiện tại nhưng chỉ khi *startId* trùng với `id` của Service được start cuối cùng.

- `stopService()` : dùng bên ngoài Service

## 3.2 Bound Service

`startService() -> onCreate() -> onBind() -> (Client truy cập Service và Service trả về kq) -> onUnBind() -> onDestroy() -> (Service dừng)`

Ta phải cung cấp 1 giao diện (interface) mà client sử dụng đề giao tiếp với Service bằng cách trả về 1 IBinder. Hàm này được gọi bất kể khi nào có 1 thành phần nào đó muốn bind vào service 

# 4. Tạo Service

* B1: Tạo Service class

```java
onCreate() {
        Log.i(TAG, "Service onCreate");
        isRunning = true;
    }
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.i(TAG, "Service onStartCommand");
        //Creating new thread for my service
        //Always write your long running tasks in a separate thread, to avoid ANR
        new Thread(new Runnable() {
            @Override
            public void run() {
                //Your logic that service will perform will be placed here
                //In this example we are just looping and waits for 1000 milliseconds in each loop.
                for (int i = 0; i < 5; i++) {
                    try {
                        Thread.sleep(1000);
                    } catch (Exception e) {
                    }
                    if(isRunning){
                        Log.i(TAG, "Service running");
                    }
                }
                //Stop service once it finishes its task
                stopSelf();
            }
        }).start();
        return Service.START_STICKY;
    }
    @Override
    public IBinder onBind(Intent arg0) {
        Log.i(TAG, "Service onBind");
        return null;
    }
    @Override
    public void onDestroy() {
        isRunning = false;
        Log.i(TAG, "Service onDestroy");
    }
}
```

* B2: Khai báo Service Manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.javatechig.serviceexample" >
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name=".HelloActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!--Service declared in manifest -->
        <service android:name=".HelloService"
            android:exported="false"/>
    </application>
</manifest>
```

Bạn có thể thiết lập một service chỉ có sẵn cho ứng dụng của bạn bằng cách khai báo thuộc tính `android:exported` và đặt nó thành “false”. Điều này có hiệu quả ngăn các ứng dụng khác bắt đầu service của bạn một cách không kiểm soát.

* B3: Khởi chạy Service

```java
Intent intent = new Intent(this, HelloService.class);
startService(intent);
```