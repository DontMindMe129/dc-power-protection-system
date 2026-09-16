# Danh mục ca kiểm thử kiểm chứng

## Các trường kết quả chung

Với mỗi lần thực thi, sao chép ca kiểm thử vào một tệp kết quả có ngày tháng và ghi lại phiên bản phần cứng, commit firmware, thiết lập nguồn/tải, thiết bị đo, dữ liệu đọc thô, kết quả mong đợi, kết quả thực tế và trạng thái.

## TC-START-001 - Khởi động an toàn

**Yêu cầu:** SYS-ELEC-001, SYS-ELEC-005

**Thiết bị:** Nguồn 12 V có giới hạn dòng, tải nhỏ an toàn, DMM

**Quy trình:**

1. Đặt nguồn ở 12,0 V với giới hạn dòng thận trọng.
2. Kết nối trạm và một tải nhỏ.
3. Cấp nguồn trong khi quan sát đầu ra tải.
4. Lặp lại từ trạng thái mất nguồn và trạng thái reset MCU.

**Đạt:** Tải không được cấp điện trước khi hoàn tất kiểm tra khởi tạo; trạm đạt trạng thái `NORMAL` mà không có đóng cắt mất kiểm soát.

## TC-MEAS-001 - Độ chính xác điện áp đầu vào

**Yêu cầu:** SYS-MEAS-001, SYS-MEAS-004

**Thiết bị:** Nguồn điều chỉnh được và DMM tham chiếu

**Các điểm thử:** 9,0; 10,5; 12,0; 14,2 và 15,0 V, với hành động bảo vệ được cô lập hoặc được xử lý theo quy trình.

**Đạt:** Sai số tuyệt đối hiển thị tại mọi điểm <=0,20 V sau khi hiệu chuẩn.

## TC-MEAS-002 - Độ chính xác điện áp tải

**Yêu cầu:** SYS-MEAS-002, SYS-MEAS-004

**Quy trình:** Thử các giá trị đầu ra đại diện, bao gồm 0 V khi công tắc tắt và hoạt động có tải bình thường. So sánh điện áp đầu cực với giá trị trên màn hình/UART.

**Đạt:** Sai số tuyệt đối tại mọi điểm đã công bố <=0,20 V.

## TC-MEAS-003 - Độ chính xác dòng tải

**Yêu cầu:** SYS-MEAS-003, SYS-MEAS-005

**Thiết bị:** Nguồn 12 V có giới hạn dòng, bộ tải/tải điện tử, phép đo dòng tham chiếu

**Các điểm thử:** 0,10; 0,25; 0,50; 0,90; 1,00 và 1,20 A.

**Đạt:** Trong dải 0,10 đến 1,00 A, sai số <= giá trị lớn hơn giữa 0,05 A và 5% giá trị tham chiếu; mạch đầu vào đo không bão hòa tại 1,20 A.

## TC-MEAS-004 - Tính toán độ sụt áp

**Yêu cầu:** SYS-MEAS-007

**Đạt:** Độ sụt áp được báo cáo bằng `Vin - Vload` đã báo cáo trong giới hạn làm tròn số và nhất quán với giá trị đọc DMM.

## TC-PATH-001 - Độ bền và sụt áp ở một ampere

**Yêu cầu:** SYS-ELEC-003, SYS-ELEC-004, SYS-SAFE-003

**Thiết bị:** Nguồn 12 V có giới hạn dòng, tải 1 A, hai phép đo điện áp hoặc phương pháp DMM có khả năng lặp lại, thiết bị đo nhiệt độ

**Quy trình:**

1. Kiểm tra định mức của mọi linh kiện và đầu nối.
2. Vận hành ở khoảng 12 V và 1,0 A trong 30 phút.
3. Ghi `Vin`, `Vload`, dòng điện và nhiệt độ quan sát được lúc bắt đầu và theo các khoảng thời gian đều đặn.
4. Dừng khi xuất hiện bất kỳ xu hướng mất an toàn nào.

**Đạt:** Hoạt động vẫn được kiểm soát; độ sụt áp của trạm <=0,30 V sau khi ổn định nhiệt; không linh kiện nào vượt giới hạn đã rà soát.

## TC-UVP-001 - Trip thấp áp kéo dài

**Yêu cầu:** SYS-PROT-001

**Thiết bị:** Nguồn điều chỉnh được, thiết bị đo thời gian cho lần chạy cuối

**Quy trình:** Bắt đầu ở 12 V, sau đó giảm xuống dưới 10,5 V trong khi ghi lại `Vin`, lệnh/đầu ra công tắc và trạng thái.

**Đạt:** Điều kiện thấp áp liên tục gây trip UVP sau khoảng xác nhận đã cấu hình và tải tiếp tục tắt.

## TC-UVP-002 - Loại bỏ quá độ thấp áp

