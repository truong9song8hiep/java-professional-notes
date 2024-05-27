# NOTE 10: JDBC
JDBC là viết tắt của Java Database Connectivity
## Introducing Relational Databases and SQL
...
## 2. Introducing the Interfaces of JDBC
Có 4 inteface chính mà chúng ta sẽ làm việc với JDBC, và những lớp triển khai nó được các nhà cung cấp sẵn trong file jar, 
Driver: Knows how to get a connection to the database
Connection: Knows how to communicate with the database
Statement: Knows how to run the SQL
ResultSet: Knows what was returned by a SELECT query

Một kết nối bằng jdbc điển hình:
```java
package com.wiley.ocp.connection;
import java.sql.*;
public class MyFirstDatabaseConnection {
 public static void main(String[] args) throws SQLException {
 String url = "jdbc:derby:zoo";
 try (Connection conn = DriverManager.getConnection(url);
 Statement stmt = conn.createStatement();
 ResultSet rs = stmt.executeQuery("select name from animal")) {
 while (rs.next())
 System.out.println(rs.getString(1));
 }
 }
}
```

## 3. Connecting to a Database
Bước đầu tiên là kết nối với database, để kết nối với database, chúng ta cần một url trỏ tới database đó. Một jdbc url là một chuỗi gồm 3 phần cách nhau bởi dấu `:`, ví dụ: `jdbc:postgres://localhost:5432/zoo`, phần đầu tiên là chung `jdbc`, phần thứ 2 xác định loại db, phần cuối xác định đường đãn đến nó.

Tiếp theo sử dụng hàm `DriverManager.getConnection(String url)` để thực hiện kết nối, nếu thành công, một đối tượng Connection sẽ được trả về

 một cách thứ 2 là sử dụng DataSource, cách này được sử dụng trong thực tế vì nó nhiều tính năng hơn, ví dụ kết nối pool, lưu trữ kết nối ngoài ứng dụng...

 ## 4. Obtaining a Statement
Sau khi kết nối xong, bạn cần một "người ra lệnh" để thực thi SQL, "người ra lệnh" có nhiệm vụ tuyên bố câu lệnh sql cho database, inteface `Statement` đại diện cho "người ra lệnh" đó. 
```java
Statement stmt = conn.createStatement();
Statement stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);
```
### Choosing a ResultSet Type 
Mặc định  ResultSet is in TYPE_FORWARD_ONLY mode

##  Executing a Statement
Sau khi đá có "ngườ ra lệnh" Statment, chúng ta sẽ tiến hành ra lệnh cho database thực thi sql bằng các hàm có sẵn, tùy thuộc vào loại lệnh nào mà bạn muốn, các câu lệnh làm thay đổi kích thước và cấu trúc của bảng như: DELETE , INSERT , or UPDATE chúng ta dùng hàm `executeUpdate()`, kết quả trả về sẽ là một biến int, là số hàng mà bị ảnh hưởng từ câu lệnh
```java
Statement stmt = conn.createStatement();
12: int result = stmt.executeUpdate(
13: "insert into species values(10, 'Deer', 3)");
14: System.out.println(result); // 1
15: result = stmt.executeUpdate(
16: "update species set name = '' where name = 'None'");
17: System.out.println(result); // 0
18: result = stmt.executeUpdate(
19: "delete from species where id = 10");
20: System.out.println(result); // 1
```
lệnh SELECT được thực thi với `executeQuery()`, nó trả về một ResultSet, chúng tá sẽ tìm hiểu nó sau
```java
ResultSet rs = stmt.executeQuery("select * from species");
```
hàm `excute()` cho phép cả 2 loại sql ở trên, nó trả về boolean, và trả về true, nếu kết quả là một resultset.
```java
boolean isResultSet = stmt.execute(sql);
if (isResultSet) {
 ResultSet rs = stmt.getResultSet();
 System.out.println("ran a query");
} else {
 int result = stmt.getUpdateCount();
 System.out.println("ran an update");
}
```
### The Importance of a PreparedStatement
Trong dự án thực tế, thay vì sử dụng `Statement` người ta thường dùng `PreparedStatement` nó là một subinteface với 3 lợi thế: hiệu suất, bảo mật, và khả năng đọc.

bởi vì các câu lệnh sql là string nên ta có thể truyền các câu lệnh sai mà chương trình không biết lúc biên dịch. và ngoại lệ sẽ ném ra

## Getting Data from a ResultSet
Sau khi try vấn, bạn sẽ nhận được một ResultSet
### Reading a ResultSet
ResultSet đại diện cho một tập dữ liệu, chúng ta phải sử dụng hàm `.next()` để duyệt qua từng dòng dữ liệu, mỗi lần duyệt là một lần lọc qua 
```java
Map<Integer, String> idToNameMap = new HashMap<>();
21: ResultSet rs = stmt.executeQuery("select id, name from species");
22: while(rs.next()) {
23: int id = rs.getInt("id");
24: String name = rs.getString("name");
25: idToNameMap.put(id, name);
26: }
27: System.out.println(idToNameMap); // {1=African Elephant, 2=Zebra}
```
Một ResultSet có một con trỏ, thứ chỉ đến vị trí hiện tại của dữ liệu, chúng ta cũng có thể truy xuất bằng index thay vì tên cột
```java
int id = rs. getInt (1);
String name = rs. getString (2);
```
index bắt đầu từ 1, và việc truy xuất bằng tên cột vẫn được khuyên dùng vì lý do dễ đọc. Đôi khi ta chỉ lấy dòng đầu tiên thì sử dụng if thay vì while
 khi một statement.next trả về false, sau đó bạn có lấy kết quả thì exception sẽ được ném ra. việc cứ luôn trỏ đến 1 vị trí cũng nếm ngoại lệ, và lấy ra một cộ không có trong bảng cũng ném ra nôt

 ### Getting Data for a Column
một số hàm để lấy giá trị `getInt()`, `getDouble()`.....
khi trả về ngày, chúng nằm trong gói sql, chúng ta phải chuyển nó về gay từ ultil để sử dụng
```java
java.sql.Time sqlTime = rs.getTime(1);
 LocalTime localTime = sqlTime.toLocalTime();
 System.out.println(localTime); // 02:15

java.sql.Timestamp sqlTimeStamp = rs.getTimestamp(1);
 LocalDateTime localDateTime = sqlTimeStamp.toLocalDateTime();
 System.out.println(localDateTime); // 2001―05―06T02:15
```
### Scrolling ResultSet
ngoài hàm next để hướng con trỏ đến vị trí kế tiếp, ta cũng có thể lùi con trỏ bằng previous(), getFirst() và last(), trả về boolean xem nó có phải trị trí đầu cuối hay không, beforeFirst() and afterLast() luôn trả về void bởi vì nó sẽ trỏ đến noi không có dữ liệu, một hàm khác là absolute(), nó trỏ đến một ví trí tuyệt đối ví dụ absolute(0), .... hàm relative(), 

## Closing Database Resources


### Dealing with Exceptions
SQLException có 3 hàm để xem thông tin về exception, getMessage, getSQLState, getErrorCode

