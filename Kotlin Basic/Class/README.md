# Class trong Kotlin

Class trong Kotlin được khai báo bằng cách sử dụng từ khóa `class`

```Kotlin
class Invoice { /*...*/ }
```

## 1. Constructors

Một class có thể có 1 constructor chính và nhiều constructor phụ

Constructor chính được tạo ngay sau tên lớp:

```Kotlin
class Person constructor(firstName: String) { /*...*/ }
```

Từ khóa `constructor` có thể bỏ qua

```Kotlin
class Person(firstName: String) { /*...*/ }
```

**Lưu ý:** Bên trong constructor chính không được chứa bất kì dòng code nào. Nếu muốn khởi tạo 1 cái gì đấy thì có thể đặt nó trong khối khởi tạo `init{ /*...*/ }`

```Kotlin
class Person(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints ${name}")
    }
    
    val secondProperty = "Second property: ${name.length}".also(::println)
    
    init {
        println("Second initializer block that prints ${name.length}")
    }
}
```

Các tham số của constructor chính có thể được sử dụng trong các khối khởi tạo `init`, hoặc cũng có thể sử dụng trong quá trình khởi tạo các thuộc tính được khai báo trong phần thân lớp

```Kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

**Tuy nhiên:** Trong thực tế, chúng ta có thể khai báo các thuộc tính và khởi tạo nó từ constructor chính như sau:

```Kotlin
class Person(val firstName: String, val lastName: String, var age: Int) { /*...*/ }
```

Cũng giống như các thuộc tính thông thường, các thuộc tính được khai báo trong constructor chính có thể thay đổi (*var*) hoặc chỉ đọc (*val*).

### 1.2 Secondary constructors (Các hàm khởi tạo phụ)

Các secondary constructors được định nghĩa bên trong thân class và bắt đầu bằng từ khóa `construtor`

`Secondary Constructors` bắt buộc phải gọi lại `Primary Constructor` bằng cách dùng từ khóa `this` và truyền các tham số thích hợp:

```Kotlin
class Person(val name: String) {
    var children: MutableList<Person> = mutableListOf()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

**Lưu ý:** Code ở trong khối khởi tạo `init` chính là 1 phần của `Primary Constructor`. Do đó, code bên trong `init` sẽ luôn được chạy trước code bên trong `Secondary Constructor`


*Note:* Nếu bạn không muốn class có hàm khỏi tạo công khai:

```Kotlin
class DontCreateMe private constructor () { /*...*/ }
```