**Yêu cầu:** SYS-PROT-002

**Thiết bị:** Kích thích đóng cắt có khả năng lặp lại và thiết bị đo thời gian

**Đạt:** Xung dưới ngưỡng ngắn hơn 300 ms không giữ chốt UVP; trường hợp kéo dài vẫn gây trip.

## TC-OVP-001 - Trip quá áp kéo dài

**Yêu cầu:** SYS-PROT-003

**Thiết bị:** Nguồn điều chỉnh được có giới hạn dòng; không vượt quá 15 V

**Đạt:** Giá trị liên tục trên 14,2 V gây trip OVP sau khoảng thời gian đã cấu hình; tải tiếp tục bị ngắt.

## TC-OCP-001 - Cảnh báo quá dòng

**Yêu cầu:** SYS-PROT-004

**Thiết bị:** Tải có thể điều khiển

**Quy trình:** Tăng dòng trên 0,90 A nhưng dưới ngưỡng trip trong hơn 300 ms.

**Đạt:** Trạm chỉ báo cảnh báo OCP và tiếp tục giám sát có kiểm soát.

## TC-OCP-002 - Trip quá dòng

**Yêu cầu:** SYS-PROT-005

**Thiết bị:** Nguồn có giới hạn dòng, tải có thể điều khiển và thiết bị đo thời gian

**Quy trình:** Áp dụng bước dòng có kiểm soát đến ít nhất 1,20 A mà không tạo ngắn mạch cứng.

**Đạt:** Trạm ra lệnh tắt công tắc và chuyển sang trip OCP được giữ chốt; định thời cuối cùng được đánh giá trong TC-TIME-001.

## TC-TIME-001 - Đáp ứng ngắt vật lý

**Yêu cầu:** SYS-PROT-005, SYS-VER-003

**Thiết bị:** Oscilloscope hoặc logic analyzer cùng phương tiện quan sát dòng/điện áp an toàn

**Đại lượng đo:** Thời gian từ khi xác nhận vượt ngưỡng tại ranh giới đo lường/logic đến khi quan sát được tải bị ngắt từ bên ngoài.

**Đạt:** <=100 ms đối với yêu cầu OCP tạm thời. Phải ghi lại chính xác các điểm kích hoạt và kênh đo.

## TC-RESET-001 - Trip được giữ chốt

**Yêu cầu:** SYS-PROT-006

**Đạt:** Đưa biến gây lỗi trở lại bình thường không tự động kết nối lại tải.

## TC-RESET-002 - Chỉ reset sau khi hết lỗi

**Yêu cầu:** SYS-PROT-007

**Đạt:** Reset bị từ chối khi lỗi còn hoạt động và chỉ được chấp nhận sau khi mọi lỗi được giám sát đều hết lỗi liên tục ít nhất 1 s.

## TC-RESET-003 - Reset có chủ ý/chống dội

**Yêu cầu:** SYS-UI-004

**Đạt:** Dội tiếp điểm hoặc một cạnh ngắn không hợp lệ không xóa chốt; thao tác người dùng đã được lập tài liệu thì có thể xóa chốt.

## TC-UI-001 - Chỉ báo trạng thái

**Yêu cầu:** SYS-UI-001

**Đạt:** Các trạng thái `STARTUP`, `NORMAL`, `WARNING` và `TRIPPED` không gây nhầm lẫn trong các trường hợp giả lập hoặc vật lý.

## TC-UI-002 - Hiển thị phép đo

**Yêu cầu:** SYS-UI-002

**Đạt:** `Vin`, `Vload` và `Iload` được hiển thị kèm đơn vị và không hiển thị các giá trị bình thường cũ như thể vẫn hợp lệ trong khi trip hoặc có lỗi cảm biến.

## TC-DIAG-001 - Chỉ báo nguyên nhân trip

**Yêu cầu:** SYS-PROT-008, SYS-UI-003

**Đạt:** Các kích thích UVP, OVP và OCP riêng biệt tạo ra đúng nguyên nhân được lưu giữ.

## TC-DIAG-002 - Bằng chứng UART

**Yêu cầu:** SYS-DIAG-001

**Đạt:** Log chứa các phép đo/chuyển trạng thái cần thiết để đối chiếu phép thử mà không làm ảnh hưởng định thời bảo vệ.

## Trạng thái thử ngắn mạch cứng

Không có ca thử ngắn mạch cứng trực tiếp nào được cho phép trong phiên bản này. Chỉ có thể bổ sung phép thử như vậy sau khi:

- cấu trúc bảo vệ nhanh và giới hạn năng lượng của linh kiện đã được rà soát;
- có nguồn giới hạn dòng và phương tiện ngắt khẩn cấp;
- mục tiêu chính xác và tiêu chí đạt đã được quy định;
- phép thử được giám sát theo quy định phòng thí nghiệm của môn học.
