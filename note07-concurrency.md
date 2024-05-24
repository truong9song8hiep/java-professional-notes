# NOTE07: CONCURRENCY
## 1. Introducing Threads
Thread là một đơn vị thực thi nhỏ nhất mà hệ điều hành có thể tạo ra. Một tiến trình là một nhóm các thread cùng chạy và chia sẽ tài nguyên trên một môi trường (chia sẽ và sử dụng bộ nhớ).

Nhắc một chút về các biến và hàm static, chúng quả thực là được chia sẻ và sử dụng bởi nhiều luồng, khi một biến static được cật nhập thì mọi luồng khác đều biết đc sự thay đổi và có thể sử dụng nó.

### Distinguishing Thread Types 
mọi ứng dụng Java đều là đa luồng, ngay cả khi đó là một chương trình Hello World đơn giản, chúng ta sẽ chia ra luồng hệ thống và luồng người dùng tự định nghĩa.

luồng hệ thống được tạo ra bởi JVM và chạy dưới nền của chương trình, ví dụ luồng dọn rác. khi một luồng bị sự có nó sẽ gọi ra ngoại lệ hoặc lỗi.

luồng do người dùng xác định là luồng được tạo ra bởi lập trình viên để hoàn thành một công việc nào đó, tất cả các ứng dụng đều là đa luồng nhưng chỉ có một luồng do người dùng tự định nghĩa là luồng gọi hàm main().

### Understanding Thread Concurrency 
Đặc trưng của đa luồng là các tiến trình được thực thi một các đồng thời, nhưng CPU chỉ thực thi 1 lúc 1 thời điểm, ngay cả với cpu nhiều luồng, vậy làm thế nào mà hệ thống quyết định luồng nào sẽ được thực thi, OS sử dụng thread scheduler để xác đinh thread nào sẽ thực thi

### Introducing Runnable
```java
@FunctionalInterface public interface Runnable {
 void run();
}
```
giao diện runnable được gới thiệu ở các phiên bản java trước nhưng từ java 8 trờ đi thì nó mới dc giới thiệu là giao diện chức năng




