# Concept hệ thống giám sát và bảo vệ nguồn DC

> Trạng thái: bản nháp đang được làm rõ. Chỉ những mục ghi **Đã xác nhận** mới được xem là quyết định hiện tại.

## 1. Vấn đề

Một tải DC khi nối trực tiếp với nguồn bên ngoài không cung cấp đủ thông tin về điện áp thực tế tại hai phía, dòng điện đang chạy hoặc nguyên nhân khiến tải hoạt động bất thường. Dự án bổ sung một thiết bị trung gian để quan sát đường công suất và ngắt tải khi phát hiện điều kiện không an toàn.

## 2. Ranh giới hệ thống — Đã xác nhận

Nguồn và tải đều nằm ngoài phạm vi thiết kế. Chỉ điện áp DC thấp đi vào thiết bị; việc biến đổi điện lưới 220 V AC thành DC thuộc về adapter bên ngoài.

```text
Nguồn DC ngoài
      │
      ▼
┌──────────────────────────────────────┐
│ Thiết bị giám sát và bảo vệ nguồn DC │
└──────────────────────────────────────┘
      │
      ▼
Tải DC ngoài
```

Sản phẩm cuối của dự án phải được triển khai trên PCB, được lắp ráp và kiểm thử thực tế. Breadboard hoặc module chỉ được dùng để thử nghiệm từng khối trước khi chốt PCB.

## 3. Mô hình đường công suất — Đã xác nhận

Thiết bị hoạt động theo kiểu truyền thẳng, không điều chỉnh điện áp đầu ra:

```text
Switch bật:  Vload ≈ Vin − Vdrop
Switch tắt:  tải bị tách khỏi nguồn
```

Vì vậy tải phải tương thích với nguồn đang kết nối. Thay đổi cấu hình không biến đổi điện áp; cấu hình chỉ xác định miền hoạt động và cách xử lý lỗi.

## 4. Đại lượng giám sát — Đã xác nhận

Thiết bị cần đo:

- `Vin`: điện áp ở phía đầu vào thiết bị;
- `Vload`: điện áp tại phía tải;
- `Iload`: dòng điện chạy trên đường công suất;
- `Vdrop = Vin − Vload`: độ sụt áp qua đường công suất khi switch đang bật.

`Vload` thấp không tự nó chứng minh rằng tải bị ngắn mạch, vì hiện tượng này cũng xảy ra khi switch đã tắt. Việc phân loại sự kiện phải xét đồng thời các phép đo và trạng thái của switch.

## 5. Nguồn đầu vào — Đã xác nhận một phần

Thiết bị được định hướng để hoạt động với nhiều mức `Vin` và khả năng cấp dòng khác nhau, miễn là chúng nằm trong giới hạn của phần cứng. Giá trị cụ thể của các giới hạn này chưa được chọn.

Cần phân biệt:

- **giới hạn tuyệt đối của phần cứng:** không được cấu hình vượt qua;
- **giới hạn theo nguồn/tải:** dải điện áp hợp lệ, dòng cảnh báo, dòng trip và thời gian xác nhận của từng cấu hình.

Thiết bị không được giả định rằng nguồn bên ngoài sẽ luôn tự bảo vệ khi ngắn mạch. Khả năng cấp dòng tối đa của nguồn, `I_supply_max`, là thông tin cấu hình; không thể suy ra đáng tin cậy chỉ từ `Vin` và `Iload`.

USB Type-C đã được cân nhắc nhưng chưa được chọn. Nếu sử dụng Type-C để nhận điện áp cao hơn mức USB mặc định, thiết kế phải có cơ chế thương lượng nguồn phù hợp. Jack DC hoặc terminal là các phương án đơn giản hơn cho nguyên mẫu.

## 6. Tải mục tiêu của V1 — Đã xác nhận

V1 hướng tới tải DC có tính chất chủ yếu là điện trở hoặc tải điện tử, hoạt động trong miền điện áp và dòng điện được PCB hỗ trợ. Việc kiểm thử sử dụng điện trở công suất hoặc tải điện tử để tạo các điểm làm việc có kiểm soát và lặp lại được.

V1 chưa hỗ trợ tải cảm, động cơ, solenoid, sạc pin hoặc tải có dòng khởi động lớn. Các loại tải này cần những cơ chế xử lý riêng và có thể được xem xét trong phiên bản sau.

## 7. Các tình trạng cần quan sát — Sơ bộ

Các nhóm sự kiện đang được xem xét gồm:

- `Vin` nằm ngoài miền hợp lệ của cấu hình;
- `Vdrop` quá lớn khi switch đang bật;
- `Iload` vượt ngưỡng trong một khoảng thời gian;
- dòng tăng rất nhanh do quá tải nặng hoặc ngắn mạch;
- nguồn bên ngoài sụt áp, giới hạn dòng, tự ngắt hoặc khởi động lại theo chu kỳ;
- MCU mất nguồn hoặc reset trong khi đang xảy ra lỗi.

Bảo vệ trước dòng có khả năng phá hủy phải có đường tác động phần cứng và không được phụ thuộc hoàn toàn vào firmware.

## 8. Cấu hình — Đã xác nhận ở mức khái niệm

Một cấu hình nguồn/tải có thể chứa:

```text
Vin_expected_min
Vin_expected_max
I_warning
I_trip
Thời gian xác nhận quá dòng
```

Mọi cấu hình phải nằm trong giới hạn tuyệt đối của PCB. Ngưỡng dòng cho phép phải không lớn hơn giới hạn an toàn của nguồn, tải và bản thân bo mạch.

## 9. Những điều chưa xác định

Các câu hỏi sau phải được giải quyết trước khi chuyển sang yêu cầu chi tiết:

1. Miền điện áp đầu vào và dòng liên tục của PCB là bao nhiêu?
2. Đầu nối nguồn và tải sử dụng loại nào?
3. Những lỗi nào chỉ cảnh báo và những lỗi nào phải ngắt tải?
4. Sau khi trip, tải tự phục hồi hay yêu cầu reset thủ công?
5. Trạng thái mặc định của tải khi vừa cấp nguồn hoặc khi MCU reset là gì?
6. Người dùng cần xem và cấu hình những thông tin nào?
7. Môn học quy định gì về kích thước PCB, số lớp, linh kiện và thiết bị kiểm thử?

## 10. Chưa thuộc phạm vi đã phê duyệt

Các hạng mục sau chưa được đưa vào phiên bản đầu tiên:

- USB Power Delivery/PPS tích hợp trên PCB;
- tự động nhận diện khả năng cấp dòng tối đa của nguồn;
- tải cảm, động cơ, solenoid và tải có dòng khởi động lớn;
- Bluetooth, Wi-Fi hoặc ứng dụng điện thoại;
- sạc pin;
- làm việc trực tiếp với điện lưới;
- tuyên bố hoạt động với mọi nguồn và mọi loại tải DC.
