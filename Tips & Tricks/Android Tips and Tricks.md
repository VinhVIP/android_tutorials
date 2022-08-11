# Android Tips and Tricks

## 1. Internet Status Observer

Tạo 1 lớp mở rộng từ LiveData, khi có sự thay đổi trạng thái kết nối internet thì LiveData sẽ thông báo

```kotlin

import android.app.Application
import android.content.ContentValues
import android.content.Context
import android.net.ConnectivityManager
import android.net.Network
import android.net.NetworkCapabilities
import android.net.NetworkRequest
import android.util.Log
import androidx.lifecycle.LiveData

class LiveDataInternetConnections(private val connectivityManager: ConnectivityManager) :
    LiveData<Boolean>() {

    constructor(appContext: Application) : this(
        appContext.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
    )

    private val networkCallback = object : ConnectivityManager.NetworkCallback() {

        override fun onAvailable(network: Network) {
            super.onAvailable(network)
            Log.d(ContentValues.TAG, "onAvailable: Network ${network} is Available")
            postValue(true)
        }

        override fun onCapabilitiesChanged(
            network: Network,
            networkCapabilities: NetworkCapabilities
        ) {
            val isInternet =
                networkCapabilities.hasCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET)
            Log.d(ContentValues.TAG, "networkCapabilities: ${network} $networkCapabilities")
            val isValidated =
                networkCapabilities.hasCapability(NetworkCapabilities.NET_CAPABILITY_VALIDATED)
            if (isValidated) {
                Log.d(ContentValues.TAG, "hasCapability: ${network} $networkCapabilities")
            } else {
                Log.d(
                    ContentValues.TAG,
                    "Network has No Connection Capability: ${network} $networkCapabilities"
                )
            }
            postValue(isInternet && isValidated)
        }

        override fun onLost(network: Network) {
            super.onLost(network)
            Log.d(ContentValues.TAG, "onLost: ${network} Network Lost")
            postValue(false)
        }
    }

    override fun onActive() {
        super.onActive()
        val builder = NetworkRequest.Builder()
        connectivityManager.registerNetworkCallback(
            builder
                .addCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET)
                .build(), networkCallback
        )
    }

    override fun onInactive() {
        super.onInactive()
        connectivityManager.unregisterNetworkCallback(networkCallback)
    }
}
```


## 2. RecyclerView SnappingLinearLayoutManager

Mặc định khi cho RecyclerView scroll đến vị trí position bằng lệnh `scrollToPosition` hoặc `smoothScrollToPosition` thì RecyclerView sẽ chỉ scroll cho item đó được hiển thị lên màn hình

Nếu muốn item đó xuất hiện trên cùng (đối với layout dọc) thì cần phải custom lại LinearLayoutManager như sau

**SnappingLinearLayoutManager**

```kotlin
import android.content.Context
import android.graphics.PointF
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.LinearSmoothScroller
import androidx.recyclerview.widget.RecyclerView
import androidx.recyclerview.widget.RecyclerView.SmoothScroller

class SnappingLinearLayoutManager(context: Context?, orientation: Int, reverseLayout: Boolean) :
    LinearLayoutManager(context, orientation, reverseLayout) {

    override fun smoothScrollToPosition(
        recyclerView: RecyclerView, state: RecyclerView.State,
        position: Int
    ) {
        val smoothScroller: SmoothScroller = TopSnappedSmoothScroller(recyclerView.context)
        smoothScroller.targetPosition = position
        startSmoothScroll(smoothScroller)
    }

    private inner class TopSnappedSmoothScroller(context: Context?) :
        LinearSmoothScroller(context) {
        override fun computeScrollVectorForPosition(targetPosition: Int): PointF? {
            return this@SnappingLinearLayoutManager
                .computeScrollVectorForPosition(targetPosition)
        }

        override fun getVerticalSnapPreference(): Int {
            return SNAP_TO_START
        }
    }
}
```


Sử dụng:

```kotlin
recyclerView.adapter = SnappingLinearLayoutManager(context, LinearLayoutManager.VERTICAL, false)
```

## 3. Tạo ItemDivider cho RecyclerView

