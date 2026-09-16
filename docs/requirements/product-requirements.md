# Yêu cầu sản phẩm

## 1. Mục đích

Sản phẩm là một trạm giám sát và bảo vệ DC điện áp thấp, được đặt giữa nguồn bên ngoài và tải bên ngoài. Sản phẩm cho phép quan sát trạng thái điện của đường công suất và ngắt tải khi xác nhận có điều kiện điện áp hoặc dòng điện bất thường.

Tài liệu này mô tả nhu cầu của người dùng và sản phẩm. Các phát biểu chi tiết, có thể đo được, được duy trì trong `system-requirements.md`.

## 2. Mô tả vấn đề

Kết nối đơn giản giữa nguồn DC và tải cung cấp cho người dùng rất ít thông tin về điện áp đầu vào, điện áp tải, dòng tải hoặc nguyên nhân khiến tải ngừng hoạt động. Kết nối đó cũng có thể vẫn duy trì tải trong một điều kiện bất thường. Sản phẩm đề xuất bổ sung khả năng đo lường, chỉ báo và ngắt có kiểm soát mà không bao gồm việc thiết kế nguồn hoặc tải bên ngoài.

## 3. Các bên liên quan

| Bên liên quan | Nhu cầu |
| --- | --- |
| Người dùng/người vận hành | Xem các giá trị điện hiện tại và trạng thái bảo vệ |
| Người dùng/người vận hành | Nhận cảnh báo trước hoặc khi xảy ra điều kiện bất thường |
| Người dùng/người vận hành | Reset trạm một cách an toàn sau khi nguyên nhân trip đã được loại bỏ |
| Nhóm phát triển | Thể hiện khối lượng công việc cân bằng giữa phần cứng, firmware, tích hợp và kiểm thử |
| Giảng viên đánh giá | Truy vết yêu cầu đến bằng chứng thiết kế và kết quả kiểm thử |

## 4. Ranh giới hệ thống

### Bên trong dự án

- Đầu nối đầu vào và đầu ra DC.
- Bảo vệ đầu vào cần thiết cho miền hoạt động đã chọn.
- Cảm biến điện áp và dòng điện.
- Công tắc tải có thể điều khiển.
- MCU, nguồn cục bộ và giao diện lập trình/gỡ lỗi.
- Màn hình cục bộ, nút nhấn và chỉ báo âm thanh/hình ảnh.
- Firmware cho thu thập dữ liệu, lọc, quản lý trạng thái bảo vệ, giao diện người dùng và chẩn đoán.
- Đường công suất trên PCB từ `DC IN` đến `DC OUT`.

### Bên ngoài dự án

- Nguồn DC bên ngoài.
- Tải bên ngoài.
- Cáp/đường dẫn vật lý sau `DC OUT`, trừ khi có cáp kiểm thử chuyên dụng được lập tài liệu.
- Thiết kế bộ sạc pin, nguồn bàn thử hoặc bản thân tải.
- Đóng cắt điện áp lưới.

## 5. Môi trường sử dụng tham chiếu

| Tham số | Giá trị tham chiếu | Trạng thái |
| --- | --- | --- |
| Nguồn danh định | 12 V DC | Được chọn để đánh giá tính khả thi |
| Dải đánh giá | 9-15 V DC | Được chọn để đánh giá tính khả thi |
| Dòng tải liên tục | Tối đa 1,0 A | Được chọn để đánh giá tính khả thi |
| Sử dụng trong phòng thí nghiệm trong nhà | Môi trường khô ráo, có giám sát | Giả định |
| Đặc tính nguồn đầu vào | Nguồn DC ổn áp hoặc nguồn thử tương đương | Giả định |

Dải 9-15 V là dải mà trong đó trạm phải duy trì trạng thái có kiểm soát và đo lường/bảo vệ đường công suất. Đây không phải cửa sổ điện áp "bình thường" mặc định. Các ngưỡng UVP/OVP mặc định hẹp hơn và vẫn là giá trị tạm thời.

