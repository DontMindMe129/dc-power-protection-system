# Concept hệ thống giám sát và bảo vệ đường nguồn DC

> Trạng thái: bản tóm tắt Giai đoạn 1. Tài liệu này chỉ mô tả hệ thống ở mức ý tưởng; các yêu cầu chi tiết nằm trong [`product-requirements.md`](product-requirements.md).

## 1. Mục tiêu

Dự án xây dựng một thiết bị đặt giữa nguồn DC và tải DC để:

- truyền năng lượng từ nguồn tới tải;
- đo trạng thái điện của đường công suất;
- cung cấp thông tin cho người dùng;
- ngắt tải khi phát hiện điều kiện không an toàn.

Thiết bị không tạo ra một mức điện áp mới và không thay thế chức năng của bộ nguồn.

## 2. Ranh giới hệ thống

Nguồn và tải đều nằm ngoài phạm vi thiết kế. Thiết bị chỉ làm việc với điện áp DC thấp; việc chuyển đổi từ điện lưới AC sang DC thuộc về adapter bên ngoài.

```text
Nguồn DC ngoài ──> [ hệ thống giám sát và bảo vệ ] ──> Tải DC ngoài
```

Sản phẩm cuối phải được triển khai trên PCB và kiểm thử thực tế. Breadboard hoặc module chỉ dùng để thử từng khối trong quá trình phát triển.

## 3. Đầu vào và đầu ra

Hệ thống có hai loại luồng:

- **Công suất:** nhận năng lượng từ nguồn DC và truyền tới tải qua đường công suất có khả năng đóng/ngắt.
- **Thông tin:** nhận lệnh và cấu hình vận hành; trả về giá trị đo, trạng thái và cảnh báo.

Tải vật lý không phải một phần của hệ thống. Cấu hình tải chỉ mô tả miền hành vi được phép để hệ thống lựa chọn cách giám sát và bảo vệ phù hợp.

## 4. Đại lượng cần đo

- `Vin`: điện áp tại đầu vào của thiết bị.
- `Vload`: điện áp tại đầu ra, phía tải.
- `Iload`: dòng đi trên đường công suất từ nguồn tới tải, không bao gồm dòng tự tiêu thụ của thiết bị.
- `Vdrop = Vin − Vload`: độ sụt áp qua thiết bị khi công tắc công suất đang bật.

Không thể kết luận lỗi chỉ từ một đại lượng. Ví dụ, `Vload` thấp có thể do quá tải, nguồn bị sụt áp hoặc công tắc đã tắt. Hệ thống phải xét đồng thời các giá trị đo, trạng thái công tắc và cấu hình đang dùng.

## 5. Nguyên lý hoạt động

Thiết bị truyền thẳng điện áp thay vì điều chỉnh điện áp đầu ra:

```text
Công tắc bật:  Vload ≈ Vin − Vdrop
Công tắc tắt:  thiết bị không chủ động truyền năng lượng tới tải
```

Tải phải tương thích với nguồn đang kết nối. Cấu hình vận hành không biến đổi điện áp; nó chỉ xác định miền bình thường, miền cảnh báo và điều kiện ngắt.

Khối điều khiển, đo lường và giao diện lấy nguồn từ `Vin` qua một nhánh được bảo vệ và điều chỉnh riêng, đặt trước công tắc tải. Vì vậy, hệ thống có thể tiếp tục báo trạng thái sau khi đã ngắt tải nếu `Vin` vẫn còn hợp lệ.

## 6. Nguyên tắc bảo vệ

- Firmware giám sát các giá trị đo, xử lý điều kiện có thời gian và cung cấp thông tin cho người dùng.
- Sự cố quá dòng có khả năng gây hư hỏng phải có đường bảo vệ phần cứng, không phụ thuộc hoàn toàn vào firmware.
- Khi MCU mất nguồn, reset hoặc chưa điều khiển hợp lệ, công tắc tải phải mặc định ở trạng thái OFF.
- Hệ thống không giả định nguồn bên ngoài sẽ luôn tự bảo vệ khi quá tải hoặc ngắn mạch.

## 7. Phạm vi ban đầu

| Nội dung | Baseline hiện tại | Trạng thái |
| --- | --- | --- |
| Điện áp hoạt động | `5–15 V DC` | Đã xác nhận |
| Dòng tải liên tục | đến `1 A` | Tạm thời |
| Dòng tải ngắn hạn | đến `2 A` trong tối đa `1 s` | Tạm thời |
| Sản phẩm cuối | PCB lắp ráp và kiểm thử được | Đã xác nhận |

Các tải đại diện ban đầu gồm tải thuần trở, tải điện tử có tụ đầu vào và quạt DC nhỏ. Điện trở công suất hoặc tải điện tử được dùng để tạo điều kiện kiểm thử có kiểm soát; quạt giúp quan sát dòng khởi động và biến động dòng trong vận hành.

Động cơ DC hoặc servo chỉ được xem là phép thử mở rộng nếu nằm trong giới hạn của phần cứng. Phiên bản đầu không tự nhận dạng loại tải và không cam kết hỗ trợ mọi tải DC.

## 8. Ngoài phạm vi ban đầu

- làm việc trực tiếp với điện lưới AC;
- tích hợp USB Power Delivery/PPS trên PCB;
- tự nhận dạng nguồn hoặc loại tải;
- sạc pin;
- nguồn điều khiển dự phòng;
- kết nối Bluetooth, Wi-Fi hoặc ứng dụng điện thoại;
- cam kết hỗ trợ tải cảm mạnh hoặc tải có khả năng trả năng lượng lớn về nguồn.

## 9. Những điểm còn mở

- giá trị cuối cùng của giới hạn dòng và thời gian chịu dòng ngắn hạn;
- ngưỡng và kiến trúc của bảo vệ quá dòng nhanh;
- điều kiện cảnh báo, ngắt và phục hồi sau lỗi;
- cấu hình tải bắt buộc trong phiên bản đầu;
- giao diện người dùng và loại đầu nối;
- giới hạn nhiệt, điện áp tuyệt đối và thiết bị kiểm thử sẵn có.

Các điểm này sẽ được giải quyết dần bằng tính toán, lựa chọn kiến trúc và thử nghiệm; chúng chưa được xem là thiết kế đã phê duyệt.
