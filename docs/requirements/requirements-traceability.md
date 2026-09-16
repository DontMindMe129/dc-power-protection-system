# Ma trận truy vết yêu cầu

Các giá trị trạng thái: `ĐÃ LÊN KẾ HOẠCH`, `BỊ CHẶN`, `ĐẠT`, `KHÔNG ĐẠT`, `CHƯA CHẠY`.

| Yêu cầu | Phương pháp kiểm chứng chính | Trạng thái hiện tại | Tài nguyên hoặc quyết định gây chặn |
| --- | --- | --- | --- |
| SYS-ELEC-001 | Kiểm tra trực quan + TC-START-001 | CHƯA CHẠY | Phần cứng chưa được chế tạo |
| SYS-ELEC-002 | TC-MEAS-001, TC-UVP-001, TC-OVP-001 | BỊ CHẶN | Cần nguồn 9-15 V điều chỉnh được |
| SYS-ELEC-003 | TC-PATH-001 | BỊ CHẶN | Cần tải 1 A và phương tiện quan sát nhiệt độ |
| SYS-ELEC-004 | TC-PATH-001 | BỊ CHẶN | Phần cứng chưa được chế tạo |
| SYS-ELEC-005 | TC-START-001 | CHƯA CHẠY | Phần cứng/firmware chưa được xây dựng |
| SYS-ELEC-006 | Rà soát thiết kế | CHƯA CHẠY | Chưa xác định cấu trúc bảo vệ nhanh |
| SYS-ELEC-007 | Rà soát thiết kế + thử lỗi có kiểm soát | BỊ CHẶN | Cần thiết lập phòng thí nghiệm có giới hạn dòng |
| SYS-MEAS-001 | TC-MEAS-001 | BỊ CHẶN | Cần nguồn điều chỉnh được |
| SYS-MEAS-002 | TC-MEAS-002 | BỊ CHẶN | Cần nguyên mẫu |
| SYS-MEAS-003 | TC-MEAS-003 | BỊ CHẶN | Cần bộ tải |
| SYS-MEAS-004 | TC-MEAS-001/002 | BỊ CHẶN | Cần DMM tham chiếu đã hiệu chuẩn |
| SYS-MEAS-005 | TC-MEAS-003 | BỊ CHẶN | Cần bộ tải/phép đo tham chiếu |
| SYS-MEAS-006 | Kiểm tra trực quan + thử firmware sau này | CHƯA CHẠY | Thuật toán chưa được thiết kế |
| SYS-MEAS-007 | TC-MEAS-004 | CHƯA CHẠY | Cần nguyên mẫu |
| SYS-PROT-001 | TC-UVP-001 | BỊ CHẶN | Cần nguồn điều chỉnh được |
| SYS-PROT-002 | TC-UVP-002 | BỊ CHẶN | Cần nguồn quá độ/thời gian có khả năng lặp lại |
| SYS-PROT-003 | TC-OVP-001 | BỊ CHẶN | Cần nguồn điều chỉnh được |
| SYS-PROT-004 | TC-OCP-001 | BỊ CHẶN | Cần tải có thể điều khiển |
| SYS-PROT-005 | TC-OCP-002 + TC-TIME-001 | BỊ CHẶN | Cần tải có thể điều khiển và thiết bị đo thời gian |
| SYS-PROT-006 | TC-RESET-001 | CHƯA CHẠY | Cần nguyên mẫu |
| SYS-PROT-007 | TC-RESET-002 | CHƯA CHẠY | Cần nguyên mẫu |
| SYS-PROT-008 | TC-DIAG-001 | CHƯA CHẠY | Giao diện người dùng/firmware chưa được xây dựng |
| SYS-PROT-009 | Kiểm tra trực quan | ĐÃ LÊN KẾ HOẠCH | Được bắt buộc thông qua rà soát thiết kế |
| SYS-UI-001 | TC-UI-001 | CHƯA CHẠY | Giao diện người dùng chưa được xây dựng |
| SYS-UI-002 | TC-UI-002 | CHƯA CHẠY | Giao diện người dùng chưa được xây dựng |
| SYS-UI-003 | TC-DIAG-001 | CHƯA CHẠY | Giao diện người dùng chưa được xây dựng |
| SYS-UI-004 | TC-RESET-003 | CHƯA CHẠY | Thiết kế đầu vào chưa được xây dựng |
| SYS-DIAG-001 | TC-DIAG-002 | CHƯA CHẠY | Chưa xác định định dạng UART |
| SYS-SAFE-001 | Danh sách kiểm tra trước thử nghiệm | ĐÃ LÊN KẾ HOẠCH | Phải có nguồn phù hợp với giới hạn dòng |
| SYS-SAFE-002 | Đánh giá quy trình | ĐÃ LÊN KẾ HOẠCH | Đã bị cấm rõ ràng trong quy trình an toàn |
| SYS-SAFE-003 | Rà soát thiết kế | CHƯA CHẠY | Chưa xác định linh kiện |
| SYS-SAFE-004 | Danh sách kiểm tra trước thử nghiệm | ĐÃ LÊN KẾ HOẠCH | Phụ thuộc thiết lập kiểm thử |
| SYS-VER-001 | Ma trận này | ĐÃ LÊN KẾ HOẠCH | Cập nhật khi yêu cầu thay đổi |
| SYS-VER-002 | Rà soát mẫu kết quả | ĐÃ LÊN KẾ HOẠCH | Chưa thực thi phép thử nào |
| SYS-VER-003 | TC-TIME-001 | BỊ CHẶN | Cần oscilloscope/logic analyzer |

## Tóm tắt độ bao phủ

- Hành vi chức năng tĩnh có thể được kiểm thử bằng DMM, tải điện trở phù hợp và mức DC điều chỉnh được an toàn.
- Không thể kiểm chứng đầy đủ định thời ngưỡng động nếu chỉ dùng adapter cố định và đồng hồ vạn năng.
- Hành vi ngắn mạch cứng phải tiếp tục ở trạng thái bị chặn cho đến khi thiết kế bảo vệ phần cứng được rà soát và có nguồn phòng thí nghiệm giới hạn dòng.
