# Điều khiển Robot với PS2 và Mạch BANHMI

Dự án này cung cấp mã nguồn Arduino để điều khiển một nền tảng robot di động bằng **tay cầm PS2**, sử dụng bo mạch điều khiển **BANHMI của MakerViet** để quản lý động cơ và servo. Mạch BANHMI tích hợp **PCA9685** và các driver động cơ, giúp việc kết nối phần cứng trở nên gọn gàng và hiệu quả hơn.

---

## Tính năng Chính ✨

* **Điều khiển Di chuyển:** Sử dụng **joystick bên phải** (cần gạt điều hướng) của tay cầm PS2 để điều khiển hướng và tốc độ của robot (tiến, lùi, rẽ trái, rẽ phải).
* **Điều khiển Nâng/Thả:** Điều khiển hai động cơ nâng/thả độc lập bằng các nút **R1** và **R2** trên tay cầm.
* **Điều khiển Cổng Servo:** Sử dụng các nút **L1** và **L2** để điều khiển hai cặp servo (động cơ quay góc) hoạt động như cổng chắn vật, với trạng thái đóng/mở có thể chuyển đổi.
* **Kết nối PS2 Đáng tin cậy:** Mã code bao gồm logic để đảm bảo kết nối ổn định với tay cầm PS2, giảm thiểu lỗi kết nối.
* **Debug Serial (Gỡ lỗi qua Serial):** In ra các giá trị joystick và trạng thái cổng servo ra màn hình máy tính để bạn dễ dàng kiểm tra và khắc phục sự cố nếu có.

---

## Yêu cầu Phần cứng 🛠️

Để bắt đầu với dự án này, bạn sẽ cần các thành phần sau:

* **Mạch BANHMI (MakerViet):** Đây là **bảng mạch điều khiển chính** của chúng ta. Nó giống như bộ não của robot, tích hợp sẵn chip PCA9685 và các driver động cơ (mạch điều khiển động cơ), giúp việc kết nối dây điện trở nên gọn gàng và dễ dàng hơn rất nhiều  
* **Tay cầm PS2 (PlayStation 2 Controller):** Đây là thiết bị bạn sẽ dùng để điều khiển robot từ xa.   
* **Bộ thu PS2:** Một thiết bị nhỏ nhận tín hiệu không dây từ tay cầm PS2 và truyền nó đến mạch BANHMI
* **Động cơ DC:** Bốn động cơ này sẽ gắn vào bánh xe của robot (hai cho mỗi bên) để giúp robot di chuyển.
* **Động cơ DC (180 RPM):** Hai động cơ này được dùng cho cơ cấu nâng/thả, ví dụ như một cái càng nâng vật. **RPM (Revolutions Per Minute)** là số vòng quay mỗi phút, cho biết tốc độ của động cơ.
* **Servo Motors:** Bốn loại động cơ đặc biệt này có thể quay đến một góc xác định (ví dụ: 0 đến 180 độ). Chúng được dùng làm cổng chắn vật trong dự án này.
* **Nguồn điện:** Cung cấp năng lượng cho toàn bộ hệ thống (mạch BANHMI, động cơ và servo).
* **Dây nối:** Để kết nối các thành phần với nhau.

---

## Giải thích Thuật ngữ Quan trọng 🧐

Để hiểu rõ hơn về cách mạch BANHMI và code hoạt động, hãy cùng tìm hiểu một số thuật ngữ quan trọng:

### PCA9685 (PWM Driver)

* **PCA9685** là tên của một con chip điều khiển đặc biệt. Các mạch vi điều khiển (như Arduino hoặc ESP32 trên mạch BANHMI) thường có số lượng chân PWM (xem bên dưới) bị hạn chế. Chip PCA9685 giúp **"mở rộng"** số lượng chân PWM này, cho phép chúng ta điều khiển nhiều servo và động cơ hơn cùng một lúc mà không làm quá tải vi điều khiển chính.
* Nó hoạt động như một "phiên dịch viên" hoặc "bộ điều khiển trung tâm" nhỏ, nhận lệnh từ vi điều khiển chính (qua giao tiếp I2C) và sau đó tạo ra các tín hiệu PWM chính xác để điều khiển các thiết bị đầu ra (động cơ, servo).

### PWM (Pulse Width Modulation - Điều chế độ rộng xung)