```kotlin
import android.content.Context
import android.graphics.Canvas
import android.graphics.drawable.Drawable
import android.view.View
import androidx.annotation.DrawableRes
import androidx.core.content.ContextCompat
import androidx.recyclerview.widget.RecyclerView

class SimpleDividerItemDecoration(context: Context, @DrawableRes dividerRes: Int) :
    RecyclerView.ItemDecoration() {

    private val mDivider: Drawable = ContextCompat.getDrawable(context, dividerRes)!!

    override fun onDrawOver(c: Canvas, parent: RecyclerView) {
        val left = parent.paddingLeft
        val right = parent.width - parent.paddingRight
        val childCount = parent.childCount
        for (i in 0 until childCount) {
            val child: View = parent.getChildAt(i)
            val params = child.layoutParams as RecyclerView.LayoutParams
            val top: Int = child.bottom + params.bottomMargin
            val bottom = top + mDivider.intrinsicHeight
            mDivider.setBounds(left, top, right, bottom)
            mDivider.draw(c)
        }
    }
}
```

Cách dùng:

```kotlin
recyclerView.addItemDecoration(SimpleDividerItemDecoration(requireContext(), R.drawable.divider))
```


## 4. Notification

```kotlin
	private fun showNotification(
        context: Context,
        title: String,
        content: String,
        smallBitmap: Bitmap?
    ) {
        val resultIntent = Intent(context, SplashActivity::class.java)
        val resultPendingIntent: PendingIntent? =
            TaskStackBuilder.create(context).run {
                addNextIntentWithParentStack(resultIntent)
                getPendingIntent(
                    0,
                    PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_ONE_SHOT or PendingIntent.FLAG_IMMUTABLE
                )
            }

        val sound = Uri.parse("android.resource://" + context.packageName + "/" + R.raw.sound)

        val notification =
            NotificationCompat.Builder(context, MyApplication.CHANNEL_ID)
                .setVisibility(NotificationCompat.VISIBILITY_PUBLIC)
                .setSmallIcon(R.drawable.ic_notfound)
                .setContentIntent(resultPendingIntent)
                .setContentTitle(title)
                .setContentText(content)
                .setPriority(NotificationCompat.PRIORITY_MAX)
                .setLargeIcon(smallBitmap)
                .setStyle(
                    NotificationCompat.BigPictureStyle()
                        .bigPicture(smallBitmap)
                        .bigLargeIcon(null)
                )
                .setAutoCancel(true)
                .setSound(sound)

        NotificationManagerCompat.from(context).notify(123, notification.build())
    }

```

## 5. Date

### 5.1 Chuyển Date từ UTC sang timezone của người dùng

```koltin
fun Date.toLocalDate(): Date {
    return Date(this.time + Calendar.getInstance().timeZone.getOffset(Date().time))
}
```

### 5.2 Chuyển String sang Date

```koltin
fun String.toDate(format: String = "yyyy-MM-dd HH:mm:ss"): Date {
    val sdf = SimpleDateFormat(format)
    return sdf.parse(this).toLocalDate()
}
```

## 6. RecyclerView Horizontal Scroll

Khi sử dụng RecyclerView nằm ngang, mà view cha bọc bên ngoài là ViewPager (hoặc 1 ViewGroup Swipable) thì ta cần chặn sự kiện swipe của view cha khi scroll RecyclerView

```kotlin
fun RecyclerView.setHorizontalScroll() {
    this.addOnItemTouchListener(object : RecyclerView.OnItemTouchListener {
        override fun onInterceptTouchEvent(rv: RecyclerView, e: MotionEvent): Boolean {
            when (e.action) {
                MotionEvent.ACTION_MOVE -> {
                    rv.parent.requestDisallowInterceptTouchEvent(true)
                }
            }
            return false
        }

        override fun onTouchEvent(rv: RecyclerView, e: MotionEvent) {
        }

        override fun onRequestDisallowInterceptTouchEvent(disallowIntercept: Boolean) {
        }
    })
}
```

## 7. Kích thước màn hình

```koltin

import android.content.res.Resources

object Screen {
    val width: Int
        get() = Resources.getSystem().displayMetrics.widthPixels

    val height: Int
        get() = Resources.getSystem().displayMetrics.heightPixels

    fun convertDpToPixel(dp: Float, context: Context): Float {
        return dp * (context.resources.displayMetrics.densityDpi.toFloat() / DisplayMetrics.DENSITY_DEFAULT)
    }

    fun convertPixelsToDp(px: Float, context: Context): Float {
        return px / (context.resources.displayMetrics.densityDpi.toFloat() / DisplayMetrics.DENSITY_DEFAULT)
    }
}
```

## 8. Đọc text từ 1 file văn bản ở raw folder

