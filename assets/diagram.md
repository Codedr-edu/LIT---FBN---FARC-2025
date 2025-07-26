Chắc chắn rồi! Dưới đây là bản README đã được cập nhật, với các lưu đồ thuật toán được đặt trong khối code mermaid để đảm bảo hiển thị chính xác trên GitHub và các trình đọc Markdown hỗ trợ.
Tóm tắt Dự án: Điều khiển Robot với PS2 và Mạch BANHMI
Dự án này tập trung vào việc điều khiển một nền tảng robot di động bằng tay cầm PS2, sử dụng mạch điều khiển BANHMI của MakerViet để quản lý động cơ và servo.
1. Hướng Cơ điện tử ⚙️
a. Tổng quan Hệ thống
Hệ thống robot được điều khiển từ xa thông qua tay cầm PS2, truyền tín hiệu đến mạch BANHMI. Mạch BANHMI đóng vai trò trung tâm xử lý, tích hợp chip PCA9685 và các driver động cơ, giúp đơn giản hóa việc kết nối phần cứng và điều khiển các thiết bị chấp hành (động cơ, servo).
b. Các Linh phụ kiện Chính
 * Mạch BANHMI (MakerViet): Bo mạch chủ tích hợp các tính năng điều khiển.
 * Tay cầm PS2 (PlayStation 2 Controller): Thiết bị điều khiển không dây.
 * Bộ thu PS2: Nhận tín hiệu từ tay cầm PS2.
 * Động cơ DC (4 cái): Dùng cho di chuyển (bánh xe).
 * Động cơ DC 180 RPM (2 cái): Dùng cho cơ cấu nâng/thả.
 * Servo Motors (4 cái): Dùng cho các cơ cấu cổng chắn vật.
 * Nguồn điện: Cung cấp năng lượng cho toàn bộ hệ thống.
 * Dây nối: Để kết nối các thành phần.
c. Cấu hình Tay cầm PS2
Để điều khiển, tay cầm PS2 cần được kết nối với bộ thu PS2, sau đó bộ thu được cắm vào các chân chuyên dụng trên mạch BANHMI. Các chân này được định nghĩa trong mã lập trình như sau:
| Tín hiệu PS2 Controller | Chân Arduino (trên mạch BANHMI) |
|---|---|
| PS2_DAT (Data) | 12 |
| PS2_CMD (Command) | 13 |
| PS2_SEL (Select) | 15 |
| PS2_CLK (Clock) | 14 |
d. Cấu hình Chân cắm trên Mạch BANHMI
Mạch BANHMI tích hợp chip PCA9685, cho phép điều khiển nhiều thiết bị PWM. Các động cơ và servo được kết nối với các kênh PWM cụ thể của PCA9685 trên mạch BANHMI:
Sơ đồ kết nối tổng quan của hệ thống logic mạch Via B (Mạch BANHMI):
Chi tiết các cổng kết nối trên mạch BANHMI:
Các kênh PWM cho Động cơ di chuyển và Nâng/Thả:
| Chức năng Động cơ | Kênh PWM của PCA9685 |
|---|---|
| LEFT_FORWARD (Bánh trái tiến) | 8 |
| LEFT_BACKWARD (Bánh trái lùi) | 9 |
| RIGHT_FORWARD (Bánh phải tiến) | 10 |
| RIGHT_BACKWARD (Bánh phải lùi) | 11 |
| LIFT_UP_1 (Nâng 1 lên) | 12 |
| LIFT_DOWN_1 (Nâng 1 xuống) | 13 |
| LIFT_UP_2 (Nâng 2 lên) | 14 |
| LIFT_DOWN_2 (Nâng 2 xuống) | 15 |
Các kênh PWM cho Servo Cổng chắn vật:
| Chức năng Servo | Kênh PWM của PCA9685 |
|---|---|
| SERVO_GATE (Cổng 1) | 2 |
| SERVO_GATE1 (Cổng 2) | 3 |
| SERVO_GATE2 (Cổng 3) | 6 |
| SERVO_GATE3 (Cổng 4) | 7 |
2. Hướng Lập trình 💻
a. Thư viện sử dụng
Dự án được triển khai trên môi trường Arduino IDE và yêu cầu các thư viện sau để biên dịch và chạy:
 * Adafruit PWM Servo Driver Library: Dùng để điều khiển chip PCA9685.
 * PS2X_lib by Bill Porter: Dùng để đọc dữ liệu từ tay cầm PS2.
 * Wire Library: Thư viện giao tiếp I2C, cần thiết cho việc kết nối với PCA9685.
b. Khai báo và Setup
Phần này định nghĩa các tài nguyên và thiết lập cấu hình ban đầu cho hệ thống:
 * Khai báo hằng số: Các #define được sử dụng để gán tên cho các chân PS2 và các kênh PWM cụ thể trên PCA9685, giúp mã dễ đọc và quản lý hơn.
 * Khai báo đối tượng: Khởi tạo đối tượng Adafruit_PWMServoDriver pwm để điều khiển PCA9685 và PS2X ps2x để đọc tín hiệu PS2.
 * Biến trạng thái: Các biến boolean (isGateOpen, isGateOpen1) theo dõi trạng thái đóng/mở của các cổng servo.
 * Hàm setup():
   * Khởi tạo giao tiếp Serial (Serial.begin(115200)) cho mục đích gỡ lỗi.
   * Thiết lập kết nối với tay cầm PS2, bao gồm logic thử lại nếu kết nối ban đầu không thành công.
   * Khởi tạo chip PCA9685 (pwm.begin()), đặt tần số bộ dao động là 27MHz và tần số PWM là 50Hz (chuẩn cho servo).
   * Đặt tốc độ xung nhịp I2C là 400kHz để tăng tốc độ giao tiếp.
   * Đặt vị trí ban đầu cho một số servo và đảm bảo tất cả các động cơ dừng thông qua hàm stopAll().
 * Hàm tiện ích angleToPulse(int angle): Chuyển đổi một giá trị góc (0-180 độ) thành giá trị độ rộng xung tương ứng cho servo.
