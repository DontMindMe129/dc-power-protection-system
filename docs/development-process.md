# Quy trình phát triển dự án

> Trạng thái: đã thống nhất để định hướng repo. Quy trình dựa trên nội dung môn Thiết kế Hệ thống Nhúng và được điều chỉnh cho dự án có PCB thực tế.

## 1. Nguyên tắc

- Phát triển từ yêu cầu đến thiết kế chi tiết bằng cách bổ sung dần mức độ cụ thể.
- Không coi một quyết định là đã phê duyệt khi nội dung vẫn chưa được hiểu hoặc xác nhận.
- Mỗi yêu cầu phải có cách kiểm chứng dự kiến trước khi triển khai.
- Phần cứng, phần mềm và giao diện được phát triển song song sau khi phân chia hệ thống.
- Mô phỏng diễn ra trong quá trình thiết kế, không chờ tới khi hoàn tất PCB.
- Chỉ tạo thư mục hoặc tài liệu khi dự án thực sự bước vào giai đoạn tương ứng.

## 2. Các giai đoạn

| Giai đoạn | Công việc chính | Đầu ra trong repo | Điều kiện chuyển bước |
| --- | --- | --- | --- |
| 0. Bảo tồn và khởi động lại | Lưu phiên bản cũ và tạo nhánh làm lại | `main`, tag `archive/generated-v1`, nhánh rebuild | Phiên bản cũ có thể khôi phục; nhánh mới sạch và độc lập |
| 1. Xác định sản phẩm | Làm rõ vấn đề, ranh giới, nguồn, tải, I/O, chức năng, use case và ràng buộc | `concept.md`, sau đó là `product-requirements.md` và `project-plan.md` | Phạm vi nhất quán, khả thi và người thực hiện giải thích được toàn bộ concept |
| 2. Đặc tả thiết kế | Kiến trúc hệ thống, sơ đồ khối, hành vi, luồng công suất/tín hiệu, trạng thái và phân chia HW/SW | `design-specification.md` | Mọi khối có trách nhiệm và giao diện rõ; kiến trúc đáp ứng yêu cầu sản phẩm |
| 3. Đặc tả thành phần | Định nghĩa chi tiết phần cứng, phần mềm và cách kiểm thử | `hardware-specification.md`, `software-specification.md`, `test-specification.md` | Các đặc tả thống nhất về giao diện và mọi yêu cầu chính có phương pháp kiểm chứng |
| 4. Thiết kế và triển khai | Tính toán, chọn linh kiện, mô phỏng, schematic, PCB và phát triển firmware | `hardware/`, `firmware/` | Thiết kế được rà soát; mô phỏng đạt; tệp PCB sẵn sàng sản xuất; firmware lõi có thể kiểm tra |
| 5. Chế tạo và tích hợp | Đặt PCB, lắp ráp, bring-up từng khối và tích hợp HW/SW | PCB thực tế, BOM chốt, bản ghi bring-up | Nguồn cục bộ, đo lường, switch, bảo vệ, MCU và UI hoạt động cùng nhau |
| 6. Kiểm chứng | Thực hiện phép thử, so sánh với yêu cầu và lưu bằng chứng | `verification/` | Các yêu cầu bắt buộc có kết luận và sai lệch được giải thích |
| 7. Bàn giao | Hoàn thiện báo cáo, demo và bài thuyết trình | `deliverables/` | Báo cáo phản ánh đúng quá trình và khớp với phiên bản sản phẩm đã kiểm thử |

## 3. Bộ đặc tả theo môn học

Năm nhóm tài liệu của System Specification được ánh xạ vào repo như sau:

| Tài liệu môn học | Vai trò | Tệp dự kiến |
| --- | --- | --- |
| Product Requirement | Mô tả sản phẩm phải làm gì và các ràng buộc cấp sản phẩm | `docs/product-requirements.md` |
| Design Specification | Mô tả kiến trúc, hành vi, phân rã chức năng và giao diện | `docs/design-specification.md` |
| Hardware Specification | Mô tả cách triển khai phần cứng và các giới hạn điện | `docs/hardware-specification.md` |
| Software Specification | Mô tả kiến trúc phần mềm, trạng thái, thuật toán và driver | `docs/software-specification.md` |
| Test Specification | Mô tả cách chứng minh hệ thống đáp ứng yêu cầu | `docs/test-specification.md` |

## 4. Yêu cầu của môn học áp dụng cho dự án

- Báo cáo phải trình bày theo quy trình thiết kế hệ thống nhúng.
- Thiết kế phải được mô phỏng bằng Proteus trong phạm vi công cụ hỗ trợ.
- Dự án phải có nguyên mẫu; nhóm đã chọn triển khai PCB thực tế.
- Báo cáo phải thể hiện việc phân chia và phối hợp công việc nhóm khi có nhiều thành viên.
- Phần trình diễn/thuyết trình được chuẩn bị sau khi hệ thống đã tích hợp và có bằng chứng kiểm thử.

## 5. Trạng thái hiện tại

Dự án đang ở Giai đoạn 1. `concept.md` và bản cơ sở `product-requirements.md` đang được phát triển. Các thông số tạm thời và vấn đề còn mở trong yêu cầu sản phẩm phải tiếp tục được kiểm tra trước khi chốt kiến trúc và chuyển sang các đặc tả thành phần.
