# NOTE07: CONCURRENCY
Trong thực tế, các vấn đề về đa luồng và đồng thời luôn gây khó khăn kể cả với những lập trình viên kỳ cựu nhất. Ngay từ những ngày đầu, Java đã hỗ trợ đa luồng bằng cách sử dụng lớp `Thread`. Năm 2004, Java 5 đã được phát hành với một loạt các tiện ích mới trong đó có các API hỗ trợ đa luồng nằm trong gói `java.util.concurrent`.

## 1. Introducing Threads
Chúng ta bắt đầu phần học này với các khái niệm về lập trình đa luồng. Thread là một đơn vị thực thi nhỏ nhất mà hệ điều hành có thể tạo ra. Một tiến trình là một nhóm các thread cùng chạy và chia sẽ tài nguyên trên một môi trường (chia sẽ và sử dụng bộ nhớ). Do đó một tiến trình đơn luồng ( single-threaded process) là một tiến trình chỉ có duy nhất một luồng chạy, trong khi một tiến trình đa luồng là tiến trình chứa một hoặc nhiều luồng. Một task là đơn vị của một luồng.

Nhắc một chút về các biến và hàm static, chúng quả thực là được chia sẻ và sử dụng bởi nhiều luồng, khi một biến static được cật nhập thì mọi luồng khác đều biết đc sự thay đổi và có thể sử dụng nó.

### Distinguishing Thread Types 
Mọi ứng dụng Java đều là đa luồng, ngay cả khi đó là một chương trình Hello World đơn giản, chúng ta sẽ chia ra luồng hệ thống và luồng người dùng tự định nghĩa.

luồng hệ thống (System thread) được tạo ra bởi JVM và chạy dưới nền của chương trình, ví dụ luồng dọn rác. Hầu hết các ST đều vô hình đối với lập trình viên. khi một luồng bị sự cố không thể phục hồi nó sẽ tạo ra một Java Error ngược lại thì là Exception.

luồng do người dùng xác định (user-defi ned thread) là luồng được tạo ra bởi lập trình viên để hoàn thành một công việc nào đó, tất cả các ứng dụng đơn giản mà ta viết trước đây đều là đa luồng nhưng chỉ có một luồng do người dùng tự định nghĩa là luồng gọi hàm main().

### Understanding Thread Concurrency 
Chúng ta biết rằng tiến trình đa luồng được hệ điều hành xử lý các luồng cùng một thời gian, hay còn gọi là *đồng thời*. nhưng CPU chỉ thực thi 1 lúc 1 thời điểm, ngay cả với cpu nhiều luồng, vậy làm thế nào mà hệ thống quyết định luồng nào sẽ được thực thi, OS sử dụng thread scheduler để xác đinh thread nào sẽ thực thi. Nó sẽ lập lịch theo vòng tròng, ví dụ với 10 luồng, nó sẽ thực thi mỗi luồng với khoảng 10 mili giây, sau đó cứ lặp đi lặp lại. gọi là Round-Robin Scheduling. Nếu một luồng chưa được xử lý xong trong khi thời gian đã hết một tiến trình *context switch* sẽ được thực thi, chúng là các công việc sao lưu trạng thái của luồng hiện tại và khôi phục trạng thái của luồng tiếp theo. Công việc này thường tốn kém chi phí. 

Đôi khi một luồng có thể được ưu tiên thực thi hơn một luồng khác nhờ *mức độ ưu tiên*, mức độ ưu tiên là một con số được thread scheduler đánh giá xem thread đó có được ưu tiên hay không. Trong Java nó là con số từ 1(ưu tiên thấp)> 5(trung bình)> 10(ưu tiên cao).

### The Importance of Thread Scheduling
Mặc dù máy tính đa nhân (multi-core) ngày càng trở nên phổ biến, các kỹ thuật và thuật toán lập lịch luồng (thread scheduling) vẫn rất quan trọng, đặc biệt là trên các hệ thống đơn nhân (single-core). Các hệ điều hành hiện đại sử dụng các thuật toán lập lịch phức tạp để quản lý việc thực thi các luồng và quá trình (process) một cách hiệu quả, tạo cảm giác rằng máy tính có thể thực thi nhiều tác vụ cùng lúc.

### Introducing Runnable
Runnable là một funtional inteface mà nó không nhân bất cứ tham số nào và cũng không trả về một giá trị nào. giao diện runnable được gới thiệu ở các phiên bản java trước nhưng từ java 8 trờ đi thì nó mới dc giới thiệu là giao diện chức năng

