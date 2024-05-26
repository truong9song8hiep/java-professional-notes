# NOTE05: Dates, Strings, and Localization
## Working with Dates and Times
Java 8 đã cải tiến hoàn toàn cách làm việc với Ngày và Giờ. bạn vẫn có thể sử dụng những lớp cũ nhưng chúng bị đánh giá là lỗi thời và không dùng nữa.
### Creating Dates and Times
Java 8 cung cấp một số lớp mới định nghĩa ngày mà nó rõ ràng hơn lớp Date mà ta từng biết.
- LocalDate: cung cấp ngày tháng, không thời gian, không múi giờ.
- LocalTime: cung cấp thời gian, không ngày tháng, không múi giờ.
- LocalDateTime: cung cấp ngày tháng + thời gian, không múi giờ.
- ZoneDateTime: cung cấp ngày tháng, giời gian, múi giờ.

Oracler khuyên bạn tránh sử dụng múi giờ trừ khi cần thiết, sử dụng hàm `static now()` để trả về giá trị hiện tại:
```java
System.out.println(LocalDate.now()); // 2015–05–25
System.out.println(LocalTime.now()); // 09:13:07.768
System.out.println(LocalDateTime.now()); //2015–05–25T09:13:07.768
System.out.println(ZonedDateTime.now()); //2015–05–25T09:13:07.769–04:00[America/New_York]
```
múi giờ UTC và GMT
UTC được sử dụng ở một số nước châu âu và châu phi, thời gian dc hiển thị là 0-24 hoặc 0-12
UTC được phát triển dựa trên GMT, UTC sử dụng khi muốn thống nhất về thời gian giữa các khu vực địa lý khác nhau, ví dụ khi tôi nói chúng ta sẽ họp lúc 14:00:00 UTC, thì các đồng nghiệp ở việt nam sẽ tham gia vào lúc 21:00:00, và nhật bản sẽ tham gia vào lúc 19:00:00.

### Tìm múi giờ
Sử dụng hàm `ZoneId.systemDefault()` để in ra timezone

## 2. Reviewing the String class