* **PWM** là một kỹ thuật để điều khiển lượng năng lượng được cấp cho một thiết bị điện tử. Thay vì bật/tắt hoàn toàn nguồn điện, PWM sẽ bật và tắt nguồn điện rất nhanh.
* Hãy tưởng tượng bạn có một công tắc đèn:
    * Nếu bạn bật đèn liên tục (100% thời gian bật), đèn sẽ sáng hết cỡ.
    * Nếu bạn bật đèn 50% thời gian và tắt 50% thời gian, đèn sẽ sáng mờ hơn một nửa.
* Với động cơ DC, khi bạn thay đổi "độ rộng xung" (tức là thay đổi thời gian bật so với thời gian tắt), bạn sẽ điều khiển được **tốc độ quay** của động cơ.
* Với servo, PWM được dùng để điều khiển **góc quay** của chúng. Mỗi độ rộng xung nhất định sẽ tương ứng với một góc quay cụ thể của servo.

### I2C (Inter-Integrated Circuit - Giao tiếp giữa các Mạch tích hợp)

* **I2C** là một loại **"ngôn ngữ"** hoặc **"giao thức giao tiếp"** mà các con chip điện tử dùng để nói chuyện với nhau.
* Nó giống như việc bạn có hai người đang trò chuyện. Thay vì mỗi người cần một đường dây điện thoại riêng để nói chuyện với từng người khác, I2C chỉ cần **hai dây** chung để nhiều thiết bị có thể giao tiếp với nhau.
    * Một dây gọi là **SDA (Serial Data)**: dùng để gửi và nhận dữ liệu.
    * Một dây gọi là **SCL (Serial Clock)**: dùng để đồng bộ hóa tốc độ truyền dữ liệu giữa các thiết bị.
* Trong dự án này, vi điều khiển trên mạch BANHMI (ví dụ, ESP32) sử dụng I2C để gửi lệnh đến chip PCA9685, và PCA9685 sẽ thực hiện các lệnh đó để điều khiển động cơ và servo.

---

## Cấu hình Chân cắm trên Mạch BANHMI 📌

Mạch BANHMI được thiết kế để đơn giản hóa việc kết nối. Các chân (pins) cho PS2 và các kênh PWM cho động cơ/servo đã được định nghĩa và kết nối sẵn trên mạch.

**Sơ đồ kết nối tổng quan của hệ thống logic mạch Via B (Mạch BANHMI):**


**Chi tiết các cổng kết nối trên mạch BANHMI:**

