# NOTE 03: Generics And Collections
Trong phần này chúng ta sẽ tìm hiểu các collection trong java và cách sử dụng generic
## Review OCA
## 2. Working with Generics
Tại sao chúng ta cần generics? Hãy nhớ lại khi chúng ta không sử dụng kiểu

Một non-generic list có thể chứa các element với các kiểu dữ liệu khác nhau. Generic giúp bạn khắc phục vấn đề khác kiểu dữ liệu bằng cách chỉ định nó ngay lúc khai báo collection.

### Generic Classes
Bạn có thể gắn generic cho class của bạn. bằng cách đặt nó trong dấu <> ngay sau tên lớp.
```java
public class Crate<T> {
 private T contents;
 public T emptyCrate() {
 return contents;
 }
 public void packCrate(T contents) {
 this.contents = contents;
 }
}
```

Quy ước đặt tên generics:
- E cho các element.
- K cho key trong map.
- V cho value trong map.
- N cho number.
- T cho type chung
- s, u, v... cho nhiều loại chung.

Ví dụ chúng ta muốn di chuyển những con voi sang một chuồng thú lớn hơn
```java
Elephant elephant = new Elephant();
Crate<Elephant> crateForElephant = new Crate<>();
crateForElephant.packCrate(elephant);
Elephant inNewHome = crateForElephant.emptyCrate();
```
Và bây giờ chúng ta cũng muốn di chuyển những con ngựa vằn sang chuồng mới.
```java
Crate<Zebra> crateForZebra = new Crate<>();
```
chúng ta không đơn giản chỉ hard-code là Elepant bởi vì Zebra không phải Elepant, giải pháp là tạo một superclass là Animal và sử dụng nó trong Crate.

Generic tỏa sáng khi những kiểu dữ liệu không có bất cứ quan hệ gì với nhau, ví dụ, bạn cần vận chuyển một đám robot sang thành phố khác.
```java
Robot joeBot = new Robot();
Crate<Robot> robotCrate = new Crate<>();
robotCrate.packCrate(joeBot);

Robot atDestination = robotCrate.emptyCrate();
```
Trước khi có generic, để làm được như trên chúng ta phải sử dụng instace variable trong Create có kiểu là Object, điều này làm tăng gánh nặng cho Create bởi vì Create không cần biết nó chứa cái gì, và thứ mà nó chứa cũng không cần biết về nó. Việc sử dụng generic thường sẽ không xảy ra đối với code của bạn, mà nó thường được xuất hiện trong các thư viện và framework.

Generic class không bị giới hạn bởi một kiểu, bạn có thể khai báo 2,3 hoặc nhiều hơn.

Việc đặt generic là một trong những cách chúng ta "qua mặt" được trình biên dịch, Sự thật là trình biên dịch sẽ đặt Object cho các generic, nói cách khác sau khi biên dịch các generic thực chất là các object, điều này có nghĩa là chỉ có một file được sinh ra, chứ không phải nhiều file dựa vào kiểu chỉ định như một số ngôn ngữ khác làm. quá trình thay thế các generic được gọi là xóa kiểu (type erasure), điều này giúp nó tương thích với các bản java trở về trước khi chưa xuất hiện generic.

### Generic Interfaces
Giống như lớp, chúng ta cũng có thể gắn generic cho interface.
```java
public interface Shippable<T> {
 void ship(T t);
}
```
Có 3 cách để một class triển khai generic interface.
```java
class ShippableRobotCrate implements Shippable<Robot> {
 public void ship(Robot t) { }
}

class ShippableAbstractCrate<U> implements Shippable<U> {
 public void ship(U t) { }
}

class ShippableCrate implements Shippable {
 public void ship(Object t) { }
}
```
#### What You Can’t Do with Generic Types
Một số hạn chế khi làm việc với generic type, 

### Generic method

## 3. Using Lists, Sets, Maps, and Queues
Collection là một đại diện cho một tập hợp các đối tượng. **Java Collection Framework** là một bộ các lớp nằm trong `java.util` dùng để lưu trữ các collection. Có 4 interface chính trong Java Collection Framework:
- List: một tập hợp các phần tử theo thứ tự, cho phép trùng lặp, có thể truy cập thông qua index.
- Set: không cho phép trùng lặp
- Map: lưu trữ theo key/value, key không được trùng.
- Queue: theo thứ tự vào trước ra trước.
List, Set và Queue đều kế thừa từ Collection nên chúng sẽ có thể sử dụng các hàm trong Collection, còn Map thì không kế thừa nên không thể sử dụng.
### Common Collections Methods
```java
### *add()*
### *remove()*
### *isEmpty() and size()*
### *clear()*
hàm `void clear()` xóa mọi phần tử khỏi collection.
```
### contains()
### Using the List Interface
Bạn sử dung List khi bạn muốn lưu trữ các đối tượng có thể truy xuất bằng index và cho phép trùng lặp.
### Một số lớp triển khai của List
### Một số hàm của List
```java
List<String> animal = new ArrayList
```
### Using the Set Interface
Chúng ta sử dụng Set khi không muốn trùng lặp phần tử.

### Using the Queue Interface

### Map
Map được sử dụng khi muốn lưu trữ dữ liệu dưới dạng key/value.
#### Các triển khai của Map
```java
// 1. HashMap
// 2. LinkedHashMap
// 3. TreeMap
// 4. ConcurrentHashMap
```
#### Các phương thức của Map

### So sánh các collection
- List:

## Comparator vs. Comparable
