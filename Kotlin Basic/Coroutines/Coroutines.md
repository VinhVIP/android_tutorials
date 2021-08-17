# Coroutines trong Kotlin

# 1. Coroutines là gì?

- Coroutines là light-weight Threads, nghĩa là chức năng của nó khá tương tự với Thread nhưng nó sử dụng ít tài nguyên hơn

- Giống với Threads, coroutines có thể chạy đồng thời với nhau, chờ nhau và giao tiếp với nhau

- Coroutine khác Thread.

## 1.1 Tại sao nên dùng Coroutines?

Một ứng dụng khi được chạy sẽ chạy trên 1 luồng chính là UI Thread. Nếu trên UI Thread này ta đồng thời xử lý các tác vụ ngầm (background) như upload/download file, kết nối server, load nhạc,... thì sẽ ảnh hưởng đến hiệu năng của UI Thread, khiến UI người dùng bị lag, đơ, thậm chí crash.

Có 1 phương án để giải quyết vấn đề trên là với mỗi tác vụ ngầm (background service), ta sẽ cho các Thread đảm nhiệm các vai trò này và sau đó báo cáo lại kết quả cho UI Thread. Tuy nhiên, việc có nhiều Thread được tạo ra từ 1 ứng dụng cũng sẽ gây ra nhiều vấn đề và tiêu tốn khá nhiều tài nguyên.

Từ đó, giải pháp được đưa ra là sử dụng Coroutines. Bởi vì Coroutine nhẹ, tiêu tốn rất ít tài nguyên và 1 Thread có thể quản lý, thực thi nhiều Coroutine -> *Phê chưa???*

## 1.2 Cách tạo 1 Coroutine

Sử dụng Coroutines Builder như `launch`, `async`, `runBlocking` để tạo 1 `Coroutine`

### 1.2.1 launch

```java
// Tạo coroutine tại global (app) level
// Chỉ sử dụng khi cần thiết cho các tác vụ lâu dài với vòng đời của ứng dụng
GlobalScope.launch {
	// code
}

// Tạo coroutine tại local scope
launch {
	// code
}
```

- `launch` tạo 1 coroutine trên Thread chứa nó, nhưng **KHÔNG** block Thread đó

- `launch` trả về 1 đối tượng là `Job`, từ đối tượng này ta có thể chờ/hủy coroutine

```java
val job: Job = launch {
	// code
	delay(1000)
}

job.join()		// Chờ coroutine thực thi đến khi xong
job.cancel()	// Hủy coroutine
```
### 1.2.2 async

- `async`: Giống với `launch`, nhưng `async` trả về 1 đối tượng là `Deferred` (kế thừa Job) và kèm theo giá trị trả về sau khi thực thi.

```java
val jobDeferred: Deferred<String> = async {
	// code
	delay(1000)
	"Done!"
}

val message: String = jobDeferred.await()
println(message)
```

### 1.2.3 runBlocking

- `runBlocking` block thread hiện tại. *Chấm hết :v*

## 1.3 Sự khác nhau giữa Thread.sleep() và delay()

- `Thread.sleep(millis)`: Tạm hoãn Thread trong 1 khoảng thời gian xác định, nghĩa là những gì thực hiện bên trong Thread đều bị tạm ngừng

- `delay(millis)`: Phương thức này dùng cho coroutine, với chức năng tạm hoãn coroutine hiện hành, những coroutine khác cho dù chung Thread với coroutine bị delay vẫn sẽ chạy như bình thường.

# 2. Suspending functions (Phương thức trì hoãn)

- Là phương thức có modifier là `suspend`

- Suspending functions chỉ được gọi từ 1 coroutine hoặc 1 suspending function khác

VD:

```java
suspend fun mySuspendFunc(time: Long) {
	// code
	delay(time)
}
```

# 3. Hủy 1 Coroutine

- To cancel a coroutine, it should be cooperative

- Coopearative functions ở đây có thể được hiển là cancellable functions (phương thức có thể hủy)

VD:

```java
val job = launch{
	// doing something
}

job.cancel()
job.join()
```

Nếu Coroutine là cooperative thì `job.cancel()` sẽ được thực hiện, còn không thì chương trình sẽ chờ cho đến khi Coroutine hoàn thành công việc - `job.join()`

