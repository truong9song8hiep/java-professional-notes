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
Khi ta muốn in giá trị bằng System.out.println(object); thì hàm toString của object sẽ được gọi tự động. Việc ghi đè hàm toString giúp chúng ta dễ dàng gỡ lỗi và biết thêm thông tin của đối tượng, tuy nhiên sẽ thật là mệt mõi nếu ta phải in ra thông tin của một lớp có quá nhiều variable, bạn có thể tham khảo một số thư viện có sẵn để trợ giúp cho bạn, ví dụ:
```java
// sử dụng một số thư viện nổi tiếp để hỗ trợ bạn như Apache Commons Lang
public String toString() {
 return ToStringBuilder.reflectionToString(this); // kết quả: toString.Hippo@12da89a7[name=Harry,weight=3100.0]
 return ToStringBuilder.reflectionToString(this, ToStringStyle.SHORT_PREFIX_STYLE); // kết quả: Hippo[name=Harry,weight=3100.0]
}
```
## equal
Hãy nhớ rằng, sử dụng phép `==` khi bạn muốn so sánh 2 kiểu dữ liệu nguyên thủy hoặc 2 biến có cùng tham chiếu đến một đối tượng hay không. Để kiểm tra 2 biến có giống nhau không thì phải sử dụng `equal`, hoặc ít nhất đối tượng đó ghi đè hàm equal
```java
String s1 = new String("lion");
String s2 = new String("lion");
System.out.println(s1.equals(s2)); // true
StringBuilder sb1 = new StringBuilder("lion");
StringBuilder sb2 = new StringBuilder("lion");
System.out.println(sb1.equals(sb2)); // false
```
String có hàm equal, nó sẽ kiểm tra chuỗi, còn StringBuilder thì không, nó sẽ gọi hàm equal của Object, và trong hàm equal của object nó sẽ kiểm tra 2 đối tượng có cùng tham chiếu đếm 1 object hay không?

Vì hàm equal rất quan trọng nên Java có đưa ra một số luật về hàm này:
- nếu x null thì ngoại lễ NullPointException sẽ xảy ra trong lúc runtime.
- nếu x không null thì `x.equal(x)` luôn là true.
- với x không null thì `x.equal(null)` luôn là false.
- nếu x và y không null thì `x.equal(y)` sẽ giống `y.equal(x)`
- nếu x,y,z không null thì `x.equal(y)` true và `y.equal(z)` true thì `z.equal(x)` chắc chắn true.

Thư viện Apache Commons Lang cũng hỗ trợ chúng ta ghi đè hàm equal như hàm toString:
```java
public boolean equals(Object obj) {
 return EqualsBuilder.reflectionEquals(this, obj); // so sánh tất cả biến
// so sánh một vài trường chỉ định
return new EqualsBuilder().appendSuper(super.equals(obj))
 .append(idNumber, other.idNumber)
 .append(name, other.name)
 .isEquals();
}
```

### hashCode
Bất cứ khi nào bạn ghi đè hàm `equal()` thì bạn cũng phải ghi đè hàm `hashCode()`, hash code (mã băm) được dùng để lưu trữ đối tượng như là một key trong map, chúng ta sẽ nói thêm ở phần "Collection vs Generic"

Mã băm là một con số mà nó dùng đê xác định đối tượng trong bộ nhớ.

Trang web chính thức của Java nói về các luật trog mã băm khá là khó hiểu nhưng bạn chỉ cần nắm vài điều sau đây:
- trong cùng 1 chương trình thì mã băm là không thể thay đổi vì vậy nên dùng các biến không thay đổi như id và tên.
- nếu 2 đối tượng bằng nhau bằng hàm `equal` thì 2 hàm đó sẽ có chung một kết quả khi gọi hàm `hashcode()`

## Working with Enums
Trong Java Enum là một lớp đại diện cho một sự liệt kê, sử dụng từ khóa `emun` để khai báo một enum;
```java
public enum Seasion {
SPRING, SUMMER, FALL, WINTER
}
```
Enum không thể được mở rộng.
### Using Enums in Switch Statements
Emun được sử dụng trong swicht
```java
Season summer = Season.SUMMER;
switch (summer) {
case WINTER:
 System.out.println("Get out the sled!");
 break;
 case SUMMER:
 System.out.println("Time for the pool!");
 break;
 default:
 System.out.println("Is it summer yet?");
}
```
### Adding Constructors, Fields, and Methods
Emun không chỉ chứa các giá trị mà nó còn có thể chứa, constructor, các trường và hàm.

## Creating Nested Classes
Một nested class là một class được định nghĩa bên trong một class khác. Một nested class mà không phải là static thì được gọi là inner class. Có 4 loại inner class:
- Member inner class là một inner class mà nó cùng cấp với instance variable, nó không phải là static.
- Local inner class được định nghĩa bên trong một method.
- Anonymous class là một trường hợp đặc biệt của inner class là chúng không có tên
- static nested class là một lớp static mà nó được định nghĩa cùng cấp với static member.
### Member Inner Classes
Member inner class là một inner class mà nó cùng cấp với các member components, một member inner class có một số tính chất sau:
- có thể khai báo là `public`, `protected`, `private` hoặc defaul access.
- có thể kế thừa 1 class hoặc triển khai các interface.
- có thể là abstract và final
- không thể khai báo static field và static method.
- có thể truy cập vào các member của outer class, ngay cả khi đó là private
```java
public class Outer {
 private String greeting = "Hi";

 protected class Inner {
 public int repeat = 3;
 public void go() {
 for (int i = 0; i < repeat; i++)
 System.out.println(greeting);
 }
 }

 public void callInner() {
 Inner inner = new Inner();
 inner.go();
 }
 public static void main(String[] args) {
 Outer outer = new Outer();
 outer.callInner();
 } }
```
Vì member inner class là không static nên, muốn sử dụng phải khởi tạo nó thông qua outer class.
```java
Outer outer = new Outer();
Inner inner = outer.new Inner(); // hợp lệ
inner.go();
```
Khi biên dịch, 2 file sẽ được tạo ra `Outer.java` và `Outer$Inner.class`, nếu bạn thấy file có `$` thì đó chính là inner class.

inner class có thể có variable cùng tên với variabler outer class

Chúng ta có thể khai báo một interface bên trong một class gióng như inner class, các hàm và variable bên trong interace này vẫn giữ nguyên tắc là public, tuy nhiên bản thân interface có thể là private hoặc protected.

### Local Inner Classes
