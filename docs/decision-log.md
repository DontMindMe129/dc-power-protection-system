# Decision log

> Phạm vi nguồn: session thiết kế lại dự án trong Codex, cập nhật đến 2026-09-18.
> Nguyên tắc: chỉ lời xác nhận của người dùng hoặc việc người dùng phê duyệt trực tiếp nội dung ngay trước đó mới được xem là quyết định. Một đề xuất kỹ thuật không tự trở thành baseline.

## 1. Trạng thái

- **Đã xác nhận:** quyết định hiện hành đã được người dùng lựa chọn.
- **Tạm thời:** baseline đã được người dùng chấp nhận để thiết kế tiếp nhưng có thể thay đổi sau tính toán hoặc thử nghiệm.
- **Đề xuất chưa duyệt:** đã có phương án hoặc con số nhưng chưa được người dùng chấp nhận.
- **Đã bác bỏ:** người dùng đã loại khỏi phạm vi hiện tại.
- **TBD:** chưa có giá trị đủ căn cứ hoặc chưa chọn phương án.

## 2. Quyết định sản phẩm và phạm vi

| ID | Ngày | Chủ đề | Quyết định | Trạng thái | Bằng chứng từ người dùng | Tài liệu bị ảnh hưởng |
| --- | --- | --- | --- | --- | --- | --- |
| DL-001 | 2026-09-17 | Cách xây dựng lại | Giữ phiên bản cũ; xây lại trên nhánh riêng và phát triển theo từng phần đã hiểu | Đã xác nhận | Yêu cầu lưu flow, bắt đầu Giai đoạn 0–1 và phê duyệt quy trình | `development-process.md`, `README.md` |
| DL-002 | 2026-09-17 | Tên sản phẩm | “Hệ thống giám sát và bảo vệ đường nguồn DC”; bỏ “configurable” khỏi tên | Đã xác nhận | “bỏ configurable được không nhỉ” rồi “approve” | `README.md`, `concept.md`, `product-requirements.md` |
| DL-003 | 2026-09-17 | System boundary | Thiết bị nằm giữa nguồn DC ngoài và tải DC ngoài; adapter AC–DC nằm ngoài project | Đã xác nhận | “dự án hiện tại đóng vai trò trung gian giữa nguồn và tải” | `concept.md` §2, `product-requirements.md` §2–3 |
| DL-004 | 2026-09-17 | PCB | Sản phẩm cuối phải có PCB thực tế | Đã xác nhận | “có pcb đấy nha” | `concept.md` §2, `development-process.md` |
| DL-005 | 2026-09-17 | Kiểu truyền công suất | Không ổn áp; `Vload` bám theo `Vin` trừ sụt áp | Đã xác nhận | “Vload theo Vin (tùy cấu hình)” | `concept.md` §4, `product-requirements.md` §3 |
| DL-006 | 2026-09-17 | Input/output | Tách đầu vào/ra công suất và đầu vào/ra thông tin | Đã xác nhận | Người dùng tự tổng hợp hai cặp input/output rồi “tạm chốt” | `concept.md` §3, `product-requirements.md` §3 |
| DL-007 | 2026-09-17 | Đại lượng đo | Đo `Vin` phía nguồn, `Vload` phía tải, `Iload` trên đường truyền công suất | Đã xác nhận | Mô tả trực tiếp “giám sát U tại 2 phía... cùng với I trên toàn bộ đường công suất” | `concept.md` §5, FR-02 đến FR-05 |
| DL-008 | 2026-09-17 | Chẩn đoán ngắn mạch | Không kết luận chỉ từ `Vload`; phải xét `Vin`, `Vload`, `Iload` và switch | Đã xác nhận | Hỏi và xác nhận trường hợp ngắn mạch có thể làm cả `Vin` và `Vload` giảm | `concept.md` §5, UC-04, FR-05 |
| DL-009 | 2026-09-18 | Certification | Không có mục Certification trong Product Requirement hiện tại | Đã bác bỏ | “phần cert không cần làm” | `product-requirements.md` |

## 3. Baseline định lượng

