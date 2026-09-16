# Hệ thống giám sát và bảo vệ nguồn DC

> Trạng thái: đang ở giai đoạn đánh giá tính khả thi và xác định yêu cầu. Chưa có phương án triển khai phần cứng hoặc firmware nào được phê duyệt.

Kho lưu trữ này chứa toàn bộ hồ sơ kỹ thuật của một đồ án môn Thiết kế Hệ thống Nhúng. Hệ thống đề xuất được lắp giữa một nguồn DC bên ngoài và một tải bên ngoài. Hệ thống đo trạng thái điện của nguồn và tải, cảnh báo người dùng về các điều kiện bất thường, đồng thời ngắt tải khi xác nhận một điều kiện bảo vệ đã cấu hình.

```text
Nguồn DC bên ngoài
        |
        v
DC IN -> bảo vệ đầu vào -> đo lường -> công tắc tải -> DC OUT
                               |             ^
                               v             |
                         Bộ điều khiển STM32 -+
                               |
                         OLED / nút nhấn /
                         còi báo / UART
        |
        v
Tải bên ngoài
```

## Cơ sở tham chiếu hiện tại

Các giá trị dưới đây là cơ sở tham chiếu dùng để phân tích tính khả thi. Những giá trị được đánh dấu **Tạm thời** phải được xác nhận sau khi chọn linh kiện và đo trên nguyên mẫu.

| Hạng mục | Quyết định hiện tại |
| --- | --- |
| Nguồn danh định | 12 V DC |
| Dải đầu vào được hỗ trợ để đánh giá đo lường/bảo vệ | 9-15 V DC |
| Dòng tải liên tục tối đa | 1,0 A |
| Đại lượng đo chính | `Vin`, `Vload`, `Iload` |
| Phạm vi bảo vệ V1 | UVP, OVP, OCP |
| Hành vi khi trip | Giữ chốt trạng thái trip; reset thủ công sau khi lỗi đã được loại bỏ |
| Phản ứng với ngắn mạch | Bảo vệ nhanh bằng phần cứng; firmware ghi nhận/hiển thị sự kiện khi có thể |
| Bảo vệ quá nhiệt | Nằm ngoài phạm vi V1 |
| Bộ điều khiển dự kiến | STM32F103C8T6 |
| Giao diện người dùng dự kiến | OLED SSD1306, ba nút nhấn, LED/còi báo, UART gỡ lỗi |
| Tài nguyên kiểm thử hiện có | Dụng cụ cơ bản; khả năng tiếp cận thiết bị phòng thí nghiệm điều chỉnh được/có giới hạn dòng còn hạn chế |

## Sơ đồ kho lưu trữ

| Đường dẫn | Mục đích | Mức độ chi tiết hiện tại |
| --- | --- | --- |
| `docs/requirements/` | Yêu cầu sản phẩm và hệ thống, ca sử dụng, truy vết, tính khả thi | Chi tiết |
| `docs/specifications/` | Đặc tả thiết kế, phần cứng, phần mềm và kiểm thử | Đặc tả kiểm thử đã chi tiết; các phần khác còn là khung |
| `docs/architecture/` | Ngữ cảnh hệ thống, phân chia hệ thống và các giao diện | Khung chờ hoàn thiện |
| `firmware/` | Dự án firmware cho MCU | Khung chờ hoàn thiện |
| `hardware/` | Sơ đồ nguyên lý, PCB, mô phỏng, BOM và dữ liệu sản xuất | Khung chờ hoàn thiện |
| `verification/` | Kế hoạch, quy trình, ca và kết quả kiểm thử | Kế hoạch và ca kiểm thử đã chi tiết; chưa có kết quả |
| `deliverables/` | Báo cáo, slide và tài liệu demo | Khung chờ hoàn thiện |
| `references/` | Bảng dữ liệu linh kiện và chỉ mục nguồn tham khảo | Khung chờ hoàn thiện |
| `project/` | Cột mốc, phân công sở hữu và quy trình đóng góp | Khung ban đầu |

## Bắt đầu từ đây

1. Đọc [`docs/requirements/product-requirements.md`](docs/requirements/product-requirements.md).
2. Xem xét các yêu cầu đo được trong [`docs/requirements/system-requirements.md`](docs/requirements/system-requirements.md).
3. Kiểm tra độ bao phủ kiểm thử trong [`docs/requirements/requirements-traceability.md`](docs/requirements/requirements-traceability.md).
4. Đọc kết luận về tính khả thi trong [`docs/requirements/feasibility-assessment.md`](docs/requirements/feasibility-assessment.md).
5. Trước mọi phép thử có cấp nguồn, tuân thủ [`verification/procedures/safety.md`](verification/procedures/safety.md).

## Quy tắc ra quyết định tiếp tục

Dự án chỉ được chuyển sang giai đoạn kiến trúc và lựa chọn linh kiện khi:

- mọi yêu cầu quan trọng về an toàn đều có phương pháp kiểm chứng đáng tin cậy;
- phép đo điện áp/dòng điện tĩnh có thể được kiểm thử bằng thiết bị hiện có;
- nhóm có thể tạm thời tiếp cận nguồn điều chỉnh được có giới hạn dòng và thiết bị đo thời gian để kiểm chứng phản ứng bảo vệ cuối cùng;
- công tắc tải, mạch cảm biến dòng và đường công suất trên PCB có thể chịu an toàn dòng 1 A liên tục;
- không thử ngắn mạch trực tiếp trước khi bảo vệ nhanh bằng phần cứng được đánh giá độc lập và có giới hạn dòng.

## Giấy phép

Chưa chọn giấy phép nguồn mở. Không được mặc định rằng có quyền phân phối lại cho đến khi nhóm lựa chọn giấy phép.
