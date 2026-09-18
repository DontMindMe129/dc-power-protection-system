# Kế hoạch dự án

> Phiên bản: 0.1
>
> Trạng thái: bản khung của Giai đoạn 1; lịch, nhân sự và khả năng tiếp cận thiết bị còn TBD

## 1. Mục tiêu

Thiết kế, chế tạo và kiểm chứng một PCB giám sát và bảo vệ đường nguồn DC theo `concept.md` và `product-requirements.md`. Quá trình phải tuân theo flow môn Thiết kế Hệ thống Nhúng và lưu được bằng chứng từ yêu cầu đến kiểm thử.

## 2. Đầu ra dự kiến

1. Concept và Product Requirement có trạng thái truy vết được.
2. Design Specification và phân chia phần cứng/phần mềm.
3. Hardware, Software và Test Specification.
4. Tính toán, mô phỏng Proteus và thử nghiệm từng khối.
5. Schematic, PCB, BOM và file sản xuất.
6. Firmware phát triển theo từng bước.
7. PCB đã lắp ráp, bản ghi bring-up và kết quả verification.
8. Báo cáo, demo và bài thuyết trình.

## 3. Thành viên và vai trò

Số thành viên, tên và phân công chính thức chưa được cung cấp trong session hiện tại.

| Vai trò cần có | Trách nhiệm chính | Người phụ trách |
| --- | --- | --- |
| Quản lý yêu cầu/hệ thống | Giữ decision log, traceability và kiểm soát phạm vi | TBD |
| Thiết kế phần cứng | Tính toán, mô phỏng, schematic, PCB và bring-up | TBD |
| Phát triển firmware | Driver, đo lường, FSM bảo vệ, UI và chẩn đoán | TBD |
| Verification | Test Specification, fixture, thực thi và lưu bằng chứng | TBD |
| Báo cáo/demo | Tổng hợp báo cáo, slide và kịch bản trình diễn | TBD |

Một người có thể đảm nhiệm nhiều vai trò nếu dự án chỉ có một hoặc ít thành viên.

## 4. Milestone và điều kiện chuyển bước

Chưa có lịch học kỳ hoặc deadline chính thức, vì vậy kế hoạch hiện dùng milestone theo cổng kiểm soát thay vì ngày cụ thể.

| Milestone | Đầu ra | Điều kiện hoàn thành |
| --- | --- | --- |
| M0 — Bảo tồn phiên bản cũ | Nhánh rebuild và lịch sử có thể khôi phục | Đã hoàn thành |
| M1 — Xác định sản phẩm | Concept, Product Requirement, decision log và project plan | Các mục sai trạng thái đã sửa; vấn đề mở được liệt kê; người thực hiện giải thích được ranh giới và hành vi lỗi |
| M2 — Đặc tả thiết kế | Kiến trúc, sơ đồ khối, FSM cấp hệ thống và phân chia HW/SW | Mỗi khối có trách nhiệm, giao diện và yêu cầu nguồn rõ ràng |
| M3 — Đặc tả thành phần | Hardware, Software và Test Specification | Mỗi yêu cầu có phương pháp kiểm chứng và không có mâu thuẫn giao diện |
| M4 — Thiết kế/Prototype khối | Tính toán, mô phỏng và thử từng khối | Nguồn nội bộ, đo lường, switch và hard protection đạt tiêu chí khối |
| M5 — PCB release | Schematic, layout, BOM và manufacturing files | Hoàn thành checklist tại mục 8 |
| M6 — Bring-up/tích hợp | PCB lắp ráp và firmware tích hợp | Các khối hoạt động cùng nhau ở chế độ giới hạn an toàn |
| M7 — Verification/bàn giao | Kết quả thử, báo cáo và demo | Các yêu cầu có kết luận pass/fail/deviation |

Ngày bắt đầu, deadline môn học và thời lượng cho từng milestone là TBD.

## 5. Ngân sách

| Hạng mục | Baseline hiện tại | Trạng thái |
| --- | --- | --- |
| BOM một bo mạch | `≤ 600.000 VNĐ` | Tạm thời |
| Prototype gồm PCB và linh kiện | `≤ 1.000.000 VNĐ` | Tạm thời |
| Nguồn, tải và dụng cụ phòng thí nghiệm | Không tính vào giá thành sản phẩm | Đã xác nhận trong phạm vi tính chi phí |