Bạn có thể xem cách kết nối thông qua 2 ảnh sơ đồ mạch dưới hoặc hướng dẫn bằng văn bản bên dưới.
![Hình 1. Sơ đồ mạch](https://github.com/Codedr-edu/LIT---FBN---FARC-2025/assets/Ảnh chụp màn hình 2025-07-18 193456.png)
![Hình 2. Giải thích sơ đồ mạch](blob:https://www.facebook.com/32c5f902-4ec5-4d0f-9d82-4295b55fe8eb)

### Kết nối PS2 Controller

Mạch BANHMI thường có các chân chuyên dụng cho bộ thu PS2. Trong mã code của bạn, chúng được định nghĩa như sau:

| Tín hiệu PS2 Controller | Chân Arduino (trên mạch BANHMI) |
| :---------------------- | :------------------------------ |
| `PS2_DAT` (Data)        | 12                              |
| `PS2_CMD` (Command)     | 13                              |
| `PS2_SEL` (Select)      | 15                              |
| `PS2_CLK` (Clock)       | 14                              |

### Các kênh PWM của PCA9685 trên Mạch BANHMI

Mạch BANHMI sử dụng chip **PCA9685** để mở rộng số lượng chân PWM. Các kênh này sẽ được kết nối trực tiếp đến các cổng động cơ và servo trên mạch BANHMI.

**Kênh PWM cho Động cơ di chuyển và Nâng/Thả:**

| Chức năng Động cơ | Kênh PWM của PCA9685 |
| :---------------- | :------------------- |
| `LEFT_FORWARD` (Bánh trái tiến)    | 8                    |
| `LEFT_BACKWARD` (Bánh trái lùi)   | 9                    |
| `RIGHT_FORWARD` (Bánh phải tiến)   | 10                   |
| `RIGHT_BACKWARD` (Bánh phải lùi)  | 11                   |
| `LIFT_UP_1` (Nâng 1 lên)       | 12                   |
| `LIFT_DOWN_1` (Nâng 1 xuống)     | 13                   |
| `LIFT_UP_2` (Nâng 2 lên)       | 14                   |
| `LIFT_DOWN_2` (Nâng 2 xuống)     | 15                   |

**Kênh PWM cho Servo Cổng chắn vật:**

| Chức năng Servo  | Kênh PWM của PCA9685 |
| :---------------- | :------------------- |
| `SERVO_GATE` (Cổng 1)      | 2                    |
| `SERVO_GATE1` (Cổng 2)     | 3                    |
| `SERVO_GATE2` (Cổng 3)     | 6                    |
| `SERVO_GATE3` (Cổng 4)     | 7                    |

---

## Cài đặt Thư viện 📚

Bạn cần cài đặt các thư viện sau trong Arduino IDE (Môi trường phát triển tích hợp Arduino - nơi bạn viết và tải code lên mạch):

1.  **Adafruit PWM Servo Driver Library:** Thư viện này giúp Arduino giao tiếp và điều khiển chip PCA9685.
    * Đi tới `Sketch` (Phác thảo) > `Include Library` (Bao gồm Thư viện) > `Manage Libraries...` (Quản lý Thư viện...).
    * Tìm kiếm "`Adafruit PWM Servo Driver`" và cài đặt nó.
2.  **PS2X_lib by Bill Porter:** Thư viện này giúp Arduino đọc dữ liệu từ tay cầm PS2.
    * Đi tới `Sketch` (Phác thảo) > `Include Library` (Bao gồm Thư viện) > `Manage Libraries...` (Quản lý Thư viện...).
    * Tìm kiếm "`PS2X_lib`" và cài đặt nó.
3.  **Wire Library:** Thư viện này thường được cài đặt sẵn với Arduino IDE. Nó được sử dụng để giao tiếp I2C với PCA9685 tích hợp trên BANHMI.

---

## Hướng dẫn Sử dụng 🚀

1.  **Kết nối Phần cứng:** Cắm bộ thu PS2, các động cơ và servo vào các cổng tương ứng trên mạch **BANHMI** theo sơ đồ đã cho. Đảm bảo bạn đã cấp nguồn điện đầy đủ và đúng cách cho mạch.
2.  **Mở mã:** Mở tệp `.ino` (tệp mã nguồn Arduino) trong Arduino IDE.
3.  **Cài đặt Thư viện:** Đảm bảo bạn đã cài đặt tất cả các thư viện cần thiết như hướng dẫn ở trên.
4.  **Chọn Bo mạch và Cổng:**
    * Đi tới `Tools` (Công cụ) > `Board` (Bo mạch) và chọn loại vi điều khiển được sử dụng trên mạch BANHMI của bạn (ví dụ: ESP32 Dev Module nếu mạch BANHMI sử dụng ESP32, hoặc Arduino Uno nếu dùng Uno).
    * Đi tới `Tools` (Công cụ) > `Port` (Cổng) và chọn cổng COM mà mạch BANHMI của bạn đang kết nối.
5.  **Tải lên:** Nhấn nút `Upload` (Tải lên) trong Arduino IDE để nạp mã vào mạch BANHMI.
6.  **Điều khiển:** Sau khi mã được tải lên thành công và tay cầm PS2 đã kết nối, bạn có thể điều khiển robot:
    * **Di chuyển:** Sử dụng **joystick bên phải** (`PSS_RX` cho trục X - ngang, `PSS_LY` cho trục Y - dọc) để điều khiển tiến, lùi và rẽ.
    * **Nâng/Thả:**
        * Nhấn giữ **R2** để kích hoạt động cơ nâng/thả số 1 đi lên và động cơ số 2 đi xuống.
        * Nhấn giữ **R1** để kích hoạt động cơ nâng/thả số 1 đi xuống và động cơ số 2 đi lên.
    * **Cổng Chắn Vật:**
        * Nhấn **L1** để chuyển đổi trạng thái (mở/đóng) của cặp servo `SERVO_GATE` và `SERVO_GATE1`.
        * Nhấn **L2** để chuyển đổi trạng thái (mở/đóng) của cặp servo `SERVO_GATE2` và `SERVO_GATE3`.

![Hình 3. Sơ đồ tay cầm](blob:https://www.facebook.com/32f4ccd8-f76c-4017-a416-aa9a63ac51cf)

---

## Chi tiết Mã nguồn 🧑‍💻

### Hằng số và Khai báo (Constants and Declarations)

* Các hằng số `#define` giống như việc đặt tên ngắn gọn cho các số hoặc chân cắm. Chúng ta dùng chúng để định nghĩa các chân kết nối PS2 và các kênh PWM trên PCA9685 (tích hợp trên BANHMI) cho cả động cơ và servo.
* Đối tượng `Adafruit_PWMServoDriver pwm` là một "biến" đặc biệt giúp chúng ta ra lệnh cho chip PCA9685.
* Đối tượng `PS2X ps2x` là một "biến" giúp chúng ta đọc các tín hiệu từ tay cầm PS2.
* Các biến `isGateOpen`, `isGateOpen1` là các biến kiểu `boolean` (chỉ có giá trị `true` hoặc `false`) để theo dõi trạng thái đóng/mở của các cổng servo.

### `angleToPulse(int angle)`

Chức năng này là một hàm (một đoạn mã nhỏ có nhiệm vụ cụ thể) giúp chúng ta chuyển đổi một góc quay mong muốn (từ 0 đến 180 độ) thành một giá trị "độ rộng xung" (từ 0 đến 4095) mà servo có thể hiểu được. Các giá trị **120** và **500** là các giới hạn của độ rộng xung cho servo của bạn và có thể cần điều chỉnh (`map` là hàm chuyển đổi một giá trị từ một phạm vi này sang một phạm vi khác).

### `setup()`

Đây là phần mã chỉ chạy một lần khi mạch Arduino khởi động:

* `Serial.begin(115200);`: Khởi tạo giao tiếp Serial, cho phép bạn gửi thông báo từ Arduino về máy tính để gỡ lỗi. `115200` là tốc độ truyền dữ liệu.
* Thiết lập kết nối với tay cầm PS2. Mã sẽ thử kết nối nhiều lần; nếu không thành công, nó sẽ in thông báo lỗi và dừng lại.
* `pwm.begin();`: Khởi tạo chip PCA9685 tích hợp trên mạch BANHMI.
* `pwm.setOscillatorFrequency(27000000);`: Đặt tần số bộ dao động của PCA9685 là 27 MHz (megahertz).
* `pwm.setPWMFreq(50);`: Đặt tần số PWM (tần số điều khiển động cơ/servo) là 50 Hz (Hertz), đây là tần số tiêu chuẩn cho servo.
* `Wire.setClock(400000);`: Đặt tốc độ xung nhịp I2C (giao thức truyền thông) giữa Arduino và PCA9685 là 400kHz (kilohertz) để truyền dữ liệu nhanh hơn.
* Đặt vị trí ban đầu cho `SERVO_GATE2` và `SERVO_GATE3`.
* Đặt các biến trạng thái cổng servo thành `false` (đóng).
* Gọi hàm `stopAll()` để đảm bảo tất cả các động cơ dừng lại ngay khi robot khởi động.

### `stopAll()`

Chức năng này là một hàm đơn giản có nhiệm vụ **dừng tất cả các động cơ DC** và động cơ nâng/thả bằng cách đặt giá trị PWM của chúng về **0** (không quay).

### `loop()`

Đây là phần mã sẽ **chạy lặp đi lặp lại mãi mãi** sau khi `setup()` kết thúc:

* `ps2x.read_gamepad();`: Đọc trạng thái hiện tại của tất cả các nút và joystick trên tay cầm PS2.
* **Điều khiển Di chuyển:**
    * Đọc giá trị thô từ joystick bên phải (trục X `PSS_RX` và trục Y `PSS_LY`).
    * `deadzone = 10;`: Đây là một "vùng chết" nhỏ. Nếu joystick chỉ di chuyển một chút trong vùng này (ví dụ, do tay bạn hơi run), robot sẽ không di chuyển để tránh tình trạng trôi không mong muốn.
    * Các giá trị joystick được điều chỉnh và ánh xạ thành tốc độ PWM (từ 0 đến 2730) để điều khiển tốc độ quay của từng bánh xe (điều khiển vi sai).
    * Mã sẽ kiểm tra tốc độ (`leftSpeed`, `rightSpeed`) để quyết định động cơ nào cần quay tiến (`_FORWARD`) hay lùi (`_BACKWARD`).
* **Điều khiển Nâng/Thả:**
    * Kiểm tra các nút **R2** và **R1** trên tay cầm.
    * Nếu **R2** được nhấn, động cơ `LIFT_UP_1` sẽ quay lên và `LIFT_DOWN_2` sẽ quay xuống (với tốc độ PWM tối đa là 2730).
    * Nếu **R1** được nhấn, động cơ `LIFT_DOWN_1` sẽ quay xuống và `LIFT_UP_2` sẽ quay lên.
    * Nếu không có nút nào được nhấn, tất cả các động cơ nâng/thả sẽ dừng lại.
* **Điều khiển Servo Cổng Chắn Vật:**
    * Khi nút **L1** được nhấn (`ps2x.ButtonPressed(PSB_L1)`), trạng thái của `isGateOpen` sẽ được đảo ngược (từ `true` thành `false` hoặc ngược lại).
    * Nếu cổng đang đóng, nó sẽ mở bằng cách di chuyển `SERVO_GATE` và `SERVO_GATE1` đến các vị trí mở (1000 và 2000 micro giây tương ứng). Sau một khoảng `delay` (thời gian chờ) ngắn, chúng được đặt về vị trí trung tâm (1500 micro giây) để giải phóng lực căng.
    * Tương tự, khi nút **L2** được nhấn, trạng thái của `isGateOpen1` sẽ được chuyển đổi và điều khiển `SERVO_GATE2` và `SERVO_GATE3`.
* **Debug Serial (Gỡ lỗi qua Serial):** In các giá trị hiện tại của joystick (`LY`, `LX`), tốc độ PWM của bánh xe (`L_PWM`, `R_PWM`) và trạng thái của cổng (`GATE`) ra cửa sổ Serial Monitor trên Arduino IDE. Điều này cực kỳ hữu ích để bạn biết robot đang nhận lệnh như thế nào và hoạt động ra sao.
* `delay(30);`: Dừng chương trình 30 mili giây trước khi lặp lại vòng `loop()`. Điều này giúp ổn định quá trình điều khiển và tránh quá tải cho vi điều khiển.

---

## Cân nhắc và Cải tiến 💡

* **Hiệu chỉnh Servo:** Các giá trị PWM (ví dụ: `1000`, `1500`, `2000` micro giây) cho servo cần được **hiệu chỉnh chính xác** cho từng loại servo bạn sử dụng để đảm bảo chúng quay đến đúng góc và không bị rung.
* **Chế độ tay cầm PS2:** Đảm bảo tay cầm PS2 của bạn đang ở **chế độ Analog** (thường có đèn báo màu đỏ trên tay cầm sáng).
* **Quản lý Nguồn:** Mạch BANHMI đã có các driver động cơ tích hợp, nhưng bạn vẫn cần đảm bảo nguồn điện cấp cho mạch đủ mạnh để vận hành tất cả các động cơ và servo, đặc biệt khi chúng hoạt động đồng thời và dưới tải nặng.
* **Tối ưu độ trễ:** Độ trễ `30ms` trong `loop()` là hợp lý cho nhiều ứng dụng, nhưng bạn có thể thử điều chỉnh nó để xem có cải thiện được độ phản hồi của robot hay không.
* **Hạn chế tốc độ PWM:** Giá trị PWM tối đa `2730` được dùng để điều khiển tốc độ động cơ. Hãy đảm bảo rằng giá trị này an toàn cho động cơ của bạn và không gây quá tải cho mạch BANHMI.
* **Cải tiến điều khiển cổng:** Logic điều khiển servo hiện tại có một độ trễ và đặt lại về vị trí trung tâm. Tùy thuộc vào cơ cấu cổng của bạn, bạn có thể muốn tinh chỉnh phần này để có chuyển động mượt mà hơn hoặc giữ servo ở vị trí cuối cùng thay vì trung tâm.

Nếu bạn có bất kỳ câu hỏi nào khác hoặc cần giải thích chi tiết hơn về một phần cụ thể, đừng ngần ngại hỏi nhé!
