# Kotlin

## 1. Kiểu số

Trong Kotlin, mọi thứ đều là một *đối tượng* (object)

### 1.1 Số nguyên

Có 4 loại số nguyên với kích thước khác nhau:

- Byte: 8 bits
- Short: 16 bits
- Int: 32 bits
- Long: 64 bits

```Kotlin
val one = 1 // Int
val threeBillion = 3000000000 // Long
val oneLong = 1L // Long
val oneByte: Byte = 1
```

### 1.2 Số thực

Có 2 loại:

- Float: 32 bits (6-7 chữ số thập phân)
- Double: 64 bits (15-16 chữ số thập phân)

```Kotlin
val pi = 3.14 // Double
// val one: Double = 1 // Error: type mismatch
val oneDouble = 1.0 // Double
```

Để chỉ rõ ràng kiểu *Float* cho 1 giá trị, sử dụng hậu tố *f* hoặc *F*, nếu giá trị đó có nhiều hơn 6-7 chữ số thập phân thì nó sẽ được làm tròn.

```Kotlin
val e = 2.7182818284 // Double
val eFloat = 2.7182818284f // Float, actual value is 2.7182817
```

**Lưu ý:** Kotlin không có ép kiểu ngầm định giống như C++/Java. Một hàm có tham số kiểu *Double* thì chỉ có thể được gọi trên các giá trị *Double*, nhưng không được gọi là *Float*, *Int* hay các kiểu số khác.

```Kotlin
fun main() {
    fun printDouble(d: Double) { print(d) }

    val i = 1
    val d = 1.0
    val f = 1.0f

    printDouble(d)
//    printDouble(i) // Error: Type mismatch
//    printDouble(f) // Error: Type mismatch
}
```


### 1.3 Một số kiểu giá trị khác

Một số giá trị được ngầm hiểu để gán cho các biến như:

- *Hexadecimals*: `0xFF`
- *Binaries*: `0b110000`

Ta có thể sử dụng dấu gạch dưới để các giá trị dễ đọc hơn.

```Kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

### 1.4. Ép kiểu dữ liệu

**Ép kiểu ngầm định**

Kiểu dữ liệu có kích thước nhỏ sẽ được ép kiểu ngầm định khi chuyển sang kiểu dữ liệu có kích thước lớn hơn.


```Kotlin
val a: Int? = 1 // A boxed Int (java.lang.Integer)
val b: Long? = a
```

**Ép kiểu tường minh**

```Kotlin
val b: Byte = 1 // OK
// val i: Int = b // ERROR
val i1: Int = b.toInt()
```

Tất cả các kiểu số đều được hỗ trợ chuyển sang các loại khác

- toByte(): Byte
- toShort(): Short
- toInt(): Int
- toLong(): Long
- toFloat(): Float
- toDouble(): Double
- toChar(): Char

Trong nhiều trường hợp, không cần chuyển đổi kiểu rõ ràng vì kiểu được suy ra từ ngữ cảnh và các phép tính số học được nạp chồng cho các chuyển đổi thích hợp.

```Kotlin
val l = 1L + 3 // Long + Int => Long
```

### 1.5 Phép chia số nguyên

Phép chia giữa các số nguyên luôn trả về 1 số nguyên, phần thập phân sẽ tự động bị loại bỏ.

```Kotlin
val x = 5 / 2
//println(x == 2.5) // ERROR: Operator '==' cannot be applied to 'Int' and 'Double'
println(x == 2) // TRUE
```

Để trả về kết quả là số thực, cần chuyển đổi kiểu dữ liệu sang số thực.

```Kotlin
val x = 5 / 2.toDouble() 
println(x == 2.5) // TRUE
```

### 1.6 Phép thao tác bit (bitwise)

Kotlin cung cấp các phép toán bit trên các số nguyên.

**Lưu ý:** Chỉ áp dụng cho kiểu `Int` và `Long`

- shl(bits) – signed shift left

- shr(bits) – signed shift right

- ushr(bits) – unsigned shift right

- and(bits) – bitwise and

- or(bits) – bitwise or

- xor(bits) – bitwise xor

- inv() – bitwise inversion

```Kotlin
val x = (1 shl 2) and 0x000FF000
```

### 1.7 Số nguyên không dấu

Kể từ Kotlin 1.3 , kiểu số Unsigned được hỗ trợ

- UByte: 8 bits, giá trị từ 0 -> 255

- UShort: 16 bits, giá trị từ 0 -> 65535

- UInt: 32 bits, giá trị từ 0 -> 2^32 - 1

- ULong: 64 bits, giá trị từ 0 -> 2^64 - 1

Để sử dụng kiểu unsigned integer, ta thêm hậu tố *u* hoặc *U* vào sau giá trị.

```Kotlin
val b: UByte = 1u  // UByte, expected type provided
val s: UShort = 1u // UShort, expected type provided
val l: ULong = 1u  // ULong, expected type provided
```

Để chỉ ra 1 kiểu cụ thể:

```Kotlin
val a = 1UL // ULong
```

## 2 Boolean 

Kiểu `Boolean` chỉ có 2 giá trị *true/false*

Kiểu `Boolean?` còn có thể giá trị *null*

```Kotlin
val myTrue: Boolean = true
val myFalse: Boolean = false
val boolNull: Boolean? = null

println(myTrue || myFalse)  // TRUE
println(myTrue && myFalse)  // FALSE
println(!myTrue)            // FALSE
```

## 3 Char 

Char là kiểu kí tự, được biểu diễn trong cặp dấu nháy đơn `''`

```Kotlin
val aChar: Char = 'a'
```

## 4 String

String là kiểu chuỗi kí tự, được biểu diễn trong cặp dấu nháy kép `""`

```Kotlin
val str = "abcd 123"
```

Để duyệt qua các kí tự của String, ta có thể dùng vòng lặp for:

```Kotlin
for (c in str) {
    println(c)
}
```

Để nối chuỗi, ta có thể sử dụng toán tử `+`, miễn là phần tử đầu tiên trong biểu thức là 1 chuỗi

```Kotlin
val s = "abc" + 1
println(s + "def")  //abc1def
```

**Viết chuỗi trên nhiều dòng:**

```Kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """
```

**String templates:**

```Kotlin
val i = 10
println("i = $i") // prints "i = 10"
```
## 5. Arrays