| ID | Ngày | Chủ đề | Giá trị | Trạng thái | Bằng chứng từ người dùng | Yêu cầu liên quan |
| --- | --- | --- | --- | --- | --- | --- |
| DL-010 | 2026-09-17 | Miền điện áp | `5–15 V DC` | Đã xác nhận | “approve 5-15V” | PR-PERF-01 |
| DL-011 | 2026-09-17 | Dòng liên tục | `0–1 A` | Tạm thời | “tạm chốt 1A và 2A... nhưng mà là tạm thôi” | PR-PERF-02 |
| DL-012 | 2026-09-17 | Dòng đỉnh | Trên `1 A` đến `2 A` | Tạm thời | Cùng lời chốt ở DL-011 | PR-PERF-03 |
| DL-013 | 2026-09-17 | Thời gian dòng đỉnh | Không quá `1 s` trong miền vận hành được công bố | Tạm thời | “chốt lại thông số này trong performance” sau khi phân biệt rating và absolute maximum | PR-PERF-04 |
| DL-014 | 2026-09-17 | Sai số đo | `Vin`, `Vload`: `±0,1 V`; `Iload`: `±0,05 A` | Tạm thời | “ok sang thông số tiếp theo” sau bảng đề xuất | PR-PERF-05 đến PR-PERF-07 |
| DL-015 | 2026-09-17 | Cập nhật giao diện | `≤ 200 ms` | Tạm thời | “approve” | PR-PERF-08 |
| DL-016 | 2026-09-17 | Phản ứng bảo vệ | `≤ 100 µs` cho bảo vệ nhanh; `≤ 10 ms` sau khi hết thời gian cho phép | Tạm thời | “approve” | PR-PERF-09, PR-PERF-10 |
| DL-017 | 2026-09-17 | Sụt áp | `≤ 0,2 V` tại `1 A`; `≤ 0,4 V` tại `2 A` | Tạm thời | “ok sang thông số tiếp theo” sau khi làm rõ quan hệ với dòng | PR-PERF-11, PR-PERF-12 |
| DL-018 | 2026-09-17 | Chi phí | BOM `≤ 600.000 VNĐ`; prototype `≤ 1.000.000 VNĐ` | Tạm thời | “approve” | PR-COST-01, PR-COST-02 |
| DL-019 | 2026-09-17 | Kích thước/khối lượng | PCB `≤ 100 × 100 mm`; cao `≤ 30 mm`; khối lượng `≤ 150 g` | Tạm thời | “tạm chốt như thế đi nhé” | PR-PHY-01 đến PR-PHY-03 |
| DL-020 | 2026-09-17 | Công suất tự tiêu thụ | Mục tiêu `≤ 1 W` | Đề xuất chưa duyệt | Người dùng duyệt nguồn chung nhưng không xác nhận lại con số `1 W` | PR-PWR-04 |

## 4. Cấu hình, profile và nguồn nuôi

| ID | Ngày | Chủ đề | Quyết định | Trạng thái | Bằng chứng từ người dùng | Tài liệu bị ảnh hưởng |
| --- | --- | --- | --- | --- | --- | --- |
| DL-021 | 2026-09-17 | Cấu hình nguồn | Hỗ trợ nhiều `Vin` và `I_supply_max` trong miền sản phẩm; `I_supply_max` do người dùng cấu hình | Đã xác nhận | “hoạt động với nhiều mức Vin hoặc I_supply_max khác nhau nhưng phải nằm trong giới hạn nhất định” | `concept.md` §6, FR-06 |
| DL-022 | 2026-09-17 | Cơ chế profile | Firmware dùng profile do người dùng chọn; không tự nhận diện tải | Đã xác nhận | Đồng ý hướng hardware có miền chung và firmware dùng cấu hình tải | `concept.md` §8, FR-06 |
| DL-023 | 2026-09-17 | Ba nhóm tải | `RESISTIVE`, tải điện tử/RC và `FAN` | Tạm thời | “với 3 loại tải hiện tại...” | `concept.md` §8 |
| DL-024 | 2026-09-17 | `CUSTOM` | Profile do người dùng tự khai báo | Đề xuất chưa duyệt | Không có lời chốt riêng; người dùng chỉ trực tiếp gọi ba nhóm tải hiện tại | `concept.md` §8 |
| DL-025 | 2026-09-17 | Motor/servo | Không cam kết hỗ trợ chung; chỉ có thể là phép thử mở rộng nếu nằm trong miền phần cứng | Tạm thời | Đồng ý hướng cover một miền hữu hạn thay vì mọi tải | `concept.md` §8, §11 |
| DL-026 | 2026-09-17 | Nguồn nuôi nội bộ | Dùng chung `Vin`, không yêu cầu nguồn phụ ngoài trong vận hành bình thường | Đã xác nhận | “thế bây giờ sử dụng nguồn chung đi” rồi “approve” | `concept.md` §7, PR-PWR-01, PR-PWR-03 |
| DL-027 | 2026-09-17 | Fail-safe | Mất nguồn điều khiển hoặc MCU reset thì công tắc tải mặc định OFF | Đã xác nhận | Phê duyệt phần bảo vệ kiến trúc dùng nguồn chung | `concept.md` §7, PR-PWR-02 |
| DL-028 | 2026-09-17 | Hai cấp bảo vệ | Quá dòng nghiêm trọng có đường phần cứng nhanh; firmware xử lý profile và lỗi có thời gian | Đã xác nhận | Sơ đồ có `HW_Protection` và phê duyệt chức năng/performance hai cấp | `concept.md` §9, FR-09 |