- `job.cancelAndJoin()` : Nếu Coroutine là cooperative thì sẽ cancel, ngược thì thì join

## 3.1 Các tạo Coroutine Cooperative

- Chỉ những suspending functions thuộc package kotlix.coroutines mới có thể tạo Coroutine cooperative (functions cancellable).

VD: `delay(), yield(), withContext(), withTimeout(),...`

```kotlin
fun main() = runBlocking{
	val job: Job = launch{
		for(i in 0..500){
			print("$i.")
			delay(50)   // dùng yield() thay thế nếu muốn công việc chạy liên tục k nghỉ
		}
	}

	delay(200)
	job.cancelAndJoin()
}
```

## 3.2 Handling Exceptions

- Các suspending functions có thể cancel (cancellable) như `yield(), delay(), ...` sẽ ném ngoại lệ `CancellationException`

```java
fun main() = runBlocking{
	val job: Job = launch(Dispatchers.Default){
		try{
			for(i in 0..500){
				print("$i.")
				delay(10)  
			}
		}catch(ex: CancellationException){
			print("Cancel Exception")
		}finally{
			print("Close finally")
		}
		
	}

	delay(200)
	job.cancelAndJoin()
}
```

- Ta không thể thực thi 1 suspending function bên trong khối lệnh `finally` bởi vì coroutine chạy trong đoạn code chứa khối finally đã bị cancel. Nhưng nếu muốn, ta cho đoạn code muốn chạy vào bên trong phương thức `withContext(NonCancellable)`

```java
fun main() = runBlocking{
	val job: Job = launch(Dispatchers.Default){
		try{
			for(i in 0..500){
				print("$i.")
				delay(10)  
			}
		}catch(ex: CancellationException){
			print("Cancel Exception")
		}finally{
			withContext(NonCancellable){
				delay(1000)
				print("Close finally")
			}
		}
		
	}

	delay(200)
	job.cancelAndJoin()
}
```

*Note:* Ta có thể gửi thông điệp cancel bằng cách sử dụng

```java
job.cancel(CancellationException("My crash message"))
```

## 3.3 withTimeout và withTimeoutOrNull

- `withTimeout` chạy trong 1 khoảng thời gian chỉ định sẽ cancel, phương thức này sẽ throw `TimeOutCancellationException`

```java
withTimeout(2000) {
	try {
		for (i in 0..500) {
			print("$i.")
			delay(500)
		}
	} catch (ex: TimeOutCancellationException) {
		// code
	} finally {
		// code
	}
}
```

- `withTimeoutOrNull` cũng sẽ chạy trong khoảng thời gian nhất định, nhưng nó không ném bất cứ ngoại lệ nào, và nó còn trả về giá trị sau thi công việc thực hiện hoàn thành. Nếu bị cancel giữa chừng, giá trị trả về sẽ là `null`

```java
val result: String? = withTimeoutOrNull(2000) {
	for (i in 0..500) {
		print("$i.")
		delay(500)
	}
	"Done!"
}
print("Result: $result")
```


# 4. Compose Suspending Functions

## 4.1 Sequential Excecution (Thực thi tuần tự)

Tìm hiểu ví dụ:

```java
fun main() = runBlocking {
	val msgOne = getMessageOne()
	val msgTwo = getMessageTwo()
	print("message: ${msgOne + msgTwo}")
}

suspend fun getMessageOne(): String {
	delay(1000)
	return "Hello"
}

suspend fun getMessageTwo(): String {
	delay(1000)
	return "World!"
}
```

Ta có 2 suspending functions, mỗi phương thức sẽ bị delay 1000ms, phương thức main là 1 runBlocking coroutine với công việc là lấy 2 message từ 2 phương thức suspend sẽ tiêu tốn thời gian >= 2000 ms

=> Chương trình được thực hiện tuần tự từ trên xuống dưới.

## 4.2 Concurrent Execution (Thực thi đồng thời)

Trường hợp muốn các tác vụ thực thi đồng thời, ta sử dụng `async`

```java
fun main() = runBlocking {
	val msgOne: Deferred<String> = async { getMessageOne() }
	val msgTwo: Deferred<String> = async { getMessageTwo() }
	print("message: ${msgOne.await() + msgTwo.await()}")
}

suspend fun getMessageOne(): String {
	delay(1000)
	return "Hello"
}

suspend fun getMessageTwo(): String {
	delay(1000)
	return "World!"
}
```

