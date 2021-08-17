# Kế thừa

Tất cả class trong Kotlin đều kế thừa từ lớp `Any`

```Kotlin
class Example // ngầm hiểu Example kế thừa Any
```

Mặc định, các lớp trong Kotlin là `final`, nghĩa là nó không thể được kế thừa. Để có thể được lớp khác kế thừa, ta thêm từ khóa `open` và trước class

```Kotlin
open class Base //Class is open for inheritance
```

Để kế thừa 1 lớp khác:

```Kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

Nếu lớp dẫn xuất có 1 hàm constructor chính, thì lớp cơ sở phải được tạo ngay tại đó, bằng cách sử dụng các tham số của constructor chính.

Nếu lớp dẫn xuất KHÔNG có constructor chính, thì mỗi constructor phụ phải khởi tạo lớp cơ sở bằng cách sử dụng từ khóa `super`, hoặc ủy quyền cho 1 constructor khác thực hiện điều đó.

```Kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

## 2. Override phương thức

Lớp dẫn xuất ghi đè phương thức của lớp cơ sở cần dùng từ khóa `override` phía trước tên phương thức

```Kotlin
open class Shape {
    open fun draw() { /*...*/ }
    fun fill() { /*...*/ }
}

class Circle() : Shape() {
    override fun draw() { /*...*/ }
}
```

Mặc định lớp dẫn xuất khi ghi đè 1 phương thức thì phương thức đó sẽ là `open`, nếu không muốn 1 lớp dẫn xuất khác kế thừa nó thì ta thêm từ khóa `final` phía trước:

```Kotlin
open class Rectangle() : Shape() {
    final override fun draw() { /*...*/ }
}
```

## 3. Override thuộc tính

Ghi đè thuộc tính tương tự với ghi đè phương thức:

```Kotlin
open class Shape {
    open val vertexCount: Int = 0
}

class Rectangle : Shape() {
    override val vertexCount = 4
}
```

Ta có thể override thuộc tính `val` bằng `var`, nhưng không thể làm ngược lại.

*Note:* Ta cũng có thể sử dụng `override` như là 1 phần của constructor chính

```Kotlin
interface Shape {
    val vertexCount: Int
}

class Rectangle(override val vertexCount: Int = 4) : Shape // Always has 4 vertices

class Polygon : Shape {
    override var vertexCount: Int = 0  // Can be set to any number later
}
```

## 4. Gọi các phương thức ở lớp cơ sở

Lớp dẫn xuất có thể gọi các phương thức ở lớp cơ sở bằng cách sử dụng từ khóa `super`

```Kotlin
open class Rectangle {
    open fun draw() { println("Drawing a rectangle") }
    val borderColor: String get() = "black"
}

class FilledRectangle : Rectangle() {
    override fun draw() {
        super.draw()
        println("Filling the rectangle")
    }

    val fillColor: String get() = super.borderColor
}
```

Bên trong inner class có thể truy cập đến các phương thức của lớp cha của lớp outer bên ngoài bằng từ khóa `super` kèm theo tên outer class: `super@Outer`

```Kotlin
class FilledRectangle: Rectangle() {
    override fun draw() { 
        val filler = Filler()
        filler.drawAndFill()
    }

    inner class Filler {
        fun fill() { println("Filling") }
        fun drawAndFill() {
            super@FilledRectangle.draw() // Calls Rectangle's implementation of draw()
            fill()
            println("Drawn a filled rectangle with color ${super@FilledRectangle.borderColor}") // Uses Rectangle's implementation of borderColor's get()
        }
    }
}
```

## 5. Quy tắc Override

Trong Kotlin, nếu lớp dẫn xuất Override 1 phương thức ở lớp cơ sở và 1 phương thức ở interface, nhưng cả 2 phương thức này đều giống nhau. Do đó, muốn truy cập cụ thể đến lớp được kế thừa, ta kèm theo điều kiện tên của lớp kế thừa/interface bên trong cặp dấu ngoặc `<>`

```Kotlin
open class Rectangle {
    open fun draw() { /* ... */ }
}

interface Polygon {
    fun draw() { /* ... */ } // interface members are 'open' by default
}

class Square() : Rectangle(), Polygon {
    // The compiler requires draw() to be overridden:
    override fun draw() {
        super<Rectangle>.draw() // call to Rectangle.draw()
        super<Polygon>.draw() // call to Polygon.draw()
    }
}
```