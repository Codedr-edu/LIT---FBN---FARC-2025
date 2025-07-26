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
