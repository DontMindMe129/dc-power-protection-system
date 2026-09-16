# Yêu cầu hệ thống

## 1. Quy ước yêu cầu

- **Đã chọn**: cơ sở tham chiếu đã được thống nhất cho giai đoạn hiện tại của dự án.
- **Tạm thời**: mục tiêu định lượng dùng để đánh giá tính khả thi; phải được xác nhận sau khi chọn linh kiện và đặc trưng hóa nguyên mẫu.
- **Chưa xác định**: chủ ý chưa được đặc tả.
- Từ "phải" biểu thị một yêu cầu bắt buộc.

Không khuyến khích thay đổi mã yêu cầu. Nếu một yêu cầu bị loại bỏ, hãy đánh dấu là đã ngừng áp dụng thay vì tái sử dụng mã đó.

## 2. Cơ sở vận hành

| Tham số | Giá trị | Trạng thái |
| --- | ---: | --- |
| Điện áp đầu vào danh định | 12,0 V DC | Đã chọn |
| Dải đo lường/bảo vệ có kiểm soát | 9,0-15,0 V DC | Đã chọn |
| Dòng tải liên tục tối đa | 1,0 A | Đã chọn |
| Ngưỡng UVP mặc định | 10,5 V | Tạm thời |
| Thời gian xác nhận UVP | 500 ms | Tạm thời |
| Ngưỡng OVP mặc định | 14,2 V | Tạm thời |
| Thời gian xác nhận OVP | 100 ms | Tạm thời |
| Ngưỡng cảnh báo OCP | 0,90 A | Tạm thời |
| Thời gian xác nhận cảnh báo OCP | 300 ms | Tạm thời |
| Ngưỡng trip OCP | 1,20 A | Tạm thời |
| Thời gian ngắt OCP | <=100 ms sau khi xác nhận vượt ngưỡng | Tạm thời |
| Thời gian duy trì trạng thái hết lỗi trước khi chấp nhận reset thủ công | 1 s | Tạm thời |

Các ngưỡng bảo vệ được chủ ý thiết kế để có thể cấu hình trong firmware. Giá trị mặc định cuối cùng phụ thuộc vào kịch bản nguồn/tải được chọn, độ chính xác cảm biến và vùng hoạt động an toàn của công tắc.

## 3. Yêu cầu về điện và đường công suất

| Mã | Yêu cầu | Trạng thái | Phương pháp kiểm chứng |
| --- | --- | --- | --- |
| SYS-ELEC-001 | Trạm phải chấp nhận nguồn DC danh định 12 V qua `DC IN`. | Đã chọn | Kiểm tra trực quan + thử nghiệm |
| SYS-ELEC-002 | Trạm phải duy trì trạng thái có kiểm soát trong toàn bộ dải đầu vào 9,0-15,0 V DC; điện áp ngoài vùng bình thường có thể gây trip có kiểm soát. | Đã chọn | Thử nghiệm |
| SYS-ELEC-003 | Đường công suất của trạm phải chịu được dòng 1,0 A liên tục trong 30 phút trong môi trường thử đã công bố. | Đã chọn | TC-PATH-001 |
| SYS-ELEC-004 | Độ sụt áp tổng `Vin - Vload` do trạm gây ra ở 1,0 A phải <=0,30 V sau khi ổn định nhiệt. | Tạm thời | TC-PATH-001 |
| SYS-ELEC-005 | Trong quá trình reset, đầu ra tải của trạm mặc định phải không được cấp điện cho đến khi khởi tạo và kiểm tra phép đo ban đầu hoàn tất. | Tạm thời | TC-START-001 |
| SYS-ELEC-006 | Việc MCU mất hoạt động hoặc reset không được làm vô hiệu đường bảo vệ nhanh theo dòng chuyên dụng. | Đã chọn | Kiểm tra trực quan + phân tích |
| SYS-ELEC-007 | Lỗi dòng điện có khả năng phá hủy phải được giới hạn bằng cơ chế phần cứng độc lập với hoạt động bình thường của firmware. | Đã chọn | Rà soát thiết kế; chỉ thử lỗi có kiểm soát sau khi qua cổng an toàn |