```kotlin
    private fun readTextRaw(context: Context) {
        val inputStream = context.resources.openRawResource(R.raw.file)
        val text = readJson(inputStream)
    }

    fun readJson(inputStream: InputStream): String {
        return try {
            val bytes = ByteArray(inputStream.available())
            inputStream.read(bytes, 0, bytes.size)
            String(bytes)
        } catch (e: IOException) {
            ""
        }
    }
```

## 9. Get Bitmap From URL

```kotlin
    fun getBitmapFromUrl(imageUrl: String): Bitmap? {
        if (imageUrl.isEmpty()) return null

        return try {
            val url = URL(imageUrl)
            val connection: HttpURLConnection = url.openConnection() as HttpURLConnection
            connection.doInput = true
            connection.connect()
            val input: InputStream = connection.inputStream
            BitmapFactory.decodeStream(input)
        } catch (e: Exception) {
            e.printStackTrace()
            null
        }
    }
```

## 10. Intent

```kotlin
    fun openIntent(context: Context, packageName: String) {
        try {
            context.startActivity(Intent(Intent.ACTION_VIEW, Uri.parse(packageName)))
        } catch (e: ActivityNotFoundException) {
        }
    }

    fun shareText(context: Context, text: String, shareBody: String = "") {
        val intent = Intent(Intent.ACTION_SEND)
        intent.type = "text/plain"
        intent.putExtra(Intent.EXTRA_SUBJECT, text)

        intent.putExtra(Intent.EXTRA_TEXT, text)
        context.startActivity(Intent.createChooser(intent, shareBody))
    }

    fun sendEmail(context: Context) {
        val email = Intent(Intent.ACTION_SEND)
        email.putExtra(Intent.EXTRA_EMAIL, arrayOf(APP_CONFIG.contactInfo.emailAddress))
        email.putExtra(Intent.EXTRA_SUBJECT, "")
        email.putExtra(Intent.EXTRA_TEXT, APP_CONFIG.contactInfo.emailContent)

        email.type = "message/rfc822"

        context.startActivity(Intent.createChooser(email, "Choose an Email client :"))
    }
```

## 11. Lấy VersionName hiện tại của app

```koltin
    fun getVersionName(context: Context): String {
        val manager = context.packageManager
        val info = manager.getPackageInfo(context.packageName, PackageManager.GET_ACTIVITIES)
        return info.versionName
    }
```

## 12. Show/Hide Keyboard

```kotlin
fun EditText.showSoftKeyboard() {
    val imm = context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager?
    imm!!.showSoftInput(this, InputMethodManager.SHOW_IMPLICIT)
}

fun EditText.hideSoftKeyboard() {
    val imm: InputMethodManager? =
        context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager?
    imm?.hideSoftInputFromWindow(this.windowToken, 0)
}
```

Cách dùng: Sau khi requestFocus() EditText, ta cần delay 1 khoảng thời gian rồi mới cho hiện bàn phím, nếu không delay thì sẽ không thể hiện bàn phím được

```koltin
lifecycleScope.launch(Dispatchers.Main) {
	editText.requestFocus()
	delay(200)
	edSearch.showSoftKeyboard()
}
```


## 13. Banner Admob

Banner Admob cần gắn cứng adUnitId khi mới tạo layout, do đó nếu ta định tạo banner admob trong xml thì phải đưa đúng adUnitId vào xml.

Trong 1 số trường hợp ta muốn giá trị adUnitId được gán sau, thì bắt buộc phải tạo AdView bằng code

Ví dụ áp dụng với ViewGroup là ConstraintLayout

```kotlin
    private fun addAdViewLayout() {
        val constraintLayout = binding.root
        val constraintSet = ConstraintSet()
        constraintSet.clone(constraintLayout)

        val mAdView = AdView(this)
        mAdView.setAdSize(AdSize.BANNER)
        mAdView.adUnitId = APP_CONFIG.admob.bannerID	// giá trị adUnitID muốn set

        mAdView.id = View.generateViewId()
        constraintLayout.addView(mAdView)

        constraintSet.apply {
            connect(mAdView.id, ConstraintSet.BOTTOM, binding.tvOffline.id, ConstraintSet.TOP, 0)
            connect(
                mAdView.id,
                ConstraintSet.START,
                ConstraintSet.PARENT_ID,
                ConstraintSet.START,
                0
            )
            connect(mAdView.id, ConstraintSet.END, ConstraintSet.PARENT_ID, ConstraintSet.END, 0)

            constrainWidth(mAdView.id, ConstraintSet.MATCH_CONSTRAINT)
            constrainHeight(mAdView.id, ConstraintSet.WRAP_CONTENT)

            applyTo(constraintLayout)
        }
    }
```


