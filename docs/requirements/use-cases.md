# Các ca sử dụng

## UC-01 - Giám sát bình thường

**Tác nhân:** Người vận hành

**Điều kiện tiên quyết:** Nguồn và tải hợp lệ đã được kết nối; không có trip nào đang được giữ chốt.

**Luồng chính:**

1. Trạm khởi tạo với đầu ra tải bị vô hiệu hóa.
2. Các phép đo ban đầu được kiểm tra.
3. Trạm kích hoạt đầu ra tải.
4. Trạm đo định kỳ `Vin`, `Vload` và `Iload`.
5. Giao diện người dùng hiển thị các phép đo và trạng thái `NORMAL`.

**Điều kiện thành công:** Tải tiếp tục được cấp nguồn và các phép đo nằm trong giới hạn sai số quy định.

## UC-02 - Thấp áp kéo dài

**Điều kiện kích hoạt:** `Vin` duy trì dưới ngưỡng UVP trong khoảng thời gian xác nhận đã cấu hình.

1. Trạm phát hiện điều kiện bằng các phép đo đã lọc.
2. Trạm ra lệnh tắt công tắc tải.
3. Trạng thái chuyển thành `TRIPPED_UVP`.
4. Giao diện người dùng và UART xác định UVP là nguyên nhân.
5. Chỉ khôi phục điện áp không làm tải được kết nối lại.

## UC-03 - Quá áp kéo dài

Luồng tổng quát giống UC-02, nhưng sử dụng ngưỡng OVP và nguyên nhân `TRIPPED_OVP`.

## UC-04 - Cảnh báo và trip quá dòng

1. Dòng điện duy trì trên ngưỡng cảnh báo gây ra trạng thái `WARNING_OCP`.
2. Trạm tiếp tục giám sát.
3. Nếu dòng điện đạt ngưỡng trip OCP, trạm ra lệnh ngắt.
4. Trạng thái chuyển thành `TRIPPED_OCP` và tiếp tục được giữ chốt.

Đường phần cứng nhanh vẫn chịu trách nhiệm giới hạn ngắn mạch cứng diễn ra nhanh hơn khả năng xử lý an toàn của firmware.

## UC-05 - Khôi phục thủ công

**Điều kiện tiên quyết:** Một trạng thái trip đang được giữ chốt.

1. Người vận hành loại bỏ nguyên nhân gây lỗi.
2. Trạm quan sát các giá trị bình thường trong khoảng thời gian duy trì trạng thái hết lỗi.
3. Người vận hành chủ ý yêu cầu reset.
4. Trạm xóa chốt, kiểm tra lại các phép đo và chỉ kết nối lại tải nếu an toàn.

**Luồng thay thế:** Yêu cầu reset khi lỗi vẫn tồn tại bị từ chối và tải tiếp tục bị ngắt.

## UC-06 - Chẩn đoán trong quá trình phát triển

Nhà phát triển kết nối UART và quan sát các mẫu đo, chuyển trạng thái và nguyên nhân trip trong khi chạy một ca kiểm thử đã được lập tài liệu. UART là bằng chứng hỗ trợ; nó không thay thế phép đo thời gian bên ngoài cho các tuyên bố về đáp ứng bảo vệ.
