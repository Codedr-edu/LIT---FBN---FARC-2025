# LIT - FBN - FARC 2025


Repo này cung cấp code chính thức được team LIT sử dụng trong vòng chung kết cuộc thi FARC 2025 (FPTU AI & Robotics Challenges 2025).

Repo này cung cấp mã nguồn Arduino để điều khiển robot bằng **tay cầm PS2**, sử dụng bo mạch điều khiển **BANHMI của MakerViet** để quản lý động cơ và servo. Mạch BANHMI tích hợp **PCA9685** và các driver động cơ, giúp việc kết nối phần cứng trở nên gọn gàng và hiệu quả hơn.

---

## Tính năng Chính

* **Điều khiển Di chuyển:** Sử dụng **joystick bên phải** của tay cầm PS2 để điều khiển hướng và tốc độ của robot (tiến, lùi, rẽ trái, rẽ phải).
* **Điều khiển Nâng/Thả:** Điều khiển hai động cơ nâng/thả độc lập bằng các nút **R1** và **R2**.
* **Điều khiển Cổng Servo:** Sử dụng các nút **L1** và **L2** để điều khiển hai cặp servo hoạt động như cổng chắn vật, với trạng thái đóng/mở có thể chuyển đổi.
* **Kết nối PS2 Đáng tin cậy:** Bao gồm logic để đảm bảo kết nối ổn định với tay cầm PS2.
* **Debug Serial:** In ra các giá trị joystick và trạng thái cổng servo để dễ dàng gỡ lỗi.

---

## Yêu cầu Phần cứng

* **Mạch BANHMI (MakerViet):** Đây là bo mạch điều khiển chính, tích hợp PCA9685 và các driver động cơ cần thiết.
* **Tay cầm PS2:** Để điều khiển robot.
* **Bộ thu PS2:** Giao tiếp với tay cầm PS2.
* **Động cơ DC:** Bốn động cơ cho bánh xe (hai cho mỗi bên).
* **Động cơ DC (180 RPM):** Hai động cơ cho cơ cấu nâng/thả.
* **Servo Motors:** Bốn servo cho các cơ cấu cổng chắn vật.
* **Nguồn điện:** Thích hợp cho mạch BANHMI, động cơ và servo.
* **Dây nối.**

---

## Cấu hình Chân cắm trên Mạch BANHMI

Mạch BANHMI được thiết kế để đơn giản hóa việc kết nối. Các chân PS2 và các kênh PWM cho động cơ/servo đã được định nghĩa và kết nối sẵn trên mạch.

### Kết nối PS2 Controller

Mạch BANHMI có các chân chuyên dụng cho bộ thu PS2. Vui lòng tham khảo tài liệu của mạch BANHMI để xác định các chân này, tuy nhiên, trong repo này, chúng được định nghĩa như sau:

| PS2 Controller | Chân Arduino (trên mạch BANHMI) |
| :------------- | :------------------------------ |
| `PS2_DAT`      | 12                              |
| `PS2_CMD`      | 13                              |
| `PS2_SEL`      | 15                              |
| `PS2_CLK`      | 14                              |

### Các kênh PWM của PCA9685 trên Mạch BANHMI

Mạch BANHMI sử dụng chip PCA9685 để mở rộng chân PWM. Các kênh này sẽ được kết nối trực tiếp đến các cổng động cơ và servo trên mạch BANHMI.

| Chức năng Động cơ | Kênh PWM của PCA9685 |
| :---------------- | :------------------- |
| `LEFT_FORWARD`    | 8                    |
| `LEFT_BACKWARD`   | 9                    |
| `RIGHT_FORWARD`   | 10                   |
| `RIGHT_BACKWARD`  | 11                   |
| `LIFT_UP_1`       | 12                   |
| `LIFT_DOWN_1`     | 13                   |
| `LIFT_UP_2`       | 14                   |
| `LIFT_DOWN_2`     | 15                   |

| Chức năng Servo  | Kênh PWM của PCA9685 |
| :---------------- | :------------------- |
| `SERVO_GATE`      | 2                    |
| `SERVO_GATE1`     | 3                    |
| `SERVO_GATE2`     | 6                    |
| `SERVO_GATE3`     | 7                    |

---

## Cài đặt Thư viện

Bạn cần cài đặt các thư viện sau trong Arduino IDE (đi tới `Sketch` > `Include Library` > `Manage Libraries...`):

1.  **Adafruit PWM Servo Driver Library:** Tìm kiếm "`Adafruit PWM Servo Driver`" và cài đặt.
2.  **PS2X_lib by Bill Porter:** Tìm kiếm "`PS2X_lib`" và cài đặt.
3.  **Wire Library:** Thư viện này thường được cài đặt sẵn với Arduino IDE và cần thiết cho giao tiếp I2C với PCA9685 tích hợp trên BANHMI.

---

## Hướng dẫn Sử dụng

1.  **Kết nối Phần cứng:** Kết nối tay cầm PS2, các động cơ và servo vào các cổng tương ứng trên mạch **BANHMI**. Đảm bảo cấp nguồn đúng cách cho mạch.
2.  **Mở mã:** Mở tệp `.ino` trong Arduino IDE.
3.  **Cài đặt Thư viện:** Đảm bảo bạn đã cài đặt tất cả các thư viện cần thiết.
4.  **Chọn Bo mạch và Cổng:** Chọn bo mạch **Arduino/ESP32** (hoặc loại vi điều khiển được sử dụng trên BANHMI) và cổng nối tiếp (`Tools` > `Port`).
5.  **Tải lên:** Tải mã lên bo mạch BANHMI của bạn.
6.  **Điều khiển:** Sau khi tải lên thành công và kết nối PS2, bạn có thể điều khiển robot bằng tay cầm PS2:
    * **Di chuyển:** Sử dụng **joystick bên phải** (`PSS_RX`, `PSS_LY`) để điều khiển tiến, lùi và rẽ.
    * **Nâng/Thả:**
        * Nhấn giữ **R2** để kích hoạt `LIFT_UP_1` và `LIFT_DOWN_2`.
        * Nhấn giữ **R1** để kích hoạt `LIFT_DOWN_1` và `LIFT_UP_2`.
    * **Cổng Chắn Vật:**
        * Nhấn **L1** để chuyển đổi trạng thái của `SERVO_GATE` và `SERVO_GATE1` (mở/đóng).
        * Nhấn **L2** để chuyển đổi trạng thái của `SERVO_GATE2` và `SERVO_GATE3` (mở/đóng).