## 14. BottomSheetWebView

Khi ta muốn hiển thị 1 trang web ở dạng BottomSheet

**BottomSheetWebView.kt**

```kotlin
import android.annotation.SuppressLint
import android.content.Context
import android.view.View
import android.view.WindowManager
import android.webkit.WebResourceRequest
import android.webkit.WebSettings
import android.webkit.WebView
import android.webkit.WebViewClient
import android.widget.FrameLayout
import android.widget.ImageView
import com.doczy.livescore.R
import com.google.android.material.bottomsheet.BottomSheetBehavior
import com.google.android.material.bottomsheet.BottomSheetDialog

class BottomSheetWebView(context: Context) : FrameLayout(context) {

    private val mBottomSheetDialog: BottomSheetDialog = BottomSheetDialog(context)
    private var mCurrentWebViewScrollY = 0

    private lateinit var webView: ObservableWebView

    init {
        inflateLayout(context)
        setupBottomSheetBehaviour()
        setupWebView()
    }

    private fun inflateLayout(context: Context) {
        inflate(context, R.layout.bottom_sheet_webview, this)

        mBottomSheetDialog.setContentView(this)
        webView = findViewById(R.id.webView)

        mBottomSheetDialog.window?.findViewById<View>(com.google.android.material.R.id.design_bottom_sheet)
            ?.setBackgroundResource(android.R.color.transparent)

        val layout =
            mBottomSheetDialog.window?.findViewById<View>(com.google.android.material.R.id.design_bottom_sheet)
        layout?.let {
            val behavior = BottomSheetBehavior.from(it)
            setupFullHeight(it)
            behavior.state = BottomSheetBehavior.STATE_EXPANDED
        }

        val btnClose = findViewById<ImageView>(R.id.btnClose)
        btnClose.setOnClickListener { close() }

        setting()
    }

    private fun setupBottomSheetBehaviour() {
        (parent as? View)?.let { view ->
            BottomSheetBehavior.from(view).let { behaviour ->
                behaviour.addBottomSheetCallback(object :
                    BottomSheetBehavior.BottomSheetCallback() {
                    override fun onSlide(bottomSheet: View, slideOffset: Float) {

                    }

                    override fun onStateChanged(bottomSheet: View, newState: Int) {
                        if (newState == BottomSheetBehavior.STATE_DRAGGING && mCurrentWebViewScrollY > 0) {
                            // this is where we check if webview can scroll up or not and based on that we let BottomSheet close on scroll down
                            behaviour.setState(BottomSheetBehavior.STATE_EXPANDED);
                        } else if (newState == BottomSheetBehavior.STATE_HIDDEN) {
                            close()
                        }
                    }
                })
            }
        }
    }

    @SuppressLint("SetJavaScriptEnabled")
    private fun setting(){
        webView.settings.apply {
            javaScriptEnabled = true
            useWideViewPort = true
            loadWithOverviewMode = true
            setSupportZoom(true)
            builtInZoomControls = true
            displayZoomControls = false
            javaScriptCanOpenWindowsAutomatically = true
            setSupportMultipleWindows(true)
            domStorageEnabled = true
            databaseEnabled = true
            userAgentString =
                "Mozilla/5.0 (Linux; Android 11; Pixel 5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.91 Mobile Safari/537.36"
            mediaPlaybackRequiresUserGesture = true
            allowFileAccess = true
            cacheMode = WebSettings.LOAD_DEFAULT
            mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
        }

        webView.webViewClient = object : WebViewClient() {
            override fun shouldOverrideUrlLoading(
                view: WebView,
                request: WebResourceRequest?
            ): Boolean {
                return true
            }
        }
    }

    private fun setupWebView() {
        webView.onScrollChangedCallback = object : ObservableWebView.OnScrollChangeListener {
            override fun onScrollChanged(l: Int, t: Int, oldl: Int, oldt: Int) {
                mCurrentWebViewScrollY = t
            }
        }
    }

    fun showWithUrl(url: String) {
        webView.loadUrl(url)
        mBottomSheetDialog.show()
    }

    fun close() {
        mBottomSheetDialog.dismiss()
    }

    private fun setupFullHeight(bottomSheet: View) {
        val layoutParams = bottomSheet.layoutParams
        layoutParams.height = WindowManager.LayoutParams.MATCH_PARENT
        bottomSheet.layoutParams = layoutParams
    }
```


