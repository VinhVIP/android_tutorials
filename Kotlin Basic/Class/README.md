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

Nếu 1 class có `Primary Constructor` thì mỗi `Secondary Constructors` bắt buộc phải ủy quyền cho `Primary Constructors` trực tiếp hoặc gián tiếp thông qua `Secondary Constructor` khác bằng cách dùng từ khóa `this` và truyền các tham số thích hợp:

```Kotlin
class Person(val name: String) {
    init {
        println("This line print before secondary constructor run")
    }
    constructor(name: String, age: Int) : this(name) {
        println("This is secondary constructor 1")
    }

    constructor(name: String, age: Int, phone: String) : this(name, age) {
        println("This is secondary constructor 2")
    }
}
```

**Lưu ý:** Code ở trong khối khởi tạo `init` chính là 1 phần của `Primary Constructor`. Do đó, code bên trong `init` sẽ luôn được chạy trước code bên trong `Secondary Constructor`


*Note:* Nếu bạn không muốn class có hàm khỏi tạo công khai:

```Kotlin
class DontCreateMe private constructor () { /*...*/ }
```

## 2. Tạo các instance của class

Để tạo 1 instance của 1 class, ta gọi hàm constructor của class như 1 hàm thông thường:

```Kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

## 3. Các thành phần bên trong 1 class

Class có thể chứa các thành phần:

- Các Constructor và khối khởi tạo.
- Các thuộc tính (Properties)
- Các hàm (Functions)
- Nested và inner classes
- Đối tượng