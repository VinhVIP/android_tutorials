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


# 1. Adapter

*RecyclerGameAdapter*

```Kotlin
package com.vinh.testnew

import android.content.Context
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.ImageView
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class RecyclerGameAdapter(
    private val context: Context,
    private val games: MutableList<Game>,       // Mảng data của RecyclerView
    private val listener: OnItemClickListener   // Quản lý sự kiện item clicked
) :
    RecyclerView.Adapter<RecyclerGameAdapter.GameHolder>() {


    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): GameHolder {
        // LayoutInflater dùng để chuyển layout xml thành View
        val view = LayoutInflater.from(context).inflate(R.layout.row_item, parent, false)
        return GameHolder(view)
    }

    override fun onBindViewHolder(holder: GameHolder, position: Int) {
        // Gửi dữ liệu để hiển thị lên row item layout
        holder.bindData(games[position])
        
        // Set sự kiện click vào item
        holder.itemView.setOnClickListener {
            listener.onClick(games[position], position)
        }
    }

    override fun getItemCount(): Int {
        return games.size
    }

    class GameHolder(view: View) : RecyclerView.ViewHolder(view) {
        var gameId: TextView = view.findViewById(R.id.gameId)
        var img: ImageView = view.findViewById(R.id.img)
        var name: TextView = view.findViewById(R.id.name)
        var type: TextView = view.findViewById(R.id.type)
        var rate: TextView = view.findViewById(R.id.rate)
        var size: TextView = view.findViewById(R.id.size)


        fun bindData(game: Game) {
            gameId.text = game.id.toString()
            img.setImageResource(game.img)
            name.text = game.name
            type.text = game.type
            rate.text = game.rate.toString()
            size.text = game.size.toString().plus(" MB")
        }
    }

    interface OnItemClickListener {
        fun onClick(game: Game, position: Int)
    }

}
```

# 2. Khai báo RecyclerView trong xml layout

*activity_main.xml*

```Kotlin
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_item" />

</LinearLayout>
```

# 3. Khai báo và set adapter cho RecyclerView

*MainActivity.kt*

```Kotlin
package com.vinh.testnew

import android.os.Bundle
import android.widget.ListView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView

class MainActivity : AppCompatActivity(), RecyclerGameAdapter.OnItemClickListener {

    private val games = mutableListOf<Game>(
        Game(1, R.mipmap.among_us, "Among Us", "Hành động", 3.7, 45, "Innersloth LLC"),
        Game(2, R.mipmap.ball, "8 Ball Pool", "Thể thao", 4.5, 97, "Miniclip.com"),
        Game(3, R.mipmap.candy, "Candy Crush Saga", "Thông thường", 4.6, 94, "King"),
        Game(4, R.mipmap.chess, "Cờ vua", "Dạng bảng", 4.4, 76, "Chess Prince"),
        Game(5,R.mipmap.lien_quan, "Garena Liên Quân Mobile", "Hành động", 3.9, 455, "Garena"),
        Game(6, R.mipmap.minecraft, "Minecraft Trial", "Trò chơi điện tử", 4.1, 128, "Mojang"),
        Game(7, R.mipmap.plant_vs_zombie, "Plants vs Zombies 2", "Thông thường", 4.2, 357, "Electronic Arts"),
        Game(8, R.mipmap.pubg, "PUBG Mobile", "Hành động", 4.3, 527, "VNG Game Publishing"),
        Game(9, R.mipmap.shadow_knight, "Shadow Knight", "Hành động", 4.4, 420, "Fansipan Limited"),
        Game(10, R.mipmap.stick_man, "Stickman Legends: Chiến Binh Đánh Nhau Quái Vật", "Hành động", 4.0, 348, "Azura Global"),
        Game(11, R.mipmap.subway, "Subway Surfers", "Trò chơi điện tử", 4.2, 308, "SYBO Games"),
        Game(12, R.mipmap.vo_lam, "Võ Lâm Truyền Kỳ 1 Mobile", "Nhập vai", 4.4, 930, "VNG"),
        Game(13, R.mipmap.zombie, "Zombie Tsunami", "Trò chơi điện tử", 4.5, 214, "Mobigame S.A.R.L"),
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val recyclerView:RecyclerView = findViewById(R.id.listView)

        // Khai báo và khởi tạo 1 adpter
        val adapter = RecyclerGameAdapter(this, games, this)

        // Gán adapter cho RecyclerView
        recyclerView.adapter = adapter

        // Chọn hướng scroll là hướng dọc
        recyclerView.layoutManager = LinearLayoutManager(this)

        // Chọn hướng scroll là hướng ngang
        // recyclerView.layoutManager = LinearLayoutManager(this, LinearLayoutManager.HORIZONTAL, false)

    }

    override fun onClick(game: Game, position: Int) {
        Toast.makeText(this, "Clicked: "+games[position].name, Toast.LENGTH_SHORT).show();
    }

}
```