## 5. Use case, kiểm thử và vấn đề mở

| ID | Ngày | Chủ đề | Quyết định | Trạng thái | Bằng chứng từ người dùng | Tài liệu bị ảnh hưởng |
| --- | --- | --- | --- | --- | --- | --- |
| DL-029 | 2026-09-17 | Bảy use case | UC-01 đến UC-07 được chấp nhận về phạm vi | Tạm thời | “tạm thời chốt các UC như vậy đi” | `product-requirements.md` §4 |
| DL-030 | 2026-09-17 | Sau khi trip | Giữ switch OFF, không chủ động cấp năng lượng lại; không đòi `Vload = 0` ngay | Đã xác nhận | Yêu cầu làm rõ “trạng thái an toàn” rồi chốt các UC | UC-05, FR-10 |
| DL-031 | 2026-09-17 | Recovery | Manual reset, auto-retry và số lần thử | TBD | Không chọn chính sách cụ thể | UC-06, FR-11 |
| DL-032 | 2026-09-17 | Tải RC | Phải xét dòng nạp tụ; điện dung tối đa và soft-start/current-limit chưa được chọn | TBD | Hỏi cách bảo đảm dòng RC dưới `2 A`, chưa chọn giải pháp | `concept.md` §8.1 |
| DL-033 | 2026-09-17 | Quạt runtime | Phân biệt startup, xung runtime và dòng cao kéo dài; thời gian chi tiết vẫn là TBD | Tạm thời | Hỏi riêng về xung dòng runtime rồi chuyển sang thông số tiếp theo | `concept.md` §8.2 |
| DL-034 | 2026-09-17 | Verification | Phải có PCB và không chỉ test bằng điện trở; cần tải đại diện RC/điện tử và quạt | Đã xác nhận | “test bằng trở không thì có bị đơn giản quá không?” và yêu cầu cover tải đơn giản | `product-requirements.md` §11 |
| DL-035 | 2026-09-17 | Thiết bị kiểm thử | Model tải, oscilloscope, thiết bị tham chiếu và khả năng tiếp cận lab | TBD | Không chốt thiết bị cụ thể | `product-requirements.md` §11, `project-plan.md` |
| DL-036 | 2026-09-17 | Proteus | Áp dụng flow mô phỏng Proteus theo môn học | Đã xác nhận | Cung cấp slide, yêu cầu căn theo môn và phê duyệt flow | `development-process.md` §4 |
| DL-037 | 2026-09-18 | Type-C | Chưa chọn làm đầu nối cuối cùng; PD/PPS không phải baseline V1 | TBD | Chỉ nói nguồn “có thể” qua Type-C | `concept.md` §10–11 |

## 6. Quy tắc cập nhật log

- Khi một mục đổi trạng thái, thêm một dòng mới hoặc ghi rõ ngày chuyển trạng thái; không sửa lịch sử theo cách làm mất nguồn gốc quyết định.
- Một tính toán hoặc kết quả thử nghiệm có thể chứng minh tính khả thi nhưng không tự thay đổi Product Requirement nếu người dùng chưa chấp nhận thay đổi.
- `concept.md` và `product-requirements.md` phải tham chiếu log này thay vì tự nâng trạng thái của một đề xuất.