Nhắc một chút về functional inteface: nó cho phép bạn tạo đối tượng bằng hàm.
```java
@FunctionalInterface public interface Runnable {
 void run();
}
```
Chúng ta sử dụng Runnable khi muốn thực thi một công việc ở một luồng tách biệt với luồng main. Các hàm dưới đây là dựa trên Runnable:
```java
() -> System.out.println("Hello World")
() -> {int i=10; i++;}
() -> {return;}
() -> {}
```
Mặc dù từ Java 8, Runnable được đánh dấu là @functionalInteface nhưng bạn vẫn có thể triển khai với class riêng khi mà mã trong hàm run khá phức tạp.

###  Creating a Thread 
Cách đơn giản nhất để tạo một thread là sử dụng lớp `Thread`, ta định nghĩa các công việc và gọi hàm `Thread.start()` Nhớ rằng java không đảm bảo về thứ tự thực thi thread.

Có 2 cách để viết những công việc cho thread của bạn: cách 1 truyền một Runnable vào constructor, cách 2 kế thừa Thread và override lại hàm run().
```java
new Thread(() -> System.out.println("Hello World"))).start();
public class ReadInventoryThread extends Thread {
 public void run() {
 System.out.println("Printing zoo inventory");
 }
 public static void main(String[] args) {
 (new ReadInventoryThread()).start();
 }
```
Bất cứ khi nào bạn tạo một Thread instance, hãy nó gọi hàm Thread.start() để bắt đầu chạy, và hệ điều hành sẽ tách nó ra thực thi trong một luồn riêng biệt.
```java
public static void main(String[] args) {
 System.out.println("begin");
 (new ReadInventoryThread()).start();
 (new Thread(new PrintData())).start();
 (new ReadInventoryThread()).start();
 System.out.println("end");
}
```
Kết quả sẽ khác sau mỗi lần chạy lại, bởi vì các luồng không được thực thi theo thứ tự. Mặc dù thứ tự luồng thực thi không đảm bảo nhưng code bên trong nó thì vẫn theo thứ tự. Và hãy lưu ý rằng một thread chỉ bắt đầu khi hàm start() của nó được gọi. và nếu ta gọi hàm run thay vì start thì chúng vẫn sẽ chạy, nhưng hàm run sẽ thực thi trên luồng gọi nó chứ không phải luồng riêng biệt.

Nhìn chung việc tạo thread bằng kế thừa Thread chỉ được khuyến khích khi bạn muốn xác định mức độ ưu tiên, còn việc tạo thread bằng Runnable vẫn được khuyến khích hơn. Và thậm chí từ Java 8, việc tạo thread bằng các Concurrency API còn được khuyến khích hơn Runnable.

### For Developer: For Interviews, Be Familiar with Thread-Creation Options
Mặc dù trong thực tế người ta ít còn sử dụng Thread và Runnable để tạo thread, nhưng trong các câu hỏi phỏng vấn vẩn đề cập đến chúng và sự khác nhau giữa chúng:
- kế thừa Thread được dùng khi: muốn thread có mức độ ưu tiên,
- triển khai runable: khi muốn tách biệt code, kế thừa lớp khác, sử dụng một số Concurrency API.

### Polling with Sleep
Đôi khi bạn cần kiểm tra kết quả của một phép tính nằm trong một thread. việc đó gọi là Polling. ví dụ: bạn có một hàm tạo 1 thread đếm 500 lần, và bạn muốn nếu đếm <100 thì in ra chưa còn ngược lại thì in ra rồi
```java
public class CheckResults {
 private static int counter = 0;
 public static void main(String[] args) {
 new Thread(() -> {
 for(int i=0; i<500; i++) CheckResults.counter++;
 }).start();
 while(CheckResults.counter<100) {
 System.out.println("Not reached yet");
 }
 System.out.println("Reached!");
 }
}
```
kết quả có thể là 0, 1 hoặc 1 triệu lần, vì sao vậy? Vấn đề là luồng while không chờ đợi luồng đếm mà nó cứ đếm liên tục, cho đến khi count đủ 100. khi này chúng ta có thể cải tiến nó bằng hàm sleep(), hàm sleep yêu cầu luồng hiện tại ngừng trong vòng n mili giây, nếu dùng nó trong hàm main thì luồng chạy hàm main sẽ dừng lại vài mili giây để luồng đếm có thể hoạt động. chugns ta vẫn không biết khi nào while() lọc bao nhiêu lần, nhưng sẽ đảm bảo không bị vòng lặp vô tận. 

Một vấn đề khác là sẽ ra sao nếu có một luồng thay đổi giá trị của biến static đó, việc các luồng cùng truy cập vào cùng 1 tài nguyên cũng là một vấn đề rất phức tạp mà chúng ta sẽ bàn luận ở chương sau: 

## 2. Creating Threads with the ExecutorService
Java cung cấp interface `ExecutorService ` hỗ trợ việc tạo và quản lý nhiều thread.



