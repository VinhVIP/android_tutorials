# Navigation

Navigation cho phép quản lý điều hướng tương tác của người dùng vào ra các thành phần khác nhau của ứng dụng. Navigation đảm bảo cho trải nghiệm người dùng nhất quán và có thể dự đoán được bằng cách tuân thủ 1 bộ nguyên tắc đã được thiết lập.

# Các thành phần chính của Navigation

1. Navigation Graph: là 1 xml resource chứa tất cả thông tin liên quan đến điều hướng. Hiểu đơn giản thì nó giống như 1 cái bản đồ biểu thị các đường đi có thể có giữa các thành phần giao diện.

2. NavHost: là 1 đối tượng quản lý hiển thị các điểm đến (destination) từ Navigation Graph. Thành phần Navigation triển khai 1 NavHost mặc định, NavHostFragment hiển thị các fragments destination.

3. NavController: là 1 dối tượng quản lý điều hướng bên trong NavHost. NavController sắp xếp việc hoán đổi nội dung đích trong NavHost khi người dùng di chuyển trong ứng dụng.

Navigation cung cấp một số lợi ích như:

- Xử lý fragment transactions
- Xử lý các hành động Up và Back 1 cách chính xác theo mặc định
- Cung cấp tài nguyên cho animations và transisions
- Deep linking
- Safe Args - chuyển dữ liệu an toàn giữa các destinations
- Hỗ trợ ViewModel

# Bắt đầu

## Cài đặt môi trường

Thêm vào build.grade 

```xml
dependencies {
  val nav_version = "2.3.5"

  // Java language implementation
  implementation("androidx.navigation:navigation-fragment:$nav_version")
  implementation("androidx.navigation:navigation-ui:$nav_version")

  // Kotlin
  implementation("androidx.navigation:navigation-fragment-ktx:$nav_version")
  implementation("androidx.navigation:navigation-ui-ktx:$nav_version")

  // Feature module Support
  implementation("androidx.navigation:navigation-dynamic-features-fragment:$nav_version")

  // Testing Navigation
  androidTestImplementation("androidx.navigation:navigation-testing:$nav_version")

  // Jetpack Compose Integration
  implementation("androidx.navigation:navigation-compose:2.4.0-alpha03")
}
```

## Tạo 1 Navtigation Graph

## Thêm NavHost vào Activity

Trong file xml của activity:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.appcompat.widget.Toolbar
        .../>

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"

        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_graph" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        .../>

</androidx.constraintlayout.widget.ConstraintLayout>
```


- `android:name` : chứa tên class mà NavHost triển khai (như trên là mặc định)
- `app:navGraph` : liên kết NavHostFragment với Navigation Graph - chỉ định tất cả các destinations trong NavHostFragment mà người dùng có thể điều hướng.
- `app:defaultNavHost="true"` : đảm bảo rằng NavHostFragment chặn nút Back của hệ thống. Lưu ý rằng chỉ có 1 NavHost có thể là mặc định.


## Điều hướng đến 1 destinations

- Fragment.findNavController()
- View.findNavController()
- Activity.findNavController(viewId: Int)

```java
val navHostFragment =
        supportFragmentManager.findFragmentById(R.id.nav_host_fragment) as NavHostFragment
val navController = navHostFragment.navController
```

## Safe Args

Thêm vào build.gradle top-level

```xml
buildscript {
    repositories {
        google()
    }
    dependencies {
        val nav_version = "2.3.5"
        classpath("androidx.navigation:navigation-safe-args-gradle-plugin:$nav_version")
    }
}
```

Thêm vào build.gradle module app

```xml
plugins {
    id("androidx.navigation.safeargs")
}
```

```java
override fun onClick(view: View) {
    val action =
        SpecifyAmountFragmentDirections
            .actionSpecifyAmountFragmentToConfirmationFragment()
    view.findNavController().navigate(action)
}
```



Fragment A -> B

enterAnim : Anim B xuất hiện khi open từ A
exitAnim: Anim A biến mất khi open từ A
popEnterAnim: Anim A xuất hiện trở lại sau khi back từ B
popExitAnim: Anim B biến mất khi back từ B


popUpTo : màn hình này sẽ bị mất
popUpToInclusive = "true"