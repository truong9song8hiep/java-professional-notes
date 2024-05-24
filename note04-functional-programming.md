# Functional Programming
## Using Variables in Lambdas
Ở trong phần "Advanced Class Design" chúng ta đã nói về "effectively finally"




## Returning an Optional
Giả sử bạn đang tham gia một lớp học Java cơ bản và đạt điểm 90 và 100 cho hai bài kiểm tra đầu tiên, và từ đó bạn tính được điểm trung bình là (90 + 100)/2 = 95, tuyệt vời.

Tiếp theo bạn tham gia một lớp học Java nâng cao, và chúng tôi hỏi điểm trung bình của bạn là bao nhiêu? bởi vì bạn chưa tham gia làm bài kiểm tra nào nên số điểm trung bình của bạn là không biêt, không thể nói là 0 bởi vì thật sự bạn không tệ đến thế.

Trong Java 8 có một đối tượng để diễn đạt ý "chưa xác định" đó là Optional, ví dụ:
```java
public static Optional<Double> average(int… scores) {
  if (scores.length == 0) return Optional.empty();
  int sum = 0;
  for (int score: scores) sum += score;
  return Optional.of((double) sum / scores.length);
}
```

### Is Optional the Same as null?
Trước Java 8 người lập trình viên sẽ trả về `null` thay vì `Optional`. Cách viết này thường không rõ ràng, bởi vì `null` không phải là một kiểu trá trị mà ta muốn, còn `Optional`

Một lợi thế lớn của `Optional` so với `null` là nó cung cấp các hàm tiện ích để sử dụng như: `ifPresent()`, `orElse()`...

## Using Streams
Stream trong Java 8 là một API hỗ trợ việc thao tác với dữ liệu tuần tự, có nghĩa là Stream sẽ nhận dữ liệu từ một nguồn, sau đó tuần tự hóa các dữ liệu nhưng phòng lặp for, và thao tác với nó, sau đó trả về kết quả.

Stream bao gồm 3 phần:
- Source: nguồn phát dữ liệu, ví dụ I/O, các collection, Array...
- Intermediate operations (hoạt động trung gian): một chuỗi các hàm thao tác với các phần tử dữ liệu, các thao tác có thể là tìm kiếm, lọc, sửa... và sau mỗi thao tác, một stream mới sẽ được tạo ra.
- Intermediate operations (hoạt động cuối): gồm một hàm để trả về dữ liệu.

### Creating Stream Sources
Có một số cách để tạo ra một stream:
```java
Stream<String> empty = Stream.empty(); // count = 0
Stream<Integer> singleElement = Stream.of(1); // count = 1
Stream<Integer> fromArray = Stream.of(1, 2, 3); // count = 2
```
Những cách ở trên thường chỉ dùng để giới thiệu và ít xuất hiện trong thực tế, trong thực tế người ta sẽ gắn stream cho một collection.
```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> fromList = list.stream();
Stream<String> fromListParallel = list.parallelStream();
```
Còn có một cách để tạo ra một stream vô hạn nữa, ví dụ:
```java
Stream<Double> randoms = Stream.generate(Math::random);
Stream<Integer> oddNumbers = Stream.iterate(1, n -> n + 2);
```
### Using Common Terminal Operations
Khi làm việc với stream bạn có thể chỉ khai báo terminal operator mà không cần các intermediate operation, mà ngược lại thì không. chúng ta sẽ nói lý do sau.

reduction là một loại TO đặc biệt, nó gom tất cả element thành một kiểu nguyên thủy hoặc object:

#### Count()
hàm `long count()` đếm các phần tử của một stream hữu hạn, nếu gọi với steam vô hạn nó sẽ treo. hàm `count()` là recuction vì nó đếm hết phần tử trong stream. 
```java
Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
System.out.println(s.count()); // 3
```
#### min() and max()
min() và max() cho phép bạn truyền một bộ so sánh custom để tìm giá trị nhỏ và lớn nhất của một stream hữu hạn, giống như `count()`, min() và max() cũng không trả về giá trị khi làm việc với hàm vô hạn, và cả 2 cũng đều là reducer vì chúng phải đếm tất cả phần tử để trả về giá trị.
```java
Stream<String> s = Stream.of("monkey", "ape", "bonobo");
Optional<String> min = s.min((s1, s2) -> s1.length()—s2.length());
min.ifPresent(System.out::println); // ape
```
các hàm min max trả về một optional

#### findAny() and findFirst()
findAny và findFirst trả về phần tử, trừ khi là phần tử rỗng, chúng đều hoạt động với stream vô hạn, chúng là hoạt động cuối cùng nhưng không phải reducer bởi vì không cần phải duyệt hết toàn bộ phần tử mà chỉnh tìm phần tử đầu tiên.



