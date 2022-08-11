# Kotlin Collections

## Iterators

Duyệt theo 1 hướng `next()`

**Cách 1**

```kotlin
val numbers = listOf("one", "two", "three", "four")
val numbersIterator = numbers.iterator()
while (numbersIterator.hasNext()) {
    println(numbersIterator.next())
}
```

**Cách 2**

```kotlin
val numbers = listOf("one", "two", "three", "four")
for (item in numbers) {
    println(item)
}
```

**Cách 3**

```kotlin
val numbers = listOf("one", "two", "three", "four")
numbers.forEach {
    println(it)
}
```

## List iterators

Đối với list, có thể dùng list iterators để duyệt theo cả 2 hướng: `previous()` và `next()`

```kotlin
val numbers = listOf("one", "two", "three", "four")
val listIterator = numbers.listIterator()
while (listIterator.hasNext()) listIterator.next()
println("Iterating backwards:")
while (listIterator.hasPrevious()) {
    print("Index: ${listIterator.previousIndex()}")
    println(", value: ${listIterator.previous()}")
}
```


## Mutable iterators

Để duyệt 1 tập hợp có thể thay đổi (nutable collections), ta sử dụng `MutableIterator` (extends `Iterator`). Có thêm hàm `remove()` giúp xóa phần tử đang duyệt

```kotlin
val numbers = mutableListOf("one", "two", "three", "four") 
val mutableIterator = numbers.iterator()

mutableIterator.next()
mutableIterator.remove()    
println("After removal: $numbers")
```

Hơn thế, sử dụng **MutableListIterator** có thể vừa thêm/sửa phần tử đang duyệt

```koltin
val numbers = mutableListOf("one", "four", "four") 
val mutableListIterator = numbers.listIterator()

mutableListIterator.next()
mutableListIterator.add("two")
mutableListIterator.next()
mutableListIterator.set("three")   
println(numbers)
```

## Transformations

1. Map

Mapping tạo ra 1 danh sách kết quả được chuyển đổi. 
Nó áp dụng hàm lambda cho mỗi phần tử tiếp theo và trả về danh sách các kết quả của lambda. Thứ tự của các kết quả giống như thứ tự ban đầu của các phần tử.

```kotlin
val numbers = setOf(1, 2, 3)
println(numbers.map { it * 3 })
println(numbers.mapIndexed { idx, value -> value * idx })
```

Nếu phép biến đổi tạo ra giá trị `null`, ta có thể lọc các giá trị `null` từ tập hợp kết quả bằng cách gọi hàm `mapNotNull()` thay vì `map()`

```kotlin
val numbers = setOf(1, 2, 3)
println(numbers.mapNotNull { if ( it == 2) null else it * 3 })
println(numbers.mapIndexedNotNull { idx, value -> if (idx == 0) null else value * idx })
```

Kết quả:
```
[3, 9]
[2, 6]
```

Khi biến đổi 1 map, ta có 2 lựa chọn

- Biến đổi keys, giữ nguyên values: `mapKeys()`
- Biến đổi values, giữ nguyên keys: `mapValues()`

```koltin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
println(numbersMap.mapKeys { it.key.uppercase() })
println(numbersMap.mapValues { it.value + it.key.length })
```

Kết quả:

```
{KEY1=1, KEY2=2, KEY3=3, KEY11=11}
{key1=5, key2=6, key3=7, key11=16}
```

2. Zip


Zipping xây dựn các các cặp từ các phần tử có cùng vị trí của 2 tập hợp.

Zip trả về danh sách các cặp `List<Pair>`

Nếu 2 tập hợp có kích thước khác nhau, kết quả của zip() sẽ là kích thước nhỏ hơn, các phần tử cuối của tập hợp lớn hơn không được đưa vào kết quả.

```kotlin
val colors = listOf("red", "brown", "grey")
val animals = listOf("fox", "bear", "wolf")
println(colors zip animals)

val twoAnimals = listOf("fox", "bear")
println(colors.zip(twoAnimals))
```


```kotlin
val colors = listOf("red", "brown", "grey")
val animals = listOf("fox", "bear", "wolf")

println(colors.zip(animals) { color, animal -> "The ${animal.replaceFirstChar { it.uppercase() }} is $color"})
```

Khi có 1 List<Pair>, ta cũng có thể `unzip()`

```kotlin
val numberPairs = listOf("one" to 1, "two" to 2, "three" to 3, "four" to 4)
println(numberPairs.unzip())
```

Kết quả trả về của `unzip()` là `Pair<List<T>, List<R>>`

