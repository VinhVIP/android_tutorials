# Coroutines trong Kotlin

# 3. Cancel a Coroutine

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

```java
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

Giải thích: Khi thêm `start = CoroutineStart.LAZY`, chương trình chạy đến khi gặp `await()` thì `async` coroutine mới được chạy. 