**ObservableWebView**

```kotlin
package com.doczy.livescore.ui.match_analysis

import android.content.Context
import android.util.AttributeSet
import android.webkit.WebView

class ObservableWebView : WebView {
    var onScrollChangedCallback: OnScrollChangeListener? = null

    constructor(context: Context) : super(context)
    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs)
    constructor(context: Context, attrs: AttributeSet?, defStyle: Int) : super(
        context,
        attrs,
        defStyle
    )

    override fun onScrollChanged(l: Int, t: Int, oldl: Int, oldt: Int) {
        super.onScrollChanged(l, t, oldl, oldt)

        onScrollChangedCallback?.onScrollChanged(l, t, oldl, oldt)
    }

    interface OnScrollChangeListener {
        fun onScrollChanged(l: Int, t: Int, oldl: Int, oldt: Int)
    }
}
```


Cách dùng:

```kotlin
BottomSheetWebView(requireContext()).apply{
	showWithUrl("https://google.com")
}
```


## 15. RecyclerView Scroll Item theo Trang

Muốn các item của RecyclerView vuốt theo trang (giống ViewPager) thì ta sử dụng `PagerSnapHelper`

```kotlin
val snapHelper = PagerSnapHelper()
snapHelper.attachToRecyclerView(recyclerView)
```

## 16. BottomNavigation

Khi click vào 1 tab thì tab sẽ đổi icon

Ta set icon như sau:

**drawable/icon_tab.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/ic_home_selected" android:state_checked="true" />
    <item android:drawable="@drawable/ic_home" android:state_checked="false" />
</selector>
```

Tương tự với color:

**color/color_tab.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="@color/nav_selected" android:state_checked="true" />
    <item android:color="@color/nav_unselected" />
</selector>
```


```xml
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="@dimen/bottom_nav_height"
        android:layout_alignParentBottom="true"
        android:background="@color/bg_bottom_nav"
        app:elevation="6dp"
        app:itemIconTint="@color/nav_icon_tint"
        app:itemTextColor="@color/nav_icon_tint"
        app:labelVisibilityMode="labeled"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:menu="@menu/bottom_nav_menu">
```

## 17. Theme - Style

### 17.1 Set font chữ mặc định cho toàn bộ app

Thêm 2 thuộc tính này vào Theme chính

```xml
<item name="android:fontFamily">@font/font_name</item>
<item name="fontFamily">@font/font_name</item>
```

### 17.2 Splash Theme Style

Áp dụng cho `SplashScreen` kiểu mới

```implementation "androidx.core:core-splashscreen:1.0.0"```

```xml
    <style name="Theme.App.Starting" parent="Theme.SplashScreen">
        <item name="windowSplashScreenBackground">@color/window_color</item>
        <item name="windowSplashScreenAnimatedIcon">@drawable/launch_sport</item>
        <item name="postSplashScreenTheme">@style/Theme.LiveScore</item>
    </style>
```

### 17.3 ShapableImageView

```xml
    <style name="setting_icon">
        <item name="cornerSize">6dp</item>
    </style>

    <style name="ImageViewHighlight">
        <item name="cornerSizeTopLeft">8dp</item>
        <item name="cornerSizeBottomLeft">8dp</item>
    </style>
````

### 17.4 Ripple Effect

```xml
    <style name="ImageViewRippleEffect">
        <item name="android:background">?attr/selectableItemBackgroundBorderless</item>
        <item name="android:clickable">true</item>
        <item name="android:focusable">true</item>
    </style>

    <style name="LayoutRippleEffect">
        <item name="android:foreground">?attr/selectableItemBackgroundBorderless</item>
        <item name="android:clickable">true</item>
        <item name="android:focusable">true</item>
    </style>
```

### 17.5 TabLayout - Set kiểu chữ thường

Mặc định là chữ in hoa, muốn nó là in thường thì cần định nghĩa:

```xml
    <style name="MyCustomTextAppearance" parent="TextAppearance.Design.Tab">
        <item name="textAllCaps">false</item>
        <item name="android:textAllCaps">false</item>
    </style>
```

```xml
    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tab"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/bg_color"
        app:tabBackground="@color/bg_color"
        app:tabGravity="center"
        app:tabIndicatorColor="@color/tab_indicator_color"
        app:tabMode="scrollable"
        app:tabSelectedTextColor="@color/text_primary"
        app:tabTextAppearance="@style/MyCustomTextAppearance"
        app:tabTextColor="@color/text_secondary" />
```