## 6. Nhu cầu cấp sản phẩm

| Mã | Nhu cầu sản phẩm |
| --- | --- |
| PR-01 | Sản phẩm phải cho người dùng biết tải đang được kết nối, đang có cảnh báo hay đã trip. |
| PR-02 | Sản phẩm phải đo và hiển thị `Vin`, `Vload` và `Iload`. |
| PR-03 | Sản phẩm phải phát hiện các điều kiện thấp áp, quá áp và quá dòng kéo dài. |
| PR-04 | Sản phẩm phải ngắt tải bên ngoài khi xác nhận điều kiện trip. |
| PR-05 | Trạng thái trip bảo vệ phải được giữ chốt cho đến khi lỗi được loại bỏ và người dùng yêu cầu reset. |
| PR-06 | Năng lượng ngắn mạch phải được giới hạn bằng phần cứng chuyên dụng thay vì chỉ dựa vào phần mềm MCU. |
| PR-07 | Sản phẩm phải cung cấp đủ thông tin chẩn đoán để xác định nguyên nhân trip. |
| PR-08 | Sản phẩm phải có khả năng kiểm thử mà không cố ý khiến MCU hoặc người vận hành tiếp xúc với năng lượng lỗi không được kiểm soát. |
| PR-09 | Các yêu cầu, quyết định thiết kế và kết quả phải có khả năng truy vết trong kho lưu trữ. |

## 7. Phạm vi V1

### Bao gồm

- Đo điện áp đầu vào/tải.
- Đo dòng tải.
- Bảo vệ thấp áp (UVP).
- Bảo vệ quá áp (OVP).
- Bảo vệ quá dòng (OCP).
- Hành vi cảnh báo, trip, giữ chốt trạng thái và reset thủ công.
- Chỉ báo trạng thái trên OLED và đầu ra chẩn đoán UART.
- Bảo vệ nhanh bằng phần cứng đối với các lỗi dòng điện có khả năng phá hủy.
- Hiệu chuẩn và đặc trưng hóa sai số đo cơ bản.

### Hoãn lại

- Bảo vệ quá nhiệt.
- Đo năng lượng với độ chính xác cấp tính cước.
- Kết nối không dây/IoT.
- Tự động kết nối lại sau khi trip.
- Hoạt động với điện lưới AC.
- Tương thích với mọi loại nguồn và tải.

## 8. Các cổng chấp nhận sản phẩm

Nguyên mẫu V1 chỉ được chấp nhận khi:

1. Các yêu cầu đo điện áp và dòng điện tĩnh đạt yêu cầu.
2. Hành vi UVP, OVP và OCP đạt yêu cầu về ngưỡng, chống dội và giữ chốt/reset.
3. Phép thử đường công suất 1 A hoàn tất mà không gây nóng mất an toàn hoặc mất kiểm soát.
4. Một thiết lập phòng thí nghiệm tạm thời kiểm chứng được thời gian đáp ứng trip quan sát được từ bên ngoài.
5. Không cần phép thử ngắn mạch trực tiếp không được kiểm soát để chứng minh chức năng firmware cơ bản.
6. Mọi yêu cầu không đạt đều được lập tài liệu kèm bằng chứng và hướng xử lý.

## 9. Các quyết định sản phẩm còn mở

- Cấu trúc và linh kiện chính xác của công tắc tải.
- Cấu trúc cảm biến dòng và giá trị điện trở shunt.
- Chiến lược bảo vệ ngược cực và xung quá áp đầu vào.
- Hình dạng/kích thước PCB và họ đầu nối chính xác.
- Có bổ sung cảm biến nhiệt độ/OTP sau khi đánh giá tính khả thi V1 hay không.
- Hạn chót môn học, vai trò trong nhóm và định dạng nộp bài cuối cùng.
