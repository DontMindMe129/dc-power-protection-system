# Kế hoạch kiểm thử kiểm chứng

## 1. Mục tiêu

Chứng minh rằng hệ thống đề xuất đủ an toàn để chế tạo nguyên mẫu, có thể đo lường bằng thiết bị hiện có hoặc có thể mượn, và có khả năng đáp ứng từng yêu cầu V1 bắt buộc.

## 2. Chiến lược

Các phép thử tiến dần từ kích thích năng lượng thấp và giả lập đến hoạt động tích hợp 12 V / 1 A. Chỉ tăng năng lượng lỗi sau khi giai đoạn trước đạt yêu cầu.

```text
Rà soát yêu cầu
        -> tính toán và mô phỏng
        -> logic firmware với giá trị đưa vào giả lập
        -> thử đo lường/đóng cắt năng lượng thấp
        -> thử tích hợp danh định
        -> thử UVP/OVP/OCP có kiểm soát
        -> kiểm chứng định thời và nhiệt
```

## 3. Tiêu chí đầu vào

| Giai đoạn | Tiêu chí đầu vào |
| --- | --- |
| Rà soát tài liệu | Đã có mã yêu cầu và người phụ trách |
| Thử logic firmware | Đã xác định quy tắc trạng thái và ngưỡng |
| Phần cứng năng lượng thấp | Hoàn tất rà soát sơ đồ nguyên lý; giới hạn dòng đang hoạt động |
| Thử danh định 1 A | Đã kiểm tra PCB/đường dòng; các phép thử tĩnh dòng thấp đạt yêu cầu |
| Thử lỗi | Đã rà soát bảo vệ nhanh; có phương tiện ngắt khẩn cấp |
| Định thời/nhiệt cuối cùng | Nguyên mẫu tích hợp ổn định; đã đặt lịch thiết bị phù hợp |

## 4. Tiêu chí đầu ra

- Mọi yêu cầu đã chọn đều có bằng chứng `ĐẠT` hoặc sai lệch được chấp nhận rõ ràng.
- Các ngưỡng tạm thời đã được xác nhận hoặc thay đổi thông qua rà soát.
- Không còn lỗi quan trọng về an toàn chưa được giải quyết.
- Các báo cáo bắt buộc chứa dữ liệu thô và thông tin phiên bản.
- Ma trận truy vết khớp với phiên bản cuối của yêu cầu và phép thử.

## 5. Trình tự dự kiến

| Thứ tự | Nhóm kiểm thử | Cấp thiết bị | Mục đích |
| ---: | --- | --- | --- |
| 1 | Rà soát tài liệu/an toàn | Cơ bản | Loại bỏ mâu thuẫn và quy trình mất an toàn |
| 2 | Logic bảo vệ với giá trị đưa vào giả lập | Cơ bản | Chứng minh FSM, ngưỡng, chống dội và logic giữ chốt |
| 3 | Đo điện áp tĩnh | Trung cấp | Hiệu chuẩn `Vin` và `Vload` |
| 4 | Đo dòng điện tĩnh | Trung cấp | Hiệu chuẩn `Iload` |
| 5 | Khởi động/giao diện người dùng/reset | Trung cấp | Kiểm chứng hành vi tích hợp ở mức năng lượng thấp |
| 6 | Độ bền đường 1 A | Ưu tiên Đầy đủ | Kiểm chứng độ sụt áp và mức phát nhiệt |
| 7 | Ngưỡng UVP/OVP/OCP | Trung cấp | Kiểm chứng các chuyển trạng thái bảo vệ |
| 8 | Định thời đáp ứng/quá độ | Đầy đủ | Kiểm chứng định thời và khả năng loại bỏ quá độ |
| 9 | Thử lỗi phần cứng có kiểm soát | Đầy đủ, có cổng kiểm soát | Kiểm chứng bảo vệ độc lập nếu được yêu cầu |

## 6. Kế hoạch tài nguyên cho thiết lập Cơ bản đã chọn

### Công việc có thể bắt đầu mà không cần mượn thiết bị

- Rà soát yêu cầu và truy vết.
- Thử ngưỡng/trạng thái firmware bằng các giá trị đưa vào giả lập.
- Kiểm thử giao diện người dùng và UART.
- Mô phỏng và tính toán dung sai.
- Chuẩn bị bộ tải và bộ dây thử có che chắn.

### Thiết bị phải mượn hoặc sắp xếp

- Nguồn 9-15 V điều chỉnh được có giới hạn dòng.
- Bộ tải/tải điện tử đến ít nhất 1,2 A.
- Oscilloscope hoặc logic analyzer để đo thời gian đáp ứng.
- Thiết bị đo nhiệt độ cho phép thử độ bền.

## 7. Điều kiện dừng

Ngắt nguồn ngay lập tức khi xảy ra bất kỳ điều kiện nào sau đây:

- xuất hiện khói, mùi, hồ quang hoặc âm thanh bất ổn ngoài dự kiến;
- nhiệt độ đầu nối, dây dẫn, điện trở, shunt hoặc công tắc tăng bất thường;
- MCU reset lặp lại khi có tải;
- không thể vô hiệu hóa đầu ra tải;
- giới hạn dòng của nguồn kích hoạt ngoài trường hợp dự kiến;
- điện áp đo được vượt miền kiểm thử đã được phê duyệt.
