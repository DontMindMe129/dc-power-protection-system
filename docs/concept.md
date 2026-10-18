# Concept hệ thống giám sát và bảo vệ đường nguồn DC

> Trạng thái: concept được cập nhật theo các quyết định hiện tại. Tài liệu tóm tắt ý tưởng và phạm vi; yêu cầu chi tiết nằm trong [`product-requirements.md`](product-requirements.md), kiến trúc và hành vi nằm trong [`design-specification.md`](design-specification.md).

## 1. Mục tiêu

Dự án xây dựng một thiết bị đặt giữa nguồn DC và tải DC để:

- truyền năng lượng từ nguồn tới tải;
- đo trạng thái điện của đường công suất;
- cung cấp thông tin cho người dùng;
- ngắt tải khi phát hiện điều kiện không an toàn.

Thiết bị không điều chỉnh điện áp công suất cấp cho tải thành một mức cố định và không thay thế nguồn DC bên ngoài. Nguồn điện tử nội bộ vẫn tạo các rail 5 V và 3.3 V để nuôi các khối điều khiển, đo lường và giao diện.

## 2. Ranh giới hệ thống

Nguồn và tải đều nằm ngoài phạm vi thiết kế. Thiết bị chỉ làm việc với điện áp DC thấp; việc chuyển đổi từ điện lưới AC sang DC thuộc về adapter bên ngoài.

```text
Nguồn DC ngoài ──> [ hệ thống giám sát và bảo vệ ] ──> Tải DC ngoài
```

Sản phẩm cuối phải được triển khai trên PCB và kiểm thử thực tế. Breadboard hoặc module chỉ dùng để thử từng khối trong quá trình phát triển.

## 3. Đầu vào và đầu ra

Hệ thống có hai loại luồng:

- **Công suất:** nhận năng lượng từ nguồn DC và truyền tới tải qua đường công suất có khả năng đóng/ngắt.
- **Thông tin:** nhận lệnh và cấu hình vận hành qua Buttons; trả về giá trị đo, trạng thái và cảnh báo qua Display. Loại màn hình, số nút và giao tiếp cụ thể còn TBD.

Tải vật lý không phải một phần của hệ thống. Cấu hình tải chỉ mô tả miền hành vi được phép để hệ thống lựa chọn cách giám sát và bảo vệ phù hợp.

## 4. Đại lượng cần đo

- `V_in`: điện áp tại đầu nối nguồn vào của PCB, trước Input Protection.
- `V_load`: điện áp tại đầu nối tải ra của PCB, sau Power Switch.
- `I_load`: dòng đi trên nhánh tải, không bao gồm dòng tự tiêu thụ của thiết bị.
- `V_drop = V_in − V_load`: độ sụt áp toàn đường công suất giữa hai đầu nối PCB khi công tắc công suất đang bật và đã xét quá độ hợp lệ, bao gồm Input Protection nhưng không bao gồm dây bên ngoài.

Đầu vào đo `V_in` có bảo vệ phù hợp trong khối Voltage Sensing vì lấy mẫu trước Input Protection. Điện áp sau Input Protection được gọi là `V_protected`, không đồng nhất với `V_in` và chưa yêu cầu thêm kênh đo riêng.

Không thể kết luận lỗi chỉ từ một đại lượng. Ví dụ, `V_load` thấp có thể do quá tải, nguồn bị sụt áp hoặc công tắc đã tắt. Hệ thống phải xét đồng thời các giá trị đo, trạng thái công tắc và cấu hình đang dùng.

## 5. Nguyên lý hoạt động

Thiết bị truyền thẳng điện áp thay vì điều chỉnh điện áp đầu ra:

```text
Công tắc bật:  V_load ≈ V_in − V_drop
Công tắc tắt:  thiết bị không chủ động truyền năng lượng tới tải
```

Tải phải tương thích với nguồn đang kết nối. Cấu hình vận hành không biến đổi điện áp; nó chỉ xác định miền bình thường, miền cảnh báo và điều kiện ngắt.

Khi cấp nguồn/reset, hệ thống nạp cấu hình mặc định và giữ đầu ra OFF. Chỉ bật tải khi đạt điều kiện READY và có lệnh ON của người dùng. Cho phép áp dụng thay đổi cấu hình trong READY hoặc FAULT; bộ đề nghị không hợp lệ bị từ chối và không thay cấu hình đang dùng. Phiên bản hiện tại không yêu cầu nhớ cấu hình người dùng qua reset/mất nguồn.

Khối điều khiển, đo lường và giao diện lấy nguồn từ `V_protected` qua Internal Control Power Supply, đặt trước công tắc tải. Vì vậy, việc ngắt tải không chủ động cắt nguồn điều khiển; hệ thống tiếp tục báo trạng thái khi nguồn nội bộ còn hoạt động. Điểm lấy mẫu `V_in` trước protection không phải điểm lấy nguồn nuôi MCU.

Ngân sách nguồn phải dành phần tự tiêu thụ của thiết bị trước khi phân bổ dòng cho tải; mức dự phòng cụ thể được xác định khi thiết kế phần cứng.

## 6. Nguyên tắc bảo vệ

- Firmware giám sát các giá trị đo, xử lý điều kiện có thời gian và cung cấp thông tin cho người dùng.
- Sự cố quá dòng có khả năng gây hư hỏng phải có đường bảo vệ phần cứng, không phụ thuộc hoàn toàn vào firmware.
- Khi MCU mất nguồn, reset hoặc chưa điều khiển hợp lệ, công tắc tải phải mặc định ở trạng thái OFF.
- Trong phiên vận hành, lỗi đã được giữ cần yêu cầu phục hồi thủ công; phục hồi thành công vẫn OFF và cần lệnh ON riêng. Đổi cấu hình trong FAULT không tự xóa lỗi hoặc bật tải.
- Sau reset, không khôi phục fault phần mềm của phiên trước; kiểm tra lại điều kiện hiện tại. Nếu HW_OCP còn giữ lỗi thì phải tiếp tục FAULT và phục hồi thủ công. Không yêu cầu lưu lỗi bền vững qua mất nguồn hoàn toàn.
- Hệ thống không giả định nguồn bên ngoài sẽ luôn tự bảo vệ khi quá tải hoặc ngắn mạch.

## 7. Phạm vi ban đầu

| Nội dung | Baseline hiện tại | Trạng thái |
| --- | --- | --- |
| Điện áp nguồn ngoài tại đầu nối vào (`V_in`) | `5–15 V DC` | Đã xác nhận |
| Dòng tải liên tục | đến `1 A` | Tạm thời |
| Dòng tải ngắn hạn | đến `2 A` trong tối đa `1 s` | Tạm thời |
| Sản phẩm cuối | PCB lắp ráp và kiểm thử được | Đã xác nhận |

Ba profile tải ban đầu là `RESISTIVE`, `ELECTRONIC` và `FAN`, với tải đại diện lần lượt là tải thuần trở, tải điện tử có tụ đầu vào và quạt DC nhỏ. Điện trở công suất hoặc thiết bị tải điện tử được dùng để tạo điều kiện kiểm thử có kiểm soát; quạt giúp quan sát dòng khởi động và biến động dòng trong vận hành. Profile `ELECTRONIC` mô tả loại tải cần bảo vệ, không đồng nghĩa phải dùng thiết bị tải điện tử làm tải vận hành.

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

- giá trị cuối cùng của các baseline hiệu năng, gồm giới hạn dòng, thời gian dòng đỉnh và thời gian phản ứng bảo vệ nhanh;
- ngưỡng, dung sai, timing, hysteresis và cách hiện thực các khối bảo vệ;
- tiêu chí kiểm tra khởi động/phục hồi định lượng cho từng loại lỗi;
- tham số cụ thể của ba profile, bộ cấu hình mặc định và khả năng bổ sung `CUSTOM`;
- loại màn hình, số nút, giao tiếp và cách thao tác/nhập cấu hình chi tiết; loại đầu nối nguồn và tải;
- mức dự phòng dòng tự tiêu thụ và phân phối rail nguồn nội bộ;
- giới hạn nhiệt, điện áp tuyệt đối và thiết bị kiểm thử sẵn có.

Các điểm này sẽ được giải quyết dần trong đặc tả thành phần, tính toán, lựa chọn linh kiện và thử nghiệm. Chúng không mở lại các quyết định chức năng đã chốt; xem Product Requirements và Design Specification để phân biệt baseline tạm thời với hành vi đã xác nhận.
