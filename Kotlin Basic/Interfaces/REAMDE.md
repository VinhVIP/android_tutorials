# 1. Interfaces

Interface trong Kotlin có thể chứa các khai báo cho phương thức trừu tượng

Khai báo interface tương tự với khai báo class, chỉ cần thay từ khóa class thành `interface`

Tất cả các phương thức trong interface đều là `open`.

```Kotlin
interface MyInterface {
    fun bar() 	// open and abstract
    fun foo() {
      // optional body => only open, not abstract
    }
}
```

# 2. Implements interfaces

Một lớp chỉ có thể kế thừa duy nhất 1 lớp, nhưng có thể implements nhiều interface.

```Kotlin
class Child : MyInterface {
    override fun bar() {
        // body
    }
}
```