### Waiting for Results
Làm thế nào chúng ta biết được khi nào một task mà chúng ta gửi đến `ExecutorService` hoàn thành? Như đã đề cập ở phần trước, hàm `submit()` trả về một đối tượng `java.util.concurrent.Future<V>` có thể xác định kết quả.
```java
Future<?> future = service.submit(() -> System.out.println("Hello Zoo"));
```
interface `Future` có một số hàm để xác định trạng thái của task.
```java
// isDone(): trả về true nếu nó hoàn thành, ném ngoại lệ hoặc bị hủy
future.isDone();
//
isCancelled

cancel
get
get
```

---
```java
public class Test {
    private static int counter = 0;

    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService service = null;
        try {
            service = Executors.newSingleThreadExecutor();
            Future<?> result = service.submit(() -> {
                Test CheckResults;
                for (int i = 0; i < 500; i++) Test.counter++;
            });
            result.get(10, TimeUnit.SECONDS);
            System.out.println("Reached!");
        } catch (TimeoutException e) {
            System.out.println("Not reached in time");
        } finally {
            if (service != null) service.shutdown();
        }
    }
}
```
Ví dụ ở trên không sử dụng thread nhưng cũng có hiệu quả tương tự, nó trả về một `Future<V>` bởi vì Runnable.run() is void, the get() method always returns null.

### Introducing Callable
interface `Callable` được thêm vào Java 5 cùng với gói ` java.util.concurrent`, nó quen thuộc với runnable trừ hàm `call()`
```java
@FunctionalInterface public interface Callable<V> {
 V call() throws Exception;
}
```
Callable được giới thiệu như là một thay thế cho Runnable bởi vì nó cho phép lấy chi tiết hơn sau khi đã hoàn thành. 


### Ambiguous Lambda Expressions: Callable vs. Supplier
Bạn có thể để ý rằng `Callable` rất giống với `Supplier`, chúng đều không nhận đối số và trả về một generic type, làm thế nào để phân biệt chúng? câu trả lời thật đáng buồn lại là **Không thể phân biệt**. Xem sét ví dụ sau:
```java
public class AmbiguousLambdaSample {
 public static void useCallable(Callable<Integer> expression) {}
 public static void useSupplier(Supplier<Integer> expression) {}
 public static void use(Supplier<Integer> expression) {}
 public static void use(Callable<Integer> expression) {}
 public static void main(String[] args) {
 useCallable(() -> {throw new IOException();}); // COMPILES
 useSupplier(() -> {throw new IOException();}); // DOES NOT COMPILE
 use(() -> {throw new IOException();}); // DOES NOT COMPILE
 }
}
```
Chúng ta thấy rằng Callable cho phép ném ngoại lệ còn Supplier thì không, còn về hàm overload user thì sao? trình biên dịch không tính đến việc body ném ngoại lệ.  Khi một trình biên dịch không thể gán functional inteface cho một lamda epresion, nó được gọi là ambiguous lambda expression. Để giải quyết bạn chỉ cần ép kiểu cho nó.
```java
use((Callable<Integer>)() -> {throw new IOException("");}); // COMPILES
```
hàm `get()` của runnable trả về null, còn hàm `get` của Callable trả về một generic type hoặc là null. nó cũng chính là lý do lớn để callable thay thế runnable

### Checked Exceptions in Callable and Runnable
Một lợi thế nữa là Runable hỗ trợ ném checked exception trong khi runnable thì không

### Waiting for All Tasks to Finish
Sau khi gửi một tập các tác vụ cho  thread executor, công việc tiếp theo là ngồi đợi. 


## 3. Synchronizing Data Access
Có 2 luồng cùng truy cập vào một biến, làm sao để ngăn việc này.



## 4. Using Concurrent Collections
bên cạnh việc quản lý thread, `Concurrency` API cũng bao gồm một số interface và class mà nó giúp bạn làm việc với collection một cách đồng thời.

### Introducing Concurrent Collections
Một câu hỏi đầu tiên: chúng ta có thật sự cần Concurrent collection? Trong phần trước chúng ta biết la mình có thể sử dụng từ khóa `synchronous` trên bất kỳ hàm và khối nào, vậy có thể dùng nó cho collection dc không? câu trả lời là dc. ví đụ:
```java
public class ZooManager {
 private Map<String,Object> foodData = new HashMap<String,Object>();
 public synchronized void put(String key, String value) {
 foodData.put(key, value);
 }
 public synchronized Object get(String key) {
 return foodData.get(key);
 }
}
```
vậy tại sao chúng ta lại sử dụng đồng thời, 
```java
private Map<String,Object> foodData = new ConcurrentHashMap<String,Object>();
public void put(String key, String value) {
 foodData.put(key, value);
 }
 public Object get(String key) {
 return foodData.get(key);
 }
```
### Understanding Memory Consistency Errors
mục đích của concurrent collection là giải quyết vấn đề Memory Consistency Errors, Memory Consistency Errors sảy ra khi 2 thread cùng trỏ đến một biến 