---

## Chi tiết Mã nguồn

### Hằng số và Khai báo

* Các hằng số `#define` định nghĩa các chân cho kết nối PS2 và các kênh PWM trên PCA9685 (tích hợp trên BANHMI) cho cả động cơ và servo.
* Đối tượng `Adafruit_PWMServoDriver pwm` được khởi tạo để giao tiếp với PCA9685.
* Đối tượng `PS2X ps2x` được khởi tạo để giao tiếp với tay cầm PS2.
* Các biến `isGateOpen`, `isGateOpen1` theo dõi trạng thái đóng/mở của các cổng servo.

### `angleToPulse(int angle)`

Chức năng tiện ích này chuyển đổi một góc (0-180 độ) thành giá trị độ rộng xung (0-4095) tương ứng cho servo. Các giá trị **120** và **500** là các giá trị tối thiểu và tối đa đã được hiệu chỉnh và có thể cần điều chỉnh tùy thuộc vào servo cụ thể của bạn.

### `setup()`

* Khởi tạo giao tiếp Serial với tốc độ baud `115200` để gỡ lỗi.
* Thiết lập kết nối PS2 với logic thử lại. Nếu không kết nối được, chương trình sẽ dừng lại.
* Khởi tạo chip PCA9685 (`pwm.begin()`) tích hợp trên mạch BANHMI.
* Thiết lập tần số bộ dao động là **27MHz** và tần số PWM là **50Hz** (tiêu chuẩn cho servo).
* Đặt tốc độ xung nhịp I2C thành **400kHz** để giao tiếp nhanh hơn.
* Đặt vị trí ban đầu cho `SERVO_GATE2` và `SERVO_GATE3`.
* Đặt các biến trạng thái cổng servo thành `false` (đóng).
* Gọi `stopAll()` để đảm bảo tất cả các động cơ dừng khi khởi động.

### `stopAll()`

Chức năng này đặt tất cả các đầu ra PWM của động cơ di chuyển và động cơ nâng/thả về **0**, đảm bảo chúng dừng hoạt động.

### `loop()`

* `ps2x.read_gamepad()` đọc trạng thái hiện tại của tay cầm PS2.
* **Điều khiển Di chuyển:**
    * Đọc giá trị thô từ joystick bên phải (`PSS_RX`, `PSS_LY`).
    * Điều chỉnh các giá trị và áp dụng một **deadzone** để ngăn chặn trôi nhẹ.
    * Tính toán tốc độ của bánh trái và phải bằng điều khiển vi sai.
    * Ánh xạ tốc độ thành giá trị PWM (0 đến 2730) và điều khiển riêng từng động cơ (`_FORWARD` hoặc `_BACKWARD`) thông qua các cổng động cơ trên mạch BANHMI.
* **Điều khiển Nâng/Thả:**
    * Kiểm tra các nút **R2** và **R1** để kích hoạt các cặp động cơ nâng/thả với tốc độ PWM tối đa (2730).
    * Nếu không có nút nào được nhấn, tất cả các động cơ nâng/thả đều dừng.
* **Điều khiển Servo Cổng Chắn Vật:**
    * Khi **L1** được nhấn, trạng thái của `SERVO_GATE` và `SERVO_GATE1` sẽ được chuyển đổi. Có một độ trễ ngắn và servo được đặt về vị trí trung tâm sau khi di chuyển.
    * Khi **L2** được nhấn, trạng thái của `SERVO_GATE2` và `SERVO_GATE3` sẽ được chuyển đổi.
* **Debug Serial:** In các giá trị joystick, tốc độ PWM của bánh xe và trạng thái cổng servo ra Serial Monitor.
* Một độ trễ `delay(30)`ms được thêm vào để ổn định chu kỳ điều khiển.

---

## Cân nhắc và Cải tiến

* **Hiệu chỉnh Servo:** Các giá trị PWM cho servo cần được hiệu chỉnh chính xác để đảm bảo chuyển động mượt mà và chính xác.
* **Chế độ tay cầm PS2:** Đảm bảo tay cầm PS2 của bạn ở chế độ **Analog** (đèn báo màu đỏ trên tay cầm).
* **Quản lý Nguồn:** Mạch BANHMI đã có các driver động cơ tích hợp, nhưng bạn vẫn cần đảm bảo nguồn điện cấp cho mạch đủ mạnh để vận hành tất cả các động cơ và servo dưới tải.
* **Độ trễ:** Độ trễ `30ms` trong `loop()` là hợp lý nhưng có thể được điều chỉnh để tối ưu hóa độ phản hồi và tải CPU.
* **Hạn chế tốc độ PWM:** Giá trị PWM tối đa `2730` cần phù hợp với động cơ của bạn để tránh quá tải.
* **Cải tiến điều khiển cổng:** Logic điều khiển servo hiện tại có thể cần được tinh chỉnh để có chuyển động mượt mà hơn và tránh bị rung lắc.

---
