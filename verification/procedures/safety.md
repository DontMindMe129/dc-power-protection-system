# Quy trình an toàn khi thử có cấp nguồn

## Quy tắc bắt buộc

- Chỉ làm việc với DC điện áp thấp cách ly trong miền 9-15 V đã được phê duyệt.
- Không kết nối trực tiếp dự án với điện áp lưới.
- Kiểm tra cực tính nguồn và điện áp thực tế bằng DMM trước khi kết nối.
- Sử dụng nguồn có giới hạn dòng đã biết, hoặc bổ sung cầu chì/giới hạn nối tiếp phù hợp với giai đoạn và đã được rà soát.
- Duy trì phương tiện ngắt nguồn đầu vào dễ tiếp cận.
- Ngắt nguồn khỏi thiết lập trước khi thay đổi dây nối hoặc giá trị tải.
- Xem điện trở công suất là nguy cơ gây bỏng; đặt chúng trên bề mặt không cháy với khoảng hở phù hợp.
- Không phụ thuộc vào breadboard không hàn cho đường công suất 1 A cuối cùng nếu chưa đánh giá rõ ràng.
- Không nối tắt `DC OUT` khi dùng adapter cố định không được kiểm soát.
- Dừng ngay khi có khói, mùi, hồ quang, dòng không ổn định, MCU reset lặp lại hoặc phát nhiệt bất thường.

## Danh sách kiểm tra trước thử nghiệm

- [ ] Đã xác định phiên bản ca kiểm thử.
- [ ] Đã ghi phiên bản phần cứng và firmware.
- [ ] Đã kiểm tra dây nối theo sơ đồ.
- [ ] Đã kiểm tra điện áp nguồn trước khi kết nối.
- [ ] Đã kiểm tra giới hạn dòng hoặc cầu chì.
- [ ] Đã tính giá trị tải và định mức công suất.
- [ ] Que đo DMM được cắm đúng cổng và chọn đúng chế độ.
- [ ] Có thể tiếp cận phương tiện ngắt nguồn khẩn cấp.
- [ ] Đã ghi lại giá trị đọc dự kiến và ngưỡng dừng.
- [ ] Không có bước thử ngắn mạch chưa được cho phép.

## Sau thử nghiệm

Ngắt nguồn đầu vào, chờ điện trở công suất nguội, lưu dữ liệu đo thô và ghi lại mọi bất thường trước khi thay đổi thiết lập.
