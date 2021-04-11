# ListView trong Android

## 1. ListView là gì?

ListView là một ViewGroup, hiển thị các thành phần theo 1 danh sách, có thể cuộn theo chiều dọc.

ListView là 1 View cực quan trọng, được sử dụng rộng rãi

## 1.1 ListItem

Một ListView được tạo ra từ 1 danh sách các ListItem (là 1 row riêng lẻ để hiển thị dữ liệu)

ListItem có giao diện, được tạo từ những View cơ bản.

## 1.2 Adapter (Bộ chuyển đổi/tiếp nối)

Adapter có thể hiểu là một cầu nối giữa các View và các dữ liệu cơ bản của View đó. Tức Adapter quản lý các dữ liệu và ghép nối chúng với các ListItem riêng lẻ của ListView.

[adapter](https://s1.o7planning.com/vi/10435/images/1303918.png)

[adapter](https://s1.o7planning.com/vi/10435/images/1304328.png)


**AdapterView**

Có nhiều View cần tới AdapterView để quản lý dữ liệu hiển thị, các View này là con của lớp AdapterView.

[adapterview](https://s1.o7planning.com/vi/10435/images/1304039.png)

**Android Adapter**

[android adapter](https://s1.o7planning.com/vi/10435/images/1304404.png)

## 2. ListView cơ bản sử dụng ArrayAdapter

## 2.1 ArrayAdapter

ArrayAdapter sử dụng để hiển thị các ListItem đơn giản. ListItem đó có thể chỉ có duy nhất 1 TextView, hoặc 1 CheckedTextView hoặc EditText,...

Nếu muốn ListItem có giao diện phức tạp hơn, ta cần tạo 1 Adapter tùy biến (sẽ tìm hiểu sau).

*activity_main.xml*

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />

</LinearLayout>
```

*MainActivity.kt*

```Kotlin
package com.vinh.testnew

import android.os.Bundle
import android.widget.ArrayAdapter
import android.widget.ListView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private val data = listOf<String>("Vinh", "Nam", "Nhi", "Trang", "Long", "Hoa", "An")

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val listView: ListView = findViewById(R.id.listView)
        val adapter = ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, data)
        listView.adapter = adapter

    }
}

```

[demo-arrayadapter]()

## 2.2 Các layout dựng sẵn để làm việc với ArrayAdapter

Android xây dựng 1 số Layout (cho ListItem) để có thể làm việc với ArrayAdapter

**android.R.layout.simple_list_item_1**

Đây là layout đơn giản chỉ được tạo duy nhất bởi 1 TextView

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:textAppearance="?android:attr/textAppearanceListItemSmall"
    android:gravity="center_vertical"
    android:paddingStart="?android:attr/listPreferredItemPaddingStart"
    android:paddingEnd="?android:attr/listPreferredItemPaddingEnd"
    android:minHeight="?android:attr/listPreferredItemHeightSmall" />
```

**android.R.layout.simple_list_item_checked  & android.R.layout.simple_list_item_multiple_choice**

2 layout trên dùng để tạo ra ListItem có 1 CheckedTextView

```Kotlin
val listView: ListView = findViewById(R.id.listView)
listView.choiceMode = ListView.CHOICE_MODE_SINGLE
val adapter = ArrayAdapter<String>(this, android.R.layout.simple_list_item_checked, data)
listView.adapter = adapter
```

[checkedtv]()

## 3. Custom ListView

Mục tiêu là chúng ta sẽ tạo 1 ListView có giao diện như hình:

[target]()

## 3.1 Thiết kế giao diện cho ListItem 

*res/layout/row_item.xml*

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:paddingVertical="8dp"
    android:paddingStart="16dp">

    <TextView
        android:id="@+id/gameId"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:fontFamily="@font/arimo"
        android:text="1"
        android:textColor="@color/black"
        android:textSize="16sp" />

    <androidx.cardview.widget.CardView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        app:cardCornerRadius="16dp">

        <ImageView
            android:id="@+id/img"
            android:layout_width="70dp"
            android:layout_height="70dp"
            android:src="@mipmap/vo_lam" />
    </androidx.cardview.widget.CardView>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="70dp"
        android:gravity="center_vertical"
        android:orientation="vertical"
        android:paddingStart="16dp">

        <TextView
            android:id="@+id/name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ellipsize="end"
            android:fontFamily="@font/arimo"
            android:maxLines="1"
            android:text="Võ lâm truyền kì 1"
            android:textColor="#000"
            android:textSize="18sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/type"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@id/name"
            android:fontFamily="@font/arimo"
            android:text="Nhập vai"
            android:textColor="#938686"
            android:textSize="15sp" />

        <TextView
            android:id="@+id/rate"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/type"
            android:drawableEnd="@drawable/ic_star"
            android:fontFamily="@font/arimo"
            android:text="4,4"
            android:textSize="14sp" />

        <TextView
            android:id="@+id/size"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/type"
            android:layout_marginStart="12dp"
            android:layout_toRightOf="@id/rate"
            android:fontFamily="@font/arimo"
            android:text="108 MB"
            android:textSize="14sp" />
    </RelativeLayout>


</LinearLayout>
```

## 3.2 Tạo model dữ liệu

*Game.kt*

```kotlin
package com.vinh.testnew

class Game(val id: Int, var img:Int, var name:String, var type:String, var rate:Double, var size:Int, var dev:String) {
}
```

## 3.3 Tạo custom adapter là lớp mở rộng từ BaseAdapter

*ListGameAdapter.kt*

```kotlin
package com.vinh.testnew

import android.content.Context
import android.util.Log
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.BaseAdapter
import android.widget.ImageView
import android.widget.TextView

class ListGameAdapter(private val context: Context, private val dataSource: ArrayList<Game>) :
    BaseAdapter() {


    override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {

        var convertView = convertView
        val viewHolder: ViewHolder

        if (convertView == null) {
            convertView = LayoutInflater.from(context).inflate(R.layout.row_item, parent, false)
            viewHolder = ViewHolder()
            viewHolder.gameId = convertView!!.findViewById(R.id.gameId)
            viewHolder.img = convertView.findViewById(R.id.img)
            viewHolder.name = convertView.findViewById(R.id.name)
            viewHolder.type = convertView.findViewById(R.id.type)
            viewHolder.rate = convertView.findViewById(R.id.rate)
            viewHolder.size = convertView.findViewById(R.id.size)
        } else {
            viewHolder = convertView.tag as ViewHolder
        }

        val game: Game = getItem(position) as Game

        viewHolder.gameId.text = game.id.toString()
        viewHolder.img.setImageResource(game.img)
        viewHolder.name.text = game.name
        viewHolder.type.text = game.type
        viewHolder.rate.text = game.rate.toString()
        viewHolder.size.text = game.size.toString().plus(" MB")

        convertView.tag = viewHolder

        return convertView
    }

    override fun getCount(): Int {
        return dataSource.size
    }

    override fun getItem(position: Int): Game {
        return dataSource[position]
    }

    override fun getItemId(position: Int): Long {
        return getItem(position).id.toLong()
    }

    class ViewHolder {
        lateinit var gameId: TextView
        lateinit var img: ImageView
        lateinit var name: TextView
        lateinit var type: TextView
        lateinit var rate: TextView
        lateinit var size: TextView
    }

}
```

## 3.4 Truyền dữ liệu và xử lý sự kiện item click

*MainActivity.kt*

```Kotlin
package com.vinh.testnew

import android.content.Intent
import android.os.Bundle
import android.widget.ListView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private var games = ArrayList<Game>()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // khởi tạo mảng dữ liệu
        genGames()

        val listView: ListView = findViewById(R.id.listView)
        val adapter = ListGameAdapter(this, games)
        listView.adapter = adapter

        listView.setOnItemClickListener { parent, view, position, id ->
            Toast.makeText(this, "Clicked: "+games[position].name, Toast.LENGTH_SHORT).show();
        }
    }


    private fun genGames() {
        games.add(Game(1, R.mipmap.among_us, "Among Us", "Hành động", 3.7, 45, "Innersloth LLC"))
        games.add(Game(2, R.mipmap.ball, "8 Ball Pool", "Thể thao", 4.5, 97, "Miniclip.com"))
        games.add(Game(3, R.mipmap.candy, "Candy Crush Saga", "Thông thường", 4.6, 94, "King"))
        games.add(Game(4, R.mipmap.chess, "Cờ vua", "Dạng bảng", 4.4, 76, "Chess Prince"))
        games.add(Game(5,R.mipmap.lien_quan, "Garena Liên Quân Mobile", "Hành động", 3.9, 455, "Garena"))
        games.add(Game(6, R.mipmap.minecraft, "Minecraft Trial", "Trò chơi điện tử", 4.1, 128, "Mojang"))
        games.add(Game(7, R.mipmap.plant_vs_zombie, "Plants vs Zombies 2", "Thông thường", 4.2, 357, "Electronic Arts"))
        games.add(Game(8, R.mipmap.pubg, "PUBG Mobile", "Hành động", 4.3, 527, "VNG Game Publishing"))
        games.add(Game(9, R.mipmap.shadow_knight, "Shadow Knight", "Hành động", 4.4, 420, "Fansipan Limited"))
        games.add(Game(10, R.mipmap.stick_man, "Stickman Legends: Chiến Binh Đánh Nhau Quái Vật", "Hành động", 4.0, 348, "Azura Global"))
        games.add(Game(11, R.mipmap.subway, "Subway Surfers", "Trò chơi điện tử", 4.2, 308, "SYBO Games"))
        games.add(Game(12, R.mipmap.vo_lam, "Võ Lâm Truyền Kỳ 1 Mobile", "Nhập vai", 4.4, 930, "VNG"))
        games.add(Game(13, R.mipmap.zombie, "Zombie Tsunami", "Trò chơi điện tử", 4.5, 214, "Mobigame S.A.R.L"))

    }

}
```

[done]()