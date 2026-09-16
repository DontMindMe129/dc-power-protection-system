# Phần mềm nhúng (firmware)

Trạng thái: **chưa bắt đầu**.

Thư mục này sẽ trở thành một dự án firmware STM32 độc lập sau khi hoàn tất rà soát yêu cầu. Nền tảng dự kiến: STM32F103C8T6, sử dụng C, STM32 HAL, CMake, Ninja và `arm-none-eabi-gcc`.

Các mô-đun do nhóm phát triển dự kiến gồm đo lường, hiệu chuẩn, máy trạng thái bảo vệ, điều khiển công tắc tải, giao diện người dùng và chẩn đoán. Tệp do CubeMX sinh ra và mã do người dùng viết phải được phân tách rõ ràng.