Chi phí vận chuyển, số lượng PCB tối thiểu và linh kiện mua dư phải được theo dõi riêng khi có BOM.

## 6. Thiết bị và khả năng tiếp cận

| Nhu cầu | Mục đích | Trạng thái tiếp cận |
| --- | --- | --- |
| Nguồn DC điều chỉnh được và có giới hạn dòng | Bring-up, brownout và fault test có kiểm soát | TBD |
| Đồng hồ đo tham chiếu | Kiểm tra sai số `Vin`, `Vload`, `Iload` | TBD |
| Oscilloscope và probe phù hợp | Đo chuyển mạch, `100 µs`, `10 ms` và xung dòng | TBD |
| Điện trở công suất/tải điều khiển | Điểm tải lặp lại được | Giá trị và khả năng mua/mượn: TBD |
| Tải điện tử/RC có tụ đầu vào | Thử inrush | Model và fixture: TBD |
| Quạt DC đại diện | Thử startup và runtime | Model: TBD |
| Programmer/debugger | Nạp và debug firmware | TBD |
| Thiết bị hàn và kiểm tra PCB | Lắp ráp và bring-up | TBD |

Nếu không có thiết bị đủ băng thông, yêu cầu phản ứng `100 µs` không bị xóa nhưng chưa thể tuyên bố đã được kiểm chứng trực tiếp. Đây là rủi ro cần giải quyết trước M5.

## 7. Rủi ro chính

| Rủi ro | Ảnh hưởng | Hành động dự kiến |
| --- | --- | --- |
| Baseline dòng/nhiệt không khả thi | Phải đổi `1 A`, `2 A/1 s` hoặc kích thước PCB | Tính toán SOA/nhiệt và thử khối trước PCB release |
| Không có thiết bị đo đủ nhanh | Không chứng minh được `100 µs` | Xác nhận khả năng mượn lab hoặc điều chỉnh kế hoạch verification |
| Chưa chốt tải đại diện | Không xây dựng được profile và test case lặp lại | Chọn model RC/quạt trước Test Specification |
| Fault scope chưa phân loại | Kiến trúc bảo vệ dễ thiếu trường hợp | Phân loại từng fault trước Design Specification freeze |
| Linh kiện khó mua hoặc vượt ngân sách | Trễ PCB và thay đổi schematic | Kiểm tra tồn kho/giá trước BOM freeze |
| Yêu cầu hành chính học kỳ thay đổi | Báo cáo hoặc phân công không phù hợp | Xác nhận lại với giảng viên/tài liệu học kỳ hiện tại |
| Sửa requirement sau PCB release | Re-spin PCB | Dùng gate tại mục 8 và decision log |

## 8. Điều kiện để đặt PCB

Chỉ đặt PCB khi đáp ứng tối thiểu:

- Product Requirement và các thay đổi trạng thái đã được rà soát.
- Design, Hardware, Software và Test Specification thống nhất giao diện.
- Đã chốt MCU, nguồn nội bộ, cảm biến, switch và hard protection.
- Đã tính điện áp/dòng định mức, absolute maximum, SOA, tổn hao và nhiệt.
- Các khối rủi ro cao đã được mô phỏng hoặc thử thực tế.
- Schematic/ERC và PCB/DRC đã qua review.
- BOM nằm trong ngân sách hoặc có deviation được phê duyệt.
- Đã xác định thiết bị cần thiết cho bring-up và fault test.
- Có kế hoạch cấp nguồn lần đầu với giới hạn dòng an toàn.

## 9. Việc cần xác nhận với môn học

- Số thành viên và yêu cầu thể hiện phân công.
- Deadline, milestone chấm điểm và định dạng báo cáo của học kỳ hiện tại.
- Phiên bản Proteus và phạm vi bắt buộc phải mô phỏng.
- Giới hạn số lớp, kích thước hoặc quy trình chế tạo PCB nếu có.
- Thiết bị phòng thí nghiệm được phép sử dụng hoặc mượn.

Các slide đã đọc là nguồn tham khảo cho flow; yêu cầu hành chính phải được đối chiếu lại với thông báo hiện hành của giảng viên.
