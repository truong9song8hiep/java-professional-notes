# Note01: Advanced Class Design
## Reviewing OCA Concepts
Chúng ta sẽ kiểm tra lại một số kiến thức chính trong phần ... của ioc
## Unsing *instanceof*
Bây giờ chúng ta sẽ bắt đầu với một chủ đề mới, chúng ta đã nắm được một số toán tử trong java như `<`, `<=`... bây giờ chúng ta sẽ làm quen với một toán tử mới gọi là `instanceof`.

Khi một `a instanceof b`, biểu thức này trả về true nếu như A là một thể hiện của một class B, một subclass trực tiếp hoặc gián tiếp của B, hoặc một class mà nó triển khai interface B (trực tiếp hoặc gián tiếp), ví dụ:
```java
// cho 3 class
class HeavyAnimal { }
class Hippo extends HeavyAnimal { }
class Elephant extends HeavyAnimal { }

HeavyAnimal hippo = new Hippo();
boolean b1 = hippo instanceof Hippo; // true
boolean b2 = hippo instanceof HeavyAnimal; // true
boolean b3 = hippo instanceof Elephant; // false
```
Mặc dù ta khai báo kiểu dữ liệu của `hippo` là `HeavyAnimal` nhưng java vẫn biết được nó đang trỏ tới kiểu dữ liệu `Hippo` trong lúc chạy.

Trong Java, tất cả các lớp đều kế thừa từ một siêu lớp là Object vì vậy biểu thức `... instanceof Object` đều là true, có một ngoại lệ duy nhất là khi ta gán cho đối tượng đó = null, ví dụ:
```java
HeavyAnimal hippo = new Hippo();
boolean b4 = hippo instanceof Object; // true
Hippo nullHippo = null;
boolean b5 = nullHippo instanceof Object; // false
```
Đôi lúc khi chúng ta sử dụng toán tử instanceof thì Java sẽ báo lỗi biên dịch thay vì trả kết quả về `true/false` khi mà 2 class này không có mối liên hệ gì với nhau. Và chuyện này cũng chỉ áp dụng khi so sánh với lớp, còn với interface thì Java Compier lại bỏ qua. Ví dụ:
```java
public interface Mother {}
class Hippo extends HeavyAnimal { }

Hippo anotherHippo = new Hippo();
boolean b5 = anotherHippo instanceof Elephant; // Lỗi biên dịch
boolean b6 = hippo instanceof Mother; // không lỗi biên dịch
```
toán tử `instanceof` thường sẽ kiểm tra nó có phải là instance của subclass của class được so sánh hay không trước khi thực hiện ép kiểu.

Việc viết biểu thức có `instanceof` trong if là rất hiếm gặp và không được khuyến khích.

## Understanding Virtual Method Invocation
Chúng ta vừa có một ví dụ về việc cho ăn của Animal ở trên, trông khá là ngớ ngẩn, cách giải quyết là để cho mỗi loại tự quyết định chúng sẽ ăn cái gì:
```java
abstract class Animal { public abstract void feed(); }}

class Cow extends Animal {
 public void feed() { addHay(); }
 private void addHay() { }
}
class Bird extends Animal {
 public void feed() { addSeed(); }
 private void addSeed() { }
}
class Lion extends Animal {
 public void feed() { addMeat(); }
 private void addMeat() { }
}
```
Các lớp kế thừa từ Animal đều phải định nghĩa hàm `feed()` và trong hàm `feed()` chúng ta sẽ ủy quyền cho từng loại quyết định chúng ăn gì.
```java
public void feedAnimal(Animal animal) {
 animal.feed();
}
```

Ở ví dụ trên chúng ta có một Animal instace nhưng Java không gọi hàm feed của Animal, thay vào đó nó sẽ tìm kiểu dữ liệu thực tế trong lúc run time và gọi hàm feed của lớp đó. nó gọi là `virtual method invocation`. Và lưu ý là nó chỉ áp dụng cho method (vì vậy mới có thê virtual method invocation), còn các instance variable thì không hoạt động như vậy, nó vẫn sẽ lấy giá trị trong Animal. Một ví dụ khác:
```java
abstract class Animal {
 public void careFor() {
 play();
 }
 public void play() {
 System.out.println("pet animal");
 } }
class Lion extends Animal {
 public void play() {
 System.out.println("toss in meat");
 } }
public class PlayWithAnimal {
 public static void main(String... args) {
 Animal animal = new Lion();
 animal.careFor();
 } }
```
Kết quả sẽ là `toss in meat`, đúng vậy hàm main tạo ra một Lion object, và gọi careFor, nhưng chỉ có Animal mới có careFor nó sẽ thực thi, hàm careFor sẽ gọi play, và một lần nữa Java sẽ nhìn vào bên trong Lion, nếu có thì nó sẽ gọi hàm của Lion.

## Annotating Overridden Methods
Annotation là một loại metadata nó cung cấp thêm thông tin của chương trình.

@Override anotation được sử dụng trước 1 method và cho biết rằng method đó ghi đè một method của lớp cha hoặc interface mà nó implement

@Override có thể sử dụng trên một dòng
```java
@Override public void findDen() { } // hợp lệ
```
Việc gắn @Override là tùy chọn, chúng ta có thể không cần nhưng được khuyến khích giúp java kiểm tra lỗi giúp bạn trong quá trình biên dịch, các thay đổi từ lỗi đánh máy haowcj từ chính hàm cha, nó sẽ báo hiệu cho bạn biết.

Như đã nói @Override chỉ xuất hiện trên method, không thể xuất hiện trên variable, và trong một số bài kiểm tra, các phương thức thường sẽ bị ẩn đi @override để đánh lạc hướng người làm bài, và nếu như xuất hiện @overrdie thì phải chắc chắn rằng: lớp đó kế thừa hoặc triển khai 1 giao diện, và ghi đè đúng với hàm mà nó muốn, hoặc các hàm của Object mà chúng ta biết như hashCode, equals, or toString.


## equals, hashCode, and toString
Tất cả các lớp trong Java đều kế thừa từ lớp Object, cho dù lớp đó là lớp có sẵn hay là do bạn tự định nghĩa. Lớp Object có 3 hàm là equals, hashCode, and toString, vì vậy chúng ta có thể override lại 3 hàm này.
### toString