Khi này, thời gian thực thi chương trình >= 1000 ms

Giải thích: Khi sử dụng `async` thì 1 coroutine sẽ được tạo mới để chạy tác vụ được yêu cầu. Theo đoạn code trên thì 2 coroutine sẽ được tạo và chạy gần như đồng thời với nhau.

*Note:* Tại sao không sử dụng `launch` thay cho `async`? Đơn giản vì `launch` không trả về bất cứ giá trị nào, trong khi `async` thì có thể :v


## 4.3 Lazy Execution

```java
fun main() = runBlocking {
	val msgOne: Deferred<String> = async(start = CoroutineStart.LAZY) { getMessageOne() }
	val msgTwo: Deferred<String> = async(start = CoroutineStart.LAZY) { getMessageTwo() }
	print("message: ${msgOne.await() + msgTwo.await()}")
}

suspend fun getMessageOne(): String {
	delay(1000)
	return "Hello"
}

suspend fun getMessageTwo(): String {
	delay(1000)
	return "World!"
}
```

Giải thích: Khi thêm `start = CoroutineStart.LAZY`, chương trình chạy đến khi gặp `await()` thì `async` coroutine mới được chạy. Điều này tránh cho việc các async coroutines thực thi nhưng không được sử dụng gây lãng phí tài nguyên.

# 5. CoroutineScope

- Mỗi coroutine sẽ có CoroutineScope instance riêng tác động đến nó, **KHÔNG** có sự kế thừa CoroutineScope giữa coroutine cha và con.

- Thông thường, các coroutine builder như `launch`, `async`, `runBlocking` đều có CoroutineScope tác động đến nó, ta có thể truy xuất nó thông qua từ khóa `this`

```java
launch { // this: CoroutineScope
	// code
}

async { // this: CoroutineScope
	// code
}

runBlocking { // this: CoroutineScope
	// code
}
```

# 6. CoroutineContext

- Mỗi coroutine đều có CoroutineContext, nhưng coroutine con có thể kế thừa CoroutineContext từ coroutine cha.

- Để truy xuất đến CoroutineContext của 1 coroutine, ta sử dụng đối tượng `coroutineContext`

- **Dispatcher** xác định thread của 1 coroutine

```java
fun main() = runBlocking { // Thread: main
	/** 
	 * Không có tham số => CONFINED
	 * Kế thừa CoroutineContext từ coroutine cha
	 * Dù sau suspending function (vd: delay()) thì nó vẫn tiếp tục với thread trước đó
	 */
	launch {
		println("C1: ${Thread.currentThread().name}");	// Thread: main
		delay(1000)
		println("C1: ${Thread.currentThread().name}");	// Thread: main
	}

	/**
	 * Tham số là Dispatchers.Default => Tương tự với GlobalScope.launch{ }
	 * Nó lấy context từ Global, thực thi trong 1 thread riêng biệt
	 * Sau khi thực hiện suspending functions thì nó chạy lai thread cũ hoặc 1 thread khác
	 */
	launch(Dispatchers.Default) {
		println("C2: ${Thread.currentThread().name}");	// Thread: T1
		delay(1000)
		println("C2: ${Thread.currentThread().name}");	// Thread: T1 hoặc thread khác
	}

	/**
	 * Tham số là Dispatchers.Unconfined 
	 * Kế thừa CoroutineContext từ coroutine cha
	 * Sau khi thực hiện suspending functions thì nó chạy trẻn 1 thread khác với thread cũ trước đó
	 */
	launch(Dispatchers.Unconfined) {
		println("C3: ${Thread.currentThread().name}");	// Thread: main
		delay(1000)
		println("C3: ${Thread.currentThread().name}");	// Thread: thread khác main
	}

	/**
	 * Sử dụng coroutineContext từ cha cho đến con
	 * Với coroutineContext là context của cha gần nhất
	 */
	launch(coroutineContext) {
		println("C4: ${Thread.currentThread().name}");	// Thread: main
		delay(1000)
		println("C4: ${Thread.currentThread().name}");	// Thread: main
	}
}
```

- Ngoài ra còn có Dispatchers `Main` và `IO` sẽ tìm hiểu trong Android :))