## 4. Yêu cầu đo lường

| Mã | Yêu cầu | Trạng thái | Phương pháp kiểm chứng |
| --- | --- | --- | --- |
| SYS-MEAS-001 | Trạm phải đo được `Vin` từ 9,0 đến 15,0 V. | Đã chọn | TC-MEAS-001 |
| SYS-MEAS-002 | Trạm phải đo được `Vload` từ 0 đến 15,0 V. | Đã chọn | TC-MEAS-002 |
| SYS-MEAS-003 | Trạm phải đo được `Iload` từ 0 đến ít nhất 1,20 A mà không làm ADC bão hòa. | Tạm thời | TC-MEAS-003 |
| SYS-MEAS-004 | Sau khi hiệu chuẩn, sai số `Vin` và `Vload` hiển thị phải <= +/-0,20 V tại các điểm thử đã quy định. | Tạm thời | TC-MEAS-001/002 |
| SYS-MEAS-005 | Sau khi hiệu chuẩn, sai số `Iload` hiển thị trong dải 0,10 đến 1,00 A phải <= giá trị lớn hơn giữa +/-0,05 A và +/-5% giá trị tham chiếu. | Tạm thời | TC-MEAS-003 |
| SYS-MEAS-006 | Thuật toán bảo vệ phải sử dụng các phép đo đã lọc và phải lập tài liệu về chu kỳ lấy mẫu, bộ lọc và thời gian xác nhận. | Đã chọn | Kiểm tra trực quan + thử nghiệm firmware sau này |
| SYS-MEAS-007 | Trạm phải tính toán và cung cấp độ sụt áp `Vin - Vload`. | Tạm thời | TC-MEAS-004 |

## 5. Hành vi bảo vệ

| Mã | Yêu cầu | Trạng thái | Phương pháp kiểm chứng |
| --- | --- | --- | --- |
| SYS-PROT-001 | Nếu `Vin < 10,5 V` liên tục trong 500 ms, trạm phải chuyển sang trạng thái trip UVP và ra lệnh tắt công tắc tải. | Tạm thời | TC-UVP-001 |
| SYS-PROT-002 | Quá độ dưới ngưỡng UVP kéo dài dưới 300 ms không được gây trip UVP. | Tạm thời | TC-UVP-002 |
| SYS-PROT-003 | Nếu `Vin > 14,2 V` liên tục trong 100 ms, trạm phải chuyển sang trạng thái trip OVP và ra lệnh tắt công tắc tải. | Tạm thời | TC-OVP-001 |
| SYS-PROT-004 | Nếu `Iload > 0,90 A` liên tục trong 300 ms, trạm phải đưa ra cảnh báo quá dòng mà không nhất thiết ngắt tải. | Tạm thời | TC-OCP-001 |
| SYS-PROT-005 | Nếu `Iload >= 1,20 A`, trạm phải ra lệnh ngắt tải trong vòng 100 ms kể từ khi xác nhận vượt ngưỡng. | Tạm thời | TC-OCP-002 |
| SYS-PROT-006 | Sau khi xảy ra bất kỳ trip UVP, OVP hoặc OCP nào, trạng thái trip phải được giữ chốt khi giá trị đo trở lại bình thường. | Đã chọn | TC-RESET-001 |
| SYS-PROT-007 | Trạm chỉ được chấp nhận reset thủ công sau khi mọi điều kiện trip được giám sát đều hết lỗi liên tục ít nhất 1 s. | Tạm thời | TC-RESET-002 |
| SYS-PROT-008 | Trạm phải ghi nhận hoặc hiển thị nguyên nhân chính của lần trip gần nhất. | Đã chọn | TC-DIAG-001 |
| SYS-PROT-009 | Không được tuyên bố bảo vệ bằng firmware là lớp bảo vệ duy nhất chống ngắn mạch cứng. | Đã chọn | Kiểm tra trực quan |

## 6. Yêu cầu về giao diện người dùng và chẩn đoán

| Mã | Yêu cầu | Trạng thái | Phương pháp kiểm chứng |
| --- | --- | --- | --- |
| SYS-UI-001 | Giao diện người dùng cục bộ phải phân biệt được ít nhất các trạng thái `STARTUP`, `NORMAL`, `WARNING` và `TRIPPED`. | Đã chọn | TC-UI-001 |
| SYS-UI-002 | Trong hoạt động bình thường, màn hình phải hiển thị `Vin`, `Vload` và `Iload` theo đơn vị kỹ thuật. | Đã chọn | TC-UI-002 |
| SYS-UI-003 | Chỉ báo trip phải xác định rõ UVP, OVP hoặc OCP thay vì chỉ hiển thị lỗi chung chung. | Đã chọn | TC-DIAG-001 |
| SYS-UI-004 | Thao tác reset phải yêu cầu một hành động nút nhấn có chủ ý và không được xảy ra chỉ từ một cạnh tín hiệu chưa chống dội. | Tạm thời | TC-RESET-003 |
| SYS-DIAG-001 | Chẩn đoán UART phải cung cấp các chuyển trạng thái và giá trị đo đủ để hỗ trợ bằng chứng kiểm thử. | Tạm thời | TC-DIAG-002 |

## 7. Yêu cầu về an toàn và kiểm chứng

| Mã | Yêu cầu | Trạng thái | Phương pháp kiểm chứng |
| --- | --- | --- | --- |
| SYS-SAFE-001 | Các phép thử phát triển có cấp nguồn phải sử dụng nguồn có giới hạn dòng đã biết hoặc thiết bị bảo vệ nối tiếp tương đương. | Đã chọn | Đánh giá quy trình |
| SYS-SAFE-002 | Không được nối tắt trực tiếp `DC OUT` khi dùng adapter cố định không được kiểm soát. | Đã chọn | Đánh giá quy trình |
| SYS-SAFE-003 | Các linh kiện trên đường 1 A phải được chọn với biên điện áp, dòng điện, công suất và nhiệt đã được lập tài liệu. | Đã chọn | Rà soát thiết kế |
| SYS-SAFE-004 | Thiết lập kiểm thử phải có phương tiện dễ tiếp cận để ngắt nguồn đầu vào. | Đã chọn | Danh sách kiểm tra trước thử nghiệm |
| SYS-VER-001 | Mọi yêu cầu hệ thống bắt buộc phải có phương pháp kiểm chứng trước khi thiết kế chi tiết được phê duyệt. | Đã chọn | Rà soát truy vết |
| SYS-VER-002 | Mọi phép thử đã thực thi phải ghi lại phiên bản phần cứng, phiên bản firmware, thiết lập, kết quả mong đợi, kết quả thực tế và kết luận đạt/không đạt. | Đã chọn | Rà soát kết quả |
| SYS-VER-003 | Tuyên bố về thời gian đáp ứng bảo vệ phải được kiểm chứng ít nhất một lần trên nguyên mẫu tích hợp bằng oscilloscope, logic analyzer hoặc thiết bị đo thời gian tương đương. | Đã chọn | TC-TIME-001 |

## 8. Các yêu cầu chủ ý để ở trạng thái chưa xác định

- Mức tăng nhiệt tối đa cho phép của PCB/linh kiện ở 1 A.
- Điện áp đầu vào cực đại tuyệt đối khi không hoạt động.
- Định mức dòng và khả năng giữ cơ khí của đầu nối.
- Hành vi khi ngược cực.
- Mức xung/quá độ đầu vào.
- Tần số cập nhật OLED và tốc độ baud UART.
- Độ trôi phép đo dài hạn.

Các giá trị này phụ thuộc vào lựa chọn phần cứng chi tiết. Chúng phải được giải quyết trước khi phê duyệt sơ đồ nguyên lý, không được phỏng đoán trong giai đoạn yêu cầu.