c. Lập trình các nút điều khiển (loop())
Hàm loop() là nơi chứa logic điều khiển chính, chạy lặp đi lặp lại:
 * ps2x.read_gamepad();: Liên tục đọc trạng thái của tất cả các nút và joystick trên tay cầm PS2.
 * Điều khiển Di chuyển:
   * Đọc giá trị từ joystick bên phải (trục X, Y).
   * Áp dụng "deadzone" (vùng chết) để bỏ qua các chuyển động joystick nhỏ không mong muốn.
   * Tính toán và ánh xạ các giá trị joystick thành tốc độ PWM cho các động cơ bánh xe trái/phải (_FORWARD hoặc _BACKWARD) để điều khiển di chuyển vi sai (tiến, lùi, rẽ).
 * Điều khiển Nâng/Thả:
   * Kiểm tra trạng thái nút R1 và R2.
   * Nếu R1 hoặc R2 được nhấn, kích hoạt các cặp động cơ nâng/thả tương ứng với tốc độ PWM tối đa.
   * Nếu không nút nào được nhấn, tất cả động cơ nâng/thả sẽ dừng.
 * Điều khiển Servo Cổng Chắn Vật:
   * Khi nút L1 được nhấn, chuyển đổi trạng thái (mở/đóng) của cặp servo SERVO_GATE và SERVO_GATE1. Sau khi di chuyển, servo được đặt về vị trí trung tâm.
   * Tương tự, khi nút L2 được nhấn, chuyển đổi trạng thái của cặp servo SERVO_GATE2 và SERVO_GATE3.
 * Gỡ lỗi Serial: In các giá trị joystick và trạng thái hoạt động của robot ra Serial Monitor để theo dõi và gỡ lỗi.
 * delay(30);: Tạo độ trễ 30 mili giây ở cuối mỗi vòng lặp để ổn định hệ thống.
d. Hàm điều khiển chung
 * stopAll(): Một hàm riêng biệt để đặt tất cả các giá trị PWM của động cơ về 0, đảm bảo robot dừng hoàn toàn.
Lưu đồ thuật toán cho Code
Để trực quan hóa luồng logic của chương trình, dưới đây là lưu đồ thuật toán cho hàm setup() và loop():
1. Lưu đồ thuật toán cho setup()
graph TD
    A[Bắt đầu] --> B{Khởi tạo Serial (115200)};
    B --> C{Thiết lập kết nối PS2};
    C -- Lỗi --> D[Dừng chương trình (In lỗi)];
    C -- Thành công --> E{Khởi tạo PCA9685 (pwm.begin())};
    E --> F{Đặt tần số dao động PCA9685 = 27MHz};
    F --> G{Đặt tần số PWM = 50Hz};
    G --> H{Đặt tốc độ xung nhịp I2C = 400kHz};
    H --> I{Đặt vị trí ban đầu servo GATE2, GATE3};
    I --> J{Đặt trạng thái cổng servo = Đóng};
    J --> K{Gọi hàm stopAll()};
    K --> L[Kết thúc Setup];

2. Lưu đồ thuật toán cho loop()
graph TD
    A[Bắt đầu Loop] --> B{Đọc trạng thái tay cầm PS2 (ps2x.read_gamepad())};

    B --> C{Xử lý Điều khiển Di chuyển};
    C --> C1{Đọc giá trị joystick PSS_RX, PSS_LY};
    C1 --> C2{Áp dụng Deadzone};
    C2 --> C3{Tính toán tốc độ bánh trái/phải (leftSpeed, rightSpeed)};
    C3 --> C4{Chuyển đổi tốc độ thành giá trị PWM};
    C4 --> C5{Điều khiển động cơ bánh xe (FORWARD/BACKWARD) bằng PWM};

    B --> D{Xử lý Điều khiển Nâng/Thả};
    D --> D1{Kiểm tra nút R2};
    D1 -- R2 được nhấn --> D2{Kích hoạt LIFT_UP_1, LIFT_DOWN_2 (PWM tối đa)};
    D1 -- R2 không nhấn --> D3{Kiểm tra nút R1};
    D3 -- R1 được nhấn --> D4{Kích hoạt LIFT_DOWN_1, LIFT_UP_2 (PWM tối đa)};
    D3 -- R1 không nhấn --> D5{Gọi hàm stopAll() cho động cơ nâng/thả};
    D2 --> E; D4 --> E; D5 --> E;

    B --> E{Xử lý Điều khiển Servo Cổng Chắn Vật};
    E --> E1{Kiểm tra nút L1 được nhấn};
    E1 -- Đúng --> E2{Đảo ngược trạng thái isGateOpen};
    E2 --> E3{Đặt vị trí servo GATE, GATE1 theo isGateOpen};
    E3 --> E4{Delay 50ms, sau đó đặt servo về vị trí trung tâm};
    E4 --> E5;
    E1 -- Sai --> E5;

    E5 --> F{Kiểm tra nút L2 được nhấn};
    F -- Đúng --> F1{Đảo ngược trạng thái isGateOpen1};
    F1 --> F2{Đặt vị trí servo GATE2, GATE3 theo isGateOpen1};
    F2 --> G;
    F -- Sai --> G;

    G --> H{In thông tin Debug ra Serial Monitor};
    H --> I{Delay 30ms};
    I --> A;

