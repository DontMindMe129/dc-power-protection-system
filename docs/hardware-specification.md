# Đặc tả phần cứng sơ bộ

> Bản sơ bộ v0.2, cập nhật ngày 2026-11-05. Đã bao phủ các khối của kiến trúc, interface và kiểm chứng tích hợp theo yêu cầu chức năng, chỉ tiêu số và cơ chế sơ bộ. Các phần bổ sung được xây dựng theo ủy quyền hoàn thiện HW Spec của nhóm; các phương án chưa được duyệt riêng được ghi rõ. Những thông số còn mở nằm tại mục 7. Các chỉ tiêu cần được tính toán và kiểm chứng khi thiết kế.

## 1. Phạm vi và ràng buộc chung

Tài liệu cụ thể hóa phần cứng từ [yêu cầu sản phẩm](product-requirements.md) và [kiến trúc hệ thống](design-specification.md). Các bảng yêu cầu được tách khỏi hướng thiết kế đã duyệt. Các mục còn mở được tổng hợp tại mục 7.

Mức độ chi tiết của spec gồm yêu cầu định lượng, yêu cầu chức năng và cơ chế sơ bộ. Việc cụ thể hóa model linh kiện, giá trị mạch, tham chiếu ADC, lọc/đệm, hiệu chuẩn và tính toán triển khai thuộc Hardware Design, nhằm đáp ứng các yêu cầu đã đặt.

Nhóm đặt ngân sách trước, rồi lựa chọn linh kiện và thiết kế mạch đáp ứng. Khi phát hiện vấn đề khả thi, việc điều chỉnh thông số đã chốt cần có căn cứ và xác nhận lại.

| Đại lượng | Vị trí hoặc mục tiêu | Nguồn |
| --- | --- | --- |
| `V_in` | Tại đầu nối nguồn vào, trước Input Protection; miền vận hành 5–15 V DC | Design Spec mục 2; PR-PERF-01 |
| `V_protected` | Sau Input Protection, trước điểm chia nhánh điều khiển và tải | Design Spec mục 2 |
| `V_load` | Tại đầu nối tải ra, sau Power Switch | Design Spec mục 2 |
| `I_load` | Dòng nhánh tải, tách khỏi dòng cấp nguồn điều khiển | Design Spec mục 3.3 |
| Miền dòng tải | 0–1 A liên tục; trên 1–2 A trong tối đa 1 s khi cấu hình cho phép | PR-PERF-02, PR-PERF-03 |
| Sụt áp toàn PCB khi ON | ≤0,2 V tại 1 A; ≤0,4 V tại 2 A trong miền ngắn hạn, đo giữa hai đầu nối | PR-PERF-12, PR-PERF-13 |
| Tự tiêu thụ | ≤2 W tại đầu nối nguồn, khi không nối tải, sau ổn định, ở cả đầu ra ON/OFF trong miền 5–15 V | PR-PWR-04 |
| Ngân sách dòng nguồn | Dành dòng cho nhánh điều khiển trước khi phân bổ phần còn lại cho tải; xét cả dòng ngắn hạn | Design Spec mục 3.2 |

Điện áp được đo so với Power Return chung. Miền đo chính xác, miền phát hiện lỗi và miền chịu sự cố được đặc tả riêng. Thời gian cho phép quá dòng phải phối hợp với độ trễ ngắt để giữ trong miền dòng ngắn hạn của sản phẩm.

Tải đại diện kế thừa Concept gồm RESISTIVE, ELECTRONIC và FAN; phạm vi tải cảm mạnh/trả năng lượng lớn theo Design Specification. Các ràng buộc chi phí, kích thước và lắp đặt kế thừa Product Requirements.

## 2. Input Protection

### Yêu cầu

- Tiếp nhận nguồn DC ngoài và cấp `V_protected` cho cả nhánh điều khiển lẫn nhánh tải; định mức đường chung phải xét tổng dòng hai nhánh.
- Bảo vệ ngược cực, cô lập dòng sự cố và hạn chế xung đầu vào trong miền sự cố được đặc tả.
- Ngắt nguồn chung khi quá áp đầu vào kéo dài, độc lập với MCU. Ngưỡng và thời gian phải phối hợp với khả năng chịu áp của phần phía sau.
- Kênh lấy mẫu `V_in` trước khối này cần bảo vệ riêng trong Sensing.

### Hướng thiết kế đã duyệt

| Hạng mục | Phương án và trạng thái |
| --- | --- |
| Đầu nối | Terminal block hai chân cọc vít, ký hiệu DC+ và DC−; đã xác nhận dạng đầu nối |
| Cô lập dòng sự cố | Cầu chì ống dùng một lần, có đế tháo được. Schematic hiện tại: F1 là cầu chì thủy tinh 5 × 20 mm **tác động nhanh F4A** đặt trên cặp kẹp PCB (chốt ngày 2026-11-24, vì các nguồn mua không có T3.15A 5 × 20). Dòng làm việc tối đa khoảng 2,1 A, tức khoảng 50% định mức. Dòng nạp tụ tải đã được mạch làm chậm tốc độ đóng giới hạn ở khoảng 1,5 A nên không cần loại tác động chậm. Cần kiểm tra khả năng cắt DC so với dòng sự cố của nguồn |
| Ngược cực | Ưu tiên khảo sát P-MOSFET high-side; phối hợp lại với controller ngắt quá áp |
| Hạn chế xung | Schematic hiện tại có hai TVS SMBJ15CA hai chiều. D19 đặt ở `V_IN_FUSED`, trước Q1/Q2, kẹp xung ở khoảng 24,4 V (dưới mức 30 V của AO4435, 36 V của LM393/LM339 và 28 V của TPS54202). D1 đặt ở `V_PROTECTED`, sau Q2 |
| Ngắt quá áp | Hướng đã duyệt: controller bảo vệ nguồn chuyên dụng kết hợp MOSFET ngoài. Schematic hiện tại triển khai bằng mạch rời: comparator U1A (LM393) điều khiển Q2, ngưỡng chỉnh bằng RV1. Ngưỡng OVP phải đặt trên 15 V và dưới 16,7 V (Vbr min của D1), để D1 không dẫn liên tục khi quá áp kéo dài. R5 10k + RV1 500 Ω giới hạn dải chỉnh danh định ở 15,5–16,2 V (trước đây R5 8,2k + RV1 5k cho dải 12,6–19,5 V). Đề xuất chỉnh khoảng 16,0 V; hysteresis khoảng 0,19 V. R3 (kéo gate Q2 lên khi quá áp) giảm từ 100k xuống 10k. Mô phỏng `V_in` nhảy 15 → 24 V khi đang chạy, nguồn có điện trở trong 0,2 Ω: D1 chỉ phải dẫn khoảng 78 µs và hấp thụ khoảng 18 mJ, so với 607 µs và 144 mJ khi R3 = 100k. Script nằm trong `.tmp/ovp-r3-2026-11-23/`. Khi chưa chỉnh, sai số linh kiện có thể làm ngưỡng lệch trong khoảng 14,8–16,9 V, nên phải chỉnh và đo trên từng board |

Model linh kiện, định mức cầu chì, miền sự cố, ngưỡng ngắt và phục hồi còn mở. LM74502 là ứng viên tham khảo. **Miền chịu quá áp kéo dài, chốt ngày 2026-11-23: không hư hỏng tới 16,7 V.** Mốc 24 V từng được đề xuất không được chọn.
- Từ 16,0 đến 16,7 V: OVP (Q2) ngắt nguồn chung và tự phục hồi khi điện áp về bình thường.
- Trên 16,7 V kéo dài, ví dụ cắm nhầm adapter 24 V: D19 dẫn liên tục và **là linh kiện hy sinh**. Kết quả phụ thuộc khả năng cấp dòng của nguồn so với đặc tính đứt của F1 (F4A tác động nhanh, IEC 60127-2):

| Loại nguồn | Diễn biến dự kiến |
| --- | --- |
| Nguồn giới hạn dòng dưới khoảng 2 lần định mức F1, tức khoảng 8 A (gồm phần lớn adapter 1–3 A) | F1 **không đứt** hoặc đứt rất chậm. D19 tiêu tán liên tục khoảng 20–60 W, vượt xa khả năng của SMBJ, nên sẽ quá nhiệt và hỏng: thường chập, đôi khi hở. |
| Nguồn cấp được dòng lớn (hàng chục A) | F4A tác động nhanh đứt trong khoảng vài ms đến vài chục ms ở dòng gấp khoảng 10 lần định mức, nhanh hơn loại T3.15A trước đây. Tuy vậy, năng lượng qua D19 vẫn có thể vượt định mức xung khoảng 600 W/1 ms, nên D19 có thể hỏng chập trước hoặc cùng lúc F1 đứt. |

  Trong mọi trường hợp, Q2 vẫn tắt nhờ OVP, và phần sau Q2 không nhận quá 30 V, nên mạch phía sau được bảo vệ. D19 hỏng chập thì đầu vào bị ngắn mạch, nguồn bị kéo sụt hoặc tự giới hạn dòng. D19 hỏng hở thì mất kẹp xung trước Q1/Q2, nhưng OVP vẫn hoạt động. **Không cam kết F1 sẽ đứt.** Sau sự cố phải kiểm tra và thay D19, kiểm tra F1. Chưa đánh giá rủi ro D19 quá nhiệt làm cháy PCB khi nó không hỏng hẳn chập hoặc hở. Muốn chịu được 24 V không hư hỏng thì phải xem lại miền sự cố (D19 Vbr cao hơn và Q1/Q2 ≥40 V).
- Điện áp kẹp và năng lượng xung lặp lại tối đa vẫn cần kiểm chứng.

Kiểm chứng dự kiến gồm tính toán dòng tổng, đặc tuyến phối hợp cầu chì/HW_OCP, thử ngược cực, quá áp và xung có kiểm soát theo miền sự cố sẽ chốt.

## 3. Internal Control Power Supply

### Yêu cầu

Đầu vào là `V_protected`. Khối tạo rail 5 V và từ rail này tạo rail 3,3 V, hoạt động cả khi Power Switch tải OFF. Thiết kế phải đáp ứng miền nguồn ngoài 5–15 V, có xét tổn hao trước đầu vào bộ nguồn, đặc biệt tại biên `V_in = 5 V`.

| Thông số đã chốt | Rail 5 V | Rail 3,3 V |
| --- | --- | --- |
| Ngân sách tiêu thụ liên tục tối đa | 300 mA tổng, gồm đầu vào bộ tạo 3,3 V | 200 mA cho các khối sử dụng rail |
| Dải điện áp sau ổn định, bao gồm ripple | 4,75–5,25 V | 3,201–3,399 V |
| Ripple tối đa | 50 mVpp | 20 mVpp |
| Điện áp đỉnh tối đa khi khởi động | 5,25 V | 3,399 V |

- Hai ngân sách dòng phải được đáp ứng đồng thời. Công suất phía 3,3 V và tổn hao bộ tạo rail này nằm trong công suất lấy từ rail 5 V.
- Thời gian khởi động toàn khối ≤100 ms, từ khi đầu vào hợp lệ và khối được cho phép hoạt động đến khi cả hai rail đạt và duy trì dải điện áp, ripple quy định.
- Khi đầu vào mất hoặc xuống dưới miền hợp lệ, hai rail được phép giảm theo năng lượng còn trong tụ và mức tiêu thụ. Khi đầu vào hợp lệ trở lại, nguồn tự khởi động lại theo giới hạn trên, kể cả khi tụ đầu ra còn điện áp dư.
- Bộ tạo 5 V đạt hiệu suất ≥85% tại dòng tổng 300 mA trên miền đầu vào được đặc tả. Tại 5,25 V và 300 mA, công suất đầu vào bộ tạo 5 V khoảng 1,853 W nếu hiệu suất là 85%; phần còn lại trong mục tiêu 2 W cần phối hợp với các phần phía trước.
- Ưu tiên IC tạo nguồn có bảo vệ giới hạn dòng/ngắn mạch và quá nhiệt tích hợp.

### Hướng triển khai và kiểm chứng

Schematic hiện tại tạo rail 5 V bằng buck TPS54202 (khoảng 4 V) nối tiếp boost MT3608 (5,0 V). Hiệu suất ghép hai tầng ước khoảng 79%, có thể không đạt mục tiêu ≥85% ở 300 mA; cần tính lại theo dòng thực tế hoặc xem lại chỉ tiêu. Chân 5V của Blue Pill lấy nguồn qua Schottky D20 (mục 5.11). Rail 3,3 V là LDO có sẵn trên module Blue Pill, cấp chung cho MCU, nút, OLED và latch HW_OCP. LM1117 riêng trước đây đã được bỏ để latch và MCU nằm cùng một miền nguồn (mục 5.11). Model LDO trên module, khả năng đáp ứng dải 3,201–3,399 V và ngân sách dòng phải được xác nhận trên module thực tế. Phân bổ dòng cho từng khối được hoàn thiện trong Hardware Design.

Kiểm chứng điện áp, ripple và công suất tại các mức sử dụng dòng trong ngân sách, gồm tải nhẹ, trên miền nguồn quy định. Đo ripple tại đầu ra từng bộ nguồn với băng thông oscilloscope 20 MHz và đường mass đầu đo ngắn. Thử khởi động, mất/cấp lại nguồn và điện áp dư; kiểm tra nhiệt và hiệu suất. Phép đo điện áp đỉnh được quy định riêng trong Test Specification.

## 4. Power Switch

### Yêu cầu

Khối đóng/ngắt đường dương nhánh tải sau Current Sensing; Power Return nối liền với hệ thống. Miền dòng vận hành kế thừa mục 1.

| Thông số đã chốt | Yêu cầu |
| --- | --- |
| Điện trở dẫn tổng khi ON | ≤50 mΩ cho toàn phần đường công suất thuộc khối, tại điện áp gate và nhiệt độ làm việc được đặc tả |
| Sụt áp tương ứng | ≤0,05 V tại 1 A; ≤0,10 V tại 2 A trong miền ngắn hạn |
| Thời gian ngắt riêng | ≤10 µs từ `SHUTDOWN_REQUEST` đạt mức kích hoạt tại đầu vào khối đến khi ngắt cấp điện từ nguồn sang tải; gồm logic, mạch gate và MOSFET |
| Dòng rò khi OFF | ≤100 µA theo từng chiều, sau khi quá độ kết thúc, trong miền điện áp và nhiệt độ được đặc tả |
| Interface điều khiển | `MCU_ENABLE` và `SHUTDOWN_REQUEST` dùng logic 3,3 V, active-HIGH |
| Ưu tiên | Chỉ ON khi nguồn điều khiển hợp lệ, enable = 1 và shutdown = 0; shutdown ưu tiên |
| Mặc định | OFF khi MCU reset, nguồn điều khiển mất/chưa hợp lệ hoặc thiếu enable hợp lệ; chân enable trở kháng cao phải được đưa về 0 |

Ngân sách 50 mΩ tương ứng 25% sụt áp toàn PCB tại hai điểm dòng quy định; phần còn lại cần phối hợp giữa các khối. Thời gian ≤10 µs dành tối đa 90 µs cho các khâu trước Power Switch trong ngân sách ngắt toàn đường ≤100 µs của PR-PERF-10.

Nhánh tải phải có cơ chế hạn chế xung và xử lý năng lượng khi ngắt, phù hợp tải được hỗ trợ, Power Switch và Output Sensing. Miền xung và năng lượng còn mở.

### Hướng thiết kế đã duyệt và kiểm chứng

Chọn hai P-MOSFET mắc back-to-back high-side để chặn hai chiều khi OFF. Tổng điện trở và thời gian ngắt áp dụng cho cả cụm. Khi ON, các kênh dẫn có thể truyền dòng hai chiều; khả năng bảo vệ dòng ngược khi ON còn mở.

Diode hồi dòng và/hoặc TVS là hướng khảo sát xử lý xung. Phương án TVS đơn hướng ở đầu ra mới được đề xuất.

**Giới hạn tốc độ đóng (dòng nạp tụ tải).** Nếu MOSFET đóng trong khoảng 1 µs, tụ C2 trên board và nguồn xả thẳng vào tụ đầu vào của tải. Mô phỏng ngspice cho dòng đỉnh 9–75 A, nên HW_OCP tác động khi bật với tụ tải từ khoảng 4,7 µF trở lên. Schematic hiện tại khắc phục bằng các thay đổi sau:
- Bật qua R25 220 kΩ. D14 (1N4148) vẫn được giữ làm diode kẹp B-E của Q6 (anode ở emitter, cathode ở base). Đường kéo gate xuống nhanh qua D14 được chặn bằng Schottky D21 (SS14) nối tiếp R47, chỉ cho dòng chạy từ Q6 sang gate.
  - **Lý do:** bản sửa trước có gỡ D14 khỏi mạch. Khi đó, lúc bật tải, base Q6 bị kéo về khoảng 0 V trong khi emitter vẫn ở gần V_in, nên V_E − V_B vượt giới hạn 6 V của 2N3904. Mô phỏng có mô hình đánh thủng B-E cho V_E − V_B khoảng 7,8–8,2 V ở 12–15 V. Mối B-E bị đánh thủng tạo đường kéo gate nhanh, khiến dòng nạp tụ 100 µF vọt lên 13,7 A (12 V) và 38,7 A (15 V), làm mạch làm chậm mất tác dụng.
  - **Sau khi thêm D14 và D21:** V_E − V_B ≤0,71 V; dòng nạp tụ 100 µF là 1,35 A (12 V) và 1,48 A (15 V). Script nằm trong `.tmp/q6-be-fix-2026-11-24/`.
- Thêm tụ Miller C19 10 nF (film) nối tiếp R62 1 kΩ từ gate tới `V_LOAD`. `V_LOAD` lên hết trong khoảng 3 ms ở 15 V và khoảng 7 ms ở 5 V.
- R24 tăng lên 2,2 MΩ, để Vgs khi ON ở `V_in` = 5 V còn khoảng −4,2 V.
- Đường ngắt đi Q6 → D21 → R47. R47 giảm còn 47 Ω để bù sụt áp của D21, giữ thời gian ngắt khoảng 5,4 µs ở 15 V (≤10 µs). Dòng đỉnh qua Q6 khoảng 113 mA, dưới 200 mA. Sau khi ngắt, Vgs còn khoảng −0,65 V, rồi R24 kéo dần về 0.

Kết quả mô phỏng với tải 1 A song song tụ:

| Tụ tải | 15 V | 12 V | 5 V |
| --- | --- | --- | --- |
| 100 µF | đỉnh khoảng 1,4–1,5 A, đạt | đạt | đạt |
| 220 µF | khoảng 2,0 A, sát ngưỡng HW_OCP thấp nhất 2,13 A | đạt | đạt |
| 470 µF | HW_OCP tác động | HW_OCP tác động | đạt |

Đề xuất ghi vào PR/Test Spec: **tụ đầu vào của tải được hỗ trợ ≤100 µF** khi có kèm 1 A dòng tải. Mức này chưa được duyệt. Mô phỏng dùng model AOD4185 gần đúng, chưa có ký sinh PCB và chưa mô phỏng LM393. Cần kiểm chứng trên mạch thật bằng tải RC theo Test Spec. Script và kết quả nằm trong `.tmp/inrush-sim-2026-11-23/`.

Kiểm chứng dự kiến gồm đo sụt áp/nhiệt, đo thời gian ngắt, thử quyền ưu tiên và mặc định OFF khi reset/mất nguồn. Đo dòng rò riêng từng chiều với điện áp hai phía và các đường cấp phụ được kiểm soát, gồm trường hợp nguồn mất nhưng tải còn điện áp. Thử xung sau khi xác định miền sự cố; điện áp dư trên tải được xét theo Design Spec.

## 5. Sensing, HW_OCP và giao diện MCU/UI

Các mục dưới đây kế thừa yêu cầu sản phẩm và kiến trúc. Yêu cầu chức năng, ngân sách và cơ chế sơ bộ nằm tại mục 5.1–5.16. Các thông số cần xác định khi chọn phần cứng được theo dõi tại mục 7.

| Khối | Yêu cầu chính | Nội dung cần cụ thể hóa trong thiết kế |
| --- | --- | --- |
| Voltage Sensing | Miền thông tin 0–20 V cho cả hai kênh theo mục 5.4; `V_in` sai số ≤±0,05 V trong 5–15 V; `V_load` ≤±0,05 V trong 0–15 V; `V_drop` ≤±0,1 V khi ON và các phép đo trong miền quy định | Bảo vệ đầu đo, tốc độ và interface; phối hợp tham chiếu/sai số theo PR-PERF-04, 05, 07 |
| Current Sensing | `I_load` sai số ≤±0,05 A trong 0–2 A; miền thông tin dòng ít nhất 0–3 A theo mục 5.1; độ trễ và sụt áp theo mục 5.2–5.3 | Miền chịu sự cố, dung sai quanh ngưỡng HW_OCP, điều kiện đo độ trễ và interface theo PR-PERF-06, 09, 10 |
| HW_OCP | Phát hiện quá dòng độc lập firmware, ngưỡng tác động thực tế >2 A và ≤2,5 A theo mục 5.9; giữ lỗi ưu tiên, phát shutdown và trạng thái, nhận clear; ngân sách trễ ≤70 µs theo mục 5.2 | Giá trị danh định, dung sai cụ thể, điều kiện đo độ trễ, hành vi cấp/mất nguồn; interface trạng thái/clear theo mục 5.10; MCU reset giữ nguyên khóa lỗi còn tồn tại |
| MCU, Buttons, Display | Giám sát và quản lý trạng thái theo Design Spec; cấu hình/điều khiển qua buttons, hiển thị chu kỳ ≤200 ms; yêu cầu phần cứng tại mục 5.13–5.16 | Model, chân/ngoại vi, kết nối, phân bổ dòng theo linh kiện; chức năng tham chiếu FR-01 đến FR-11 |

### 5.1. Miền thông tin Current Sensing đã chốt

- Miền biểu diễn thông tin dòng ít nhất 0–3 A theo chiều nguồn → tải.
- Trong 0–2 A, sai số toàn kênh ≤±0,05 A theo PR-PERF-06.
- Trong khoảng trên 2–3 A, thông tin phải đủ để HW_OCP phát hiện vượt ngưỡng được thiết kế, với ngưỡng tác động thực tế >2 A và ≤2,5 A. Độ chính xác tại vùng này cần phối hợp với khoảng ngưỡng và dung sai HW_OCP khi hoàn thiện đặc tả.
- Khi dòng vượt 3 A trong miền sự cố được đặc tả, đường bảo vệ vẫn phải nhận biết quá dòng và duy trì yêu cầu ngắt. Miền dòng sự cố phần cứng phải chịu còn TBD.
- Miền thông tin mở rộng phục vụ phát hiện lỗi; miền dòng vận hành của sản phẩm tiếp tục theo mục 1. Kiểm chứng dự kiến gồm đo sai số ở 0–2 A và thử phát hiện quá dòng quanh ngưỡng, trên ngưỡng và khi vượt miền biểu diễn, theo điều kiện sự cố sẽ chốt.

### 5.2. Ngân sách độ trễ đường bảo vệ nhanh đã chốt

| Khâu | Độ trễ tối đa |
| --- | --- |
| Current Sensing tới đầu vào HW_OCP | 20 µs |
| HW_OCP và logic trước Power Switch | 70 µs |
| Power Switch thực hiện ngắt | 10 µs |
| Toàn đường theo PR-PERF-10 | 100 µs |

Độ trễ Sensing tính từ khi dòng tải vượt ngưỡng đang xét đến khi Current Information tại đầu vào HW_OCP biểu diễn được điều kiện vượt ngưỡng đó. Độ trễ HW_OCP và logic tính tiếp đến khi SHUTDOWN_REQUEST tại đầu vào Power Switch đạt mức kích hoạt; thời gian Power Switch theo mục 4. Điều kiện kiểm chứng phải quy định tốc độ tăng dòng, mức vượt ngưỡng và các mốc tín hiệu tương ứng.

Đây là phân bổ mục tiêu cần được tính toán và kiểm chứng trên toàn đường. Timing của đường đo phục vụ MCU được đặc tả riêng.

### 5.3. Ngân sách sụt áp Current Sensing đã chốt

| Dòng tải | Sụt áp tối đa do Current Sensing |
| --- | --- |
| 1 A liên tục | 25 mV |
| 2 A trong miền ngắn hạn | 50 mV |

Ngân sách tương ứng điện trở dẫn tổng ≤25 mΩ của phần đường công suất thuộc Current Sensing, chiếm 12,5% ngân sách sụt áp toàn PCB. Kết hợp với Power Switch đã được phân bổ 25%, phần còn lại dành cho Input Protection, đầu nối và đường dẫn là 62,5%.

Cơ chế đo theo mục 5.5. Giá trị và model linh kiện được lựa chọn để đáp ứng đồng thời sụt áp, sai số và độ trễ đã chốt. Kiểm chứng dự kiến gồm đo sụt áp toàn khối tại hai điểm dòng trên theo điều kiện điện/nhiệt được đặc tả, sau đó đối chiếu tổng sụt áp toàn PCB.

### 5.4. Miền thông tin Voltage Sensing đã chốt

- Hai kênh `V_in` và `V_load` có miền biểu diễn thông tin 0–20 V khi nguồn điều khiển còn hợp lệ.
- Sai số toàn kênh ≤±0,05 V áp dụng cho `V_in` trong 5–15 V và `V_load` trong 0–15 V theo Product Requirements. Độ chính xác ngoài các miền này cần phối hợp với yêu cầu nhận biết lỗi khi hoàn thiện đặc tả.
- Phần dưới 5 V của `V_in` phục vụ nhận biết thấp áp khi hệ thống còn hoạt động; phần trên 15 V phục vụ nhận biết quá áp. Kênh `V_load` được quan sát cả khi ON và OFF.
- Miền điện áp sự cố đầu đo phải chịu, gồm tình huống MCU mất nguồn, còn phải được xác định riêng. Điện áp âm được xét trong yêu cầu bảo vệ đầu đo. Giới hạn vận hành nguồn ngoài tiếp tục theo mục 1.
- Kiểm chứng dự kiến gồm đo sai số trong các miền quy định, thử khả năng biểu diễn ở hai đầu miền 0–20 V và thử bảo vệ đầu đo theo miền sự cố sẽ chốt.

### 5.5. Hướng triển khai Current Sensing đã duyệt

Chọn điện trở shunt nối tiếp nhánh tải kết hợp IC khuếch đại đo dòng chuyên dụng (current-sense amplifier). Dòng tải tạo điện áp chênh lệch trên shunt; IC khuếch đại thành Current Information dạng analog, phân phối tới ADC phục vụ MCU và đầu vào phát hiện quá dòng của HW_OCP.

Giá trị/dung sai shunt, hệ số khuếch đại, model IC, ADC và mạch phụ được lựa chọn trong Hardware Design. Việc phân phối tín hiệu phải đáp ứng sai số, miền thông tin và độ trễ đã chốt. Độ trễ đường HW_OCP theo mục 5.2; các đặc tính interface analog và đường đo phục vụ MCU còn phải được đặc tả.

### 5.6. Hướng triển khai Voltage Sensing và ADC đã duyệt

Mỗi kênh điện áp dùng mạng chia áp điện trở và mạch bảo vệ đầu đo, tạo tín hiệu analog đưa tới ADC phục vụ MCU. Voltage Sensing thực hiện hạ mức và bảo vệ tín hiệu; ADC tích hợp thuộc khối MCU. Ưu tiên tận dụng ADC trong MCU cho các kênh điện áp và dòng, có kiểm tra sai số, tốc độ và số kênh khi chọn MCU.

Mạch bảo vệ phải giữ điện áp/dòng vào chân ADC và các phần xử lý tín hiệu liên quan trong giới hạn cho phép theo miền sự cố được đặc tả, gồm quá áp, xung, điện áp âm và đầu đo có điện khi MCU mất nguồn. Kênh V_in lấy trước Input Protection được bảo vệ riêng theo kiến trúc.

Giá trị/dung sai điện trở, model bảo vệ, lọc/đệm nếu cần và lựa chọn ADC được hoàn thiện trong Hardware Design. Trở kháng nguồn tín hiệu, thời gian lấy mẫu, tham chiếu ADC và cách kiểm soát sai số phải được phối hợp để đáp ứng sai số toàn kênh. Các đặc tính interface analog và timing phục vụ MCU còn phải được đặc tả.

### 5.7. Chu kỳ cập nhật dữ liệu đo phục vụ MCU đã chốt

- Mỗi ≤1 ms, MCU có một bộ giá trị đo mới gồm V_in, V_load và I_load; tương ứng mỗi đại lượng được cập nhật ít nhất 1.000 lần/giây.
- Độ trễ của mạch đo, ADC và lọc dữ liệu phải phối hợp với xử lý/điều khiển để đáp ứng thời gian ngắt phần mềm toàn đường ≤10 ms theo PR-PERF-11.
- Thời điểm lấy mẫu V_in và V_load phải được phối hợp để tính V_drop phù hợp khi điện áp biến động. Giới hạn lệch thời điểm lấy mẫu còn phải được cụ thể hóa.
- Đường thông tin dòng phục vụ HW_OCP tuân theo mục 5.2. Chu kỳ hiển thị tiếp tục theo PR-PERF-08. Cách lấy mẫu, xử lý và kiểm chứng được triển khai trong Hardware/Software/Test Specification tương ứng.

### 5.8. Ngân sách sai số đo điện áp đã chốt

Ngân sách áp dụng riêng cho từng kênh, quy về điện áp thực tại đầu nối, trong miền đo chính xác của PR-PERF-04 và PR-PERF-05.

| Nhóm | Thành phần sai số | Giới hạn |
| --- | --- | --- |
| Voltage Sensing | Chia áp, bảo vệ đầu đo, lọc/đệm và nhiễu phía tín hiệu | ±25 mV |
| MCU và ADC | Tham chiếu, chuyển đổi, lượng tử hóa và xử lý quy đổi | ±25 mV |
| Toàn kênh | Tổng các giới hạn sai số tuyệt đối | ±50 mV |

Đây là phân bổ thiết kế đã được xác nhận, cần kiểm chứng khi triển khai. Các tham số và phương án cụ thể để đáp ứng từng nhóm được hoàn thiện trong Hardware Design. Nếu cần phân bổ lại, phải có căn cứ và xác nhận, đồng thời đáp ứng giới hạn toàn kênh.

### 5.9. Hướng triển khai HW_OCP đã duyệt

Dùng comparator so sánh Current Information dạng analog với mức tham chiếu, kết hợp mạch giữ lỗi ưu tiên lỗi (set-dominant latch). Khi lỗi được giữ, khối phát SHUTDOWN_REQUEST và trạng thái lỗi cho MCU; yêu cầu clear của MCU được xử lý theo quyền ưu tiên lỗi trong Design Specification. Các chức năng có thể triển khai rời hoặc tích hợp, với model và giá trị mạch được chọn trong Hardware Design.

Ngưỡng tác động thực tế của HW_OCP phải >2 A và ≤2,5 A theo PR-PERF-09. Khoảng này áp dụng cho toàn đường xác lập ngưỡng, có xét sai số Current Sensing, tham chiếu và comparator. Giá trị danh định và mức dung sai cụ thể được chọn trong Hardware Design để đáp ứng cả hai biên. Đề xuất ±5% chưa được duyệt.

Schematic hiện tại: ngưỡng danh định khoảng 2,25 A, worst-case 2,13–2,39 A. Để tránh ngắt nhầm do nhiễu, hai đầu vào của U1B được lọc:
- C20 470 pF trên `CURRENT_INFORMATION` (nguồn khoảng 6 kΩ, tần số cắt khoảng 56 kHz). Nhiễu của buck 500 kHz giảm khoảng 9 lần, của boost 1,2 MHz khoảng 21 lần. Xung khi ADC lấy mẫu qua R37 giảm từ khoảng 890 mV xuống khoảng 33 mV, trong khi biên tới ngưỡng khi đang chạy 2 A là khoảng 150 mV.
- C21 100 nF trên `OCP_THRESHOLD`.

Đổi lại, thời gian từ khi dòng vượt ngưỡng tới khi tín hiệu ở đầu vào HW_OCP vượt ngưỡng tăng lên khoảng 10,7 µs khi dòng nhảy từ 1 A lên 2,3 A, và khoảng 3 µs khi ngắn mạch. Cả hai vẫn trong ngân sách 20 µs của mục 5.2. Mô hình tính: INA180A3 150 kHz, slew 2 V/µs. Hai tụ phải đặt sát chân U1.

### 5.10. Interface trạng thái và xóa lỗi HW_OCP đã duyệt

Hai tín hiệu dùng logic 3,3 V, tích cực mức HIGH.

| Tín hiệu | Chiều | Ý nghĩa mức HIGH |
| --- | --- | --- |
| `OVERCURRENT_FAULT_STATUS` | HW_OCP → MCU | Có lỗi quá dòng đang được giữ |
| `CLEAR_FAULT_REQUEST` | MCU → HW_OCP | Yêu cầu xóa lỗi trong quá trình phục hồi |

`CLEAR_FAULT_REQUEST` phải mặc định LOW khi MCU reset hoặc chân điều khiển ở Hi-Z. Lỗi có ưu tiên cao hơn yêu cầu clear. Xóa lỗi thành công vẫn giữ đầu ra OFF, chỉ cho phép bật lại theo quy trình phục hồi và lệnh ON riêng trong Design Specification. Các mức điện áp logic chi tiết và cách triển khai được xác định trong Hardware Design.

Trong schematic hiện tại, latch HW_OCP (U7) và MCU dùng chung rail 3,3 V của Blue Pill nên hai tín hiệu nối trực tiếp: PB13 → U7 với R21 47 kΩ kéo LOW khi MCU reset hoặc Hi-Z, và U7 → PB12. Không còn buffer giữa hai miền nguồn.

### 5.11. HW_OCP khi cấp nguồn, reset và mất nguồn

- Khi nguồn nuôi hoặc Current Information chưa hợp lệ, Power Switch phải giữ OFF. Việc cho phép ON chỉ được xét sau khi đường bảo vệ sẵn sàng. Đây là hành vi đã được nhóm xác nhận.
- Nếu phát hiện quá dòng ngay khi khởi động, HW_OCP phải giữ lỗi và phát yêu cầu ngắt như khi vận hành.
- MCU reset không được tự xóa lỗi phần cứng còn được giữ. Khi khởi động lại, MCU đọc trạng thái và thực hiện Startup Checks theo Design Specification.
- Khi mất nguồn điều khiển, Power Switch phải OFF theo mục 4. Việc giữ lỗi qua mất nguồn hoàn toàn chưa được yêu cầu; sau cấp lại nguồn vẫn cần khởi tạo, kiểm tra và lệnh ON riêng.

Không dùng trạng thái báo lỗi LOW riêng lẻ làm bằng chứng đường bảo vệ đã sẵn sàng.

#### Cơ chế đã chọn: gộp miền nguồn latch và giám sát nguồn POWER_GOOD

Quy tắc chung: **tải OFF khi có quá dòng hoặc khi nguồn nuôi của đường phát hiện/thực thi ngắt không hợp lệ; đã OFF thì giữ OFF cho tới khi có lệnh ON mới. Nguồn tự hồi phục không được tính là cho phép bật lại.**

| Việc | Đảm nhận |
| --- | --- |
| Phát hiện quá dòng, ngắt ≤100 µs, giữ lỗi qua MCU reset | HW_OCP: U1B và latch U7 |
| Phát hiện nguồn của đường bảo vệ bất thường, cắt tải ngay, không chờ firmware | Khối giám sát nguồn: U10 (LM339) tạo `POWER_GOOD`, Q9 nối tiếp emitter Q5 |
| Giữ OFF sau sự cố nguồn, chờ lệnh ON mới | Firmware, nhận sự kiện qua `POWER_GOOD_MCU` (PB14, EXTI) |
| Phát hiện mất nguồn của chính latch | Gộp miền: U7 dùng chung rail 3,3 V với MCU; mất rail thì `MCU_ENABLE` cũng mất. Cờ POR của STM32 **không** chứng minh latch đã mất trạng thái, vì latch CMOS có thể giữ lỗi dưới ngưỡng PDR. Firmware phân biệt lỗi thật với trạng thái ngẫu nhiên lúc cấp nguồn bằng bản ghi HW_OCP lưu trong Flash (Software Spec mục 6.2, 7). Phần cứng không cần sửa: PB12 đã nối được ngắt EXTI |

**Các kênh giám sát.** U10 cấp từ `V_IN_REV_PROTECTED`, so với tham chiếu `CMP_REF_1V25` (TL431/2 có sẵn). Ngõ ra open-collector nối chung thành `POWER_GOOD`: chỉ cần một kênh báo hỏng là `POWER_GOOD` xuống LOW. Ngưỡng tính với TL431A 2,470–2,520 V, điện trở ±1% và offset LM339 ±5 mV:

| Kênh | Theo dõi | Linh kiện | Ngưỡng danh định xuống / lên | Worst-case |
| --- | --- | --- | --- | --- |
| VIN_UV (U10A) | `V_IN_REV_PROTECTED`: điều kiện cho TL431 ≥1 mA và comparator | R53 47k / R54 20k, R55 1 MΩ | 4,14 / 4,24 V | xuống 3,98–4,30 V; lên 4,08–4,40 V |
| 5V_UV (U10B) | `V_CTRL_5V`: nguồn INA180 và LDO Blue Pill (qua D20) | R56 47k / R57 20k, R58 2,2 MΩ | 4,15 / 4,21 V (V_in 5 V); 4,10 / 4,21 V (V_in 15 V) | xuống 3,94–4,31 V; lên 4,05–4,37 V |
| OVP mirror (U10D) | Cùng nút chia OVP `OVP_SENSE` và cùng ngưỡng với U1A, đảo cực | Không thêm | Theo RV1 | Theo OVP |

Ràng buộc kèm theo: với R9 = 1 kΩ, TL431 còn ≥1 mA khi `V_IN_REV_PROTECTED` ≥3,68 V, thấp hơn ngưỡng xuống nhỏ nhất của VIN_UV. Ngưỡng lên lớn nhất của VIN_UV thấp hơn Vin sau sụt áp ở 5 V/2 A. Ngưỡng lên lớn nhất của 5V_UV (4,37 V) thấp hơn mức 4,75 V tối thiểu của rail khoảng 0,4 V. R57 tăng từ 18k lên 20k vì biên cũ chỉ khoảng 0,06 V. Ngưỡng xuống nhỏ nhất (≈4,0 V) vẫn đủ cho INA180 (≥2,7 V), còn 74HC00 và STM32 chạy được ở rail 3,3 V suy giảm (≥2,0 V). Dưới 4,75 V, sai số đo ADC không còn được bảo đảm; firmware cần bù theo VREFINT hoặc đánh dấu số đo không hợp lệ. U10C không dùng: ngõ vào cố định, ngõ ra để hở.

**Lọc nhiễu đầu vào comparator.** C22–C25 là tụ 10 nF, đặt lần lượt ở `CMP_REF_1V25`, `OVP_SENSE`, `VIN_UV_SENSE` và `V5_UV_SENSE`, sát chân U10 và U1. Mục đích là bỏ qua các xung cỡ µs không làm nguồn điều khiển mất ổn định (PR-PWR-02):
- Nhiễu 500 kHz giảm khoảng 300–440 lần.
- Xung +0,5 V/5 µs ở V_in 15 V chỉ làm `OVP_SENSE` tăng khoảng 2 mV, so với biên 79 mV.
- Sụt −0,8 V/20 µs ở V_in 5 V chỉ làm `VIN_UV_SENSE` giảm khoảng 32 mV, so với biên 263 mV. Khi chưa có tụ, mức giảm là 239 mV.

Đổi lại, OVP phát hiện chậm hơn: khoảng 9 µs khi nhảy 15 → 24 V, khoảng 52 µs khi nhảy 15 → 17 V. Các kênh UV chậm khoảng 0,11 ms khi nhảy 5 → 3,5 V. Tham chiếu `CMP_REF_1V25` ổn định sau khoảng 0,55 ms kể từ khi cấp nguồn, nhanh hơn thời gian khởi động của buck/boost; trong khoảng đó U1A giữ Q2 tắt và `POWER_GOOD` ở mức thấp. Tụ không mắc trực tiếp vào cathode TL431 vì có R7 22k ngăn giữa.

**Hỏng thì ra OFF.** `POWER_GOOD` được kéo lên qua R59 47 kΩ **từ chính `V_IN_REV_PROTECTED`**, kéo xuống qua R63 47 kΩ, và được D17 (1N4733A) kẹp ≤5,1 V. Do đó, khi U10 không kéo xuống thì `POWER_GOOD` ≈ V_in/2 (khoảng 2,5 V ở V_in 5 V, kẹp ở 5,1 V khi V_in trên khoảng 10 V).

R63 được thêm để vá một lỗ hổng: trước đây, khi V_in sụt rất ngắn xuống khoảng 1,2–2 V, U10 ngừng hoạt động và `POWER_GOOD` lơ lửng ở mức V_in, có thể không xuống dưới VIL của PB14 (khoảng 1,16 V). Trong khi đó MCU vẫn sống nhờ tụ trên rail (khoảng 0,4 ms), nên không nhận được sự kiện, và tải tự có điện lại khi nguồn hồi. Với R63, mọi trường hợp V_in dưới khoảng 2,3 V đều làm PB14 thấy mức LOW. Từ 2 V đến ngưỡng VIN_UV thì U10 tự kéo xuống 0. Vì mức HIGH của `POWER_GOOD` thấp hơn, R55 giảm từ 2,2M xuống 1M và R58 từ 4,7M xuống 2,2M để giữ hysteresis: VIN_UV khoảng 98 mV, 5V_UV khoảng 53 mV ở V_in 5 V. Q9 (AO3400A) nằm nối tiếp emitter của Q5, nên Q5 chỉ dẫn khi `POWER_GOOD` HIGH. Khi Vin nằm giữa khoảng 2 V và ngưỡng VIN_UV, LM339 còn chạy và VIN_UV báo hỏng. Tỉ số chia 0,30 vẫn nhỏ hơn 0,5, nên điều này đúng cả khi TL431 chưa ổn áp. Dưới khoảng 2 V comparator ngừng chạy, nhưng mức kéo lên khi đó cũng không vượt Vin, và đường công suất cùng mạch lái gate Q3/Q4 cũng chỉ còn dưới mức đó. Vin mất đột ngột trong khi tụ còn giữ MCU thì `POWER_GOOD` theo Vin về 0.

**Timing.** Tắt: D18 xả C17, cỡ µs. Nhả: R60 47 kΩ nạp C17 22 nF. Vì Vgs(th) của AO3400A có thể thấp tới 0,65 V, Q9 có thể bắt đầu dẫn lại chỉ sau **khoảng 0,14 ms** (V_in 15 V) đến 0,31 ms (V_in 5 V) kể từ khi `POWER_GOOD` lên lại. Con số 0,8 ms ghi trước đây là thời điểm gate đạt 2,5 V, không phải lúc Q9 bắt đầu dẫn. Firmware phải hạ `MCU_ENABLE` trong ngắt EXTI của PB14 (mất vài µs) để tải không tự có điện lại. Việc giữ OFF vẫn phụ thuộc firmware và watchdog; muốn hoàn toàn độc lập với firmware thì phải thêm latch phần cứng cho lỗi nguồn. Đường SHUTDOWN_REQUEST → Q7 → Q5 không đi qua Q9, ngân sách 10/100 µs không đổi.

**Giới hạn được chấp nhận.**
- Mất nguồn đến mức rail 3,3 V sụp thì latch có thể mất thông tin "đã có quá dòng". Thông tin này được giữ bằng bản ghi Flash của firmware. Tải vẫn OFF và cần phục hồi cùng lệnh ON mới.
- Vùng VDD khoảng 1,8–2,0 V nằm giữa ngưỡng POR/PDR của STM32F103 và mức 2,0 V tối thiểu của 74HC00.
- Chân 5V của Blue Pill lấy nguồn từ `V_CTRL_5V` qua Schottky D20 (SS14), nên USB VBUS không cấp ngược vào `V_CTRL_5V`. Khi chỉ cắm USB, MCU vẫn chạy nhưng `V_CTRL_5V` = 0 và `POWER_GOOD` LOW, nên tải luôn OFF. Riêng chân 3,3 V của mạch nạp SWD vẫn có thể cấp ngược vào rail 3,3 V (mục 7).

Kiểm chứng ưu tiên: (1) OCP đang giữ lỗi, sau đó rail 3,3 V sụt ngắn rồi hồi; (2) cấp nguồn lạnh với Vin lên nhanh và chậm; (3) Vin, 5 V sụt khi tải đang ON; (4) OVP ngắn khi các rail không sụt; (5) MCU reset khi đang giữ lỗi. Đo đồng thời `POWER_GOOD`, gate Q9, `MCU_ENABLE`, `SHUTDOWN_REQUEST` và `V_LOAD`.

Kiểm chứng dự kiến gồm cấp nguồn với tốc độ tăng khác nhau, reset MCU khi đang có lỗi giữ, mất/cấp lại nguồn và thử điều kiện quá dòng ngay lúc khởi động. Theo dõi đồng thời enable, shutdown, trạng thái lỗi và đường công suất.

### 5.12. Interface analog giữa Sensing, HW_OCP và MCU

| Interface | Yêu cầu phần cứng |
| --- | --- |
| `V_IN_INFORMATION`, `V_LOAD_INFORMATION` | Hai ngõ analog tới ADC phục vụ MCU, tương ứng miền thông tin 0–20 V; đáp ứng sai số và timing tại mục 5.4, 5.7 và 5.8 |
| `CURRENT_INFORMATION` | Ngõ analog chung từ Current Sensing tới ADC và HW_OCP; đáp ứng miền thông tin mục 5.1, sai số dòng và độ trễ đường bảo vệ |
| Mức điện áp analog | Phải phù hợp miền đầu vào hữu dụng của ADC và comparator, có dự phòng sai số/nguồn; khoảng điện áp tín hiệu và hệ số quy đổi cụ thể TBD trong Hardware Design |
| Ghép nối | Tải đầu vào, lọc/đệm và phân nhánh tín hiệu phải được tính trong ngân sách sai số và độ trễ; hoạt động lấy mẫu ADC không được làm mất khả năng bảo vệ độc lập |
| Khi một khối chưa có nguồn | Tín hiệu còn điện không được gây hư hỏng hoặc cấp nguồn ngược ngoài giới hạn cho phép của các khối nhận |

Không yêu cầu một bộ ADC riêng cho mỗi kênh. Phương án ADC được lựa chọn để đáp ứng đồng thời tốc độ, sai số và phối hợp thời điểm lấy mẫu. Sai số toàn kênh dòng ±0,05 A phải bao gồm shunt, khuếch đại, ADC, tham chiếu và xử lý quy đổi; phân bổ cụ thể còn TBD. Mạch giới hạn đầu đo phải được đánh giá cả ảnh hưởng của dòng rò và bão hòa tới phép đo và bảo vệ.

### 5.13. MCU và các interface hỗ trợ

| Nhóm | Yêu cầu |
| --- | --- |
| Thu thập dữ liệu | Hỗ trợ ít nhất ba kênh analog chức năng cho V_in, V_load và I_load; có bộ dữ liệu mới mỗi ≤1 ms theo mục 5.7 |
| Độ chính xác ADC | Đáp ứng ngân sách MCU/ADC ±25 mV quy về đầu nối của từng kênh điện áp và sai số toàn kênh dòng ±0,05 A; độ phân giải, tham chiếu và thời gian lấy mẫu được chọn bằng tính toán |
| Xử lý và timing | Có tài nguyên xử lý, timer và bộ nhớ để thực hiện giám sát, cấu hình, trạng thái và UI, đồng thời đáp ứng ngắt phần mềm toàn đường ≤10 ms và cập nhật hiển thị ≤200 ms |
| Điều khiển và trạng thái | Có ngõ ra MCU_ENABLE, CLEAR_FAULT_REQUEST và ngõ vào OVERCURRENT_FAULT_STATUS, tương thích logic 3,3 V tại mục 4 và 5.10; ngõ vào `POWER_GOOD_MCU` (PB14, chân chịu 5 V, qua R61 47 kΩ) có ngắt cạnh xuống theo mục 5.11 |
| Reset | Trong reset, khởi tạo và khi chưa có nguồn hợp lệ, enable và clear phải được phần cứng đưa về trạng thái không hoạt động |
| Nạp và debug | Có đầu nối hoặc điểm tiếp cận để nạp/debug MCU. Nếu dùng MCU STM32, ưu tiên SWD; chuẩn cuối cùng theo MCU được chọn |
| UART | Có khả năng phục vụ debug/kiểm thử/logging theo Design Specification; vận hành thông thường không phụ thuộc kết nối UART |
| Nguồn | Miền nguồn và mức I/O phải tương thích các rail nội bộ; mọi dòng tiêu thụ của MCU/module và ngoại vi tích hợp phải nằm trong ngân sách mục 3 |

Cơ chế sơ bộ là MCU có ADC, timer, GPIO và giao tiếp màn hình phù hợp. Với một ADC ghép kênh, phải kiểm tra thời gian chuyển kênh, thời gian xác lập và lệch thời điểm đo V_in/V_load. Tần số CPU, Flash/RAM, số chân, module hay MCU rời được lựa chọn trong Hardware Design theo nhu cầu thực tế. Giới hạn điện áp rail 3,3 V riêng lẻ chưa bảo đảm độ chính xác tham chiếu ADC.

Khi kết nối mạch nạp, UART hoặc nguồn USB của module, thiết kế phải kiểm soát đường cấp nguồn phụ và tương thích mức điện áp. Các kết nối này phải cho phép kiểm chứng mặc định OFF và dòng rò theo đúng điều kiện thử.

### 5.14. Buttons

- Cung cấp thao tác ON, OFF, yêu cầu phục hồi, chọn/chỉnh và xác nhận cấu hình theo Design Specification. Số nút và cách gán thao tác được xác định cùng Software Specification.
- Tín hiệu nút phải có trạng thái nghỉ xác định, tương thích mức I/O của MCU và ổn định khi cấp nguồn/reset.
- Cơ chế sơ bộ là nút nhấn cơ kết hợp điện trở định mức nghỉ và xử lý dội phím. Phân chia chống dội bằng mạch/phần mềm, cực tính và giá trị linh kiện được xác định khi thiết kế.
- Phần cứng phải cho phép phân biệt các thao tác được giao cho từng nút; một lần nhấn không được tạo nhiều lệnh do dội phím sau khi đã phối hợp với firmware.

Kiểm chứng bằng thử nhấn/giữ/thả, cấp nguồn khi đang giữ nút và kiểm tra phối hợp firmware. Lệnh phục hồi và lệnh ON vẫn là hai thao tác chức năng riêng theo chính sách đã xác nhận.

### 5.15. Display

- Có khả năng trình bày V_in, V_load, I_load, trạng thái hệ thống, cảnh báo/nguyên nhân ngắt và các tham số đang chỉnh theo Design Specification và Product Requirements.
- Giao tiếp và khả năng xử lý hiển thị phải hỗ trợ chu kỳ cập nhật ≤200 ms, đồng thời duy trì các yêu cầu giám sát và ngắt. Giá trị hiển thị phải dùng cùng định nghĩa đại lượng và đơn vị với phép đo.
- Cơ chế sơ bộ là màn hình số kết nối MCU. Loại OLED/LCD, kích thước, độ phân giải và giao tiếp I²C/SPI hoặc loại phù hợp khác còn TBD.
- Nguồn, mức logic, dòng tiêu thụ kể cả đèn nền và dòng khởi động phải được đưa vào ngân sách nguồn. Nếu module dùng nguồn 5 V, cần kiểm tra riêng mức giao tiếp với MCU; phương án chuyển mức được xác định khi cần.

Kiểm chứng bằng hiển thị các trạng thái và tham số cần thiết, đo chu kỳ cập nhật trong khi thực hiện giám sát/bảo vệ và kiểm tra tiêu thụ. Bố cục màn hình, menu và driver thuộc Software Specification và triển khai firmware.

### 5.16. Phân phối nguồn và yêu cầu triển khai PCB

| Nhóm khối | Ràng buộc phân phối |
| --- | --- |
| MCU và logic điều khiển/bảo vệ | Ưu tiên rail 3,3 V cho miền logic thống nhất; nguồn của IC cụ thể theo Hardware Design, có kiểm tra thứ tự cấp nguồn và mặc định an toàn |
| Sensing và tham chiếu | Chọn rail phù hợp miền common-mode, biên độ tín hiệu và sai số; hoạt động đúng trong dải rail mục 3 và trên miền V_in quy định |
| Display và Buttons | Theo model được chọn, tương thích I/O MCU và được tính trong ngân sách rail |
| Tổng dòng | Rail 3,3 V ≤200 mA; rail 5 V ≤300 mA, bao gồm dòng đầu vào bộ tạo 3,3 V; đồng thời đáp ứng ≤2 W theo điều kiện PR-PWR-04 |

Ưu tiên rail 3,3 V là lựa chọn sơ bộ của bản hoàn thiện này. Dòng từng IC/module và phần dự phòng được lập trong Hardware Design trước khi chốt linh kiện. Không cộng công suất rail 3,3 V thêm lần nữa vào tổng lấy từ rail 5 V. Dòng điều khiển quy về đầu vào khi mang tải phải được xác định riêng để phối hợp giới hạn nguồn và cầu chì.

PCB phải có đầu nối tải phân biệt rõ với đầu vào, ký hiệu cực tính và điểm tiếp cận đo V_in, V_protected, V_load, Power Return, các rail nguồn cùng tín hiệu bảo vệ chính. Đầu nối tải được lựa chọn theo miền điện/dòng của nhánh tải và điều kiện đấu nối. Mẫu thử phải đáp ứng các mục tiêu kích thước, khối lượng và chi phí trong Product Requirements, có vị trí cố định bo và khoảng tiếp cận thay cầu chì, nạp/debug, đo kiểm.

Schematic hiện tại có:
- TP1–TP3 cho các tín hiệu đo.
- TP4–TP14 cho `V_PROTECTED`, `V_CTRL_BUCK_4V`, `V_CTRL_5V`, `V_BLUEPILL_3V3`, `POWER_GOOD`, gate Q9, `MCU_ENABLE`, `SHUTDOWN_REQUEST`, gate Q3/Q4 và hai điểm GND.
- J4 là header UART debug (GND, PA9 TX, PA10 RX), cố ý không có chân nguồn để không tạo thêm đường cấp nguồn phụ.
- R38 330 Ω là điện trở xả cố ý trên rail 3,3 V của Blue Pill, tiêu thụ khoảng 10 mA. Dòng này phải được tính vào ngân sách LDO.

Đường dòng lớn và đường hồi phải được bố trí để đáp ứng sụt áp, nhiệt và sai số đo. Cơ chế sơ bộ gồm lấy tín hiệu shunt kiểu Kelvin, kiểm soát đường hồi đo, bố trí khối nguồn chuyển mạch tách khỏi tín hiệu nhạy và đặt mạch bảo vệ gần điểm cần bảo vệ. Kích thước đường đồng, lớp đồng, giá trị tụ và bố trí cụ thể thuộc Hardware Design/PCB.

## 6. Phối hợp và kiểm chứng toàn hệ thống

- Đối chiếu nguồn nội bộ với miền `V_protected` và phản ứng Input Protection; kiểm tra tổng tự tiêu thụ ≤2 W theo điều kiện PR-PWR-04.
- Xác định dự phòng dòng điều khiển quy về nguồn ngoài khi mang tải để giới hạn cấu hình dòng tải theo khả năng nguồn.
- Kiểm tra tổng sụt áp giữa đầu nối vào/ra, có xét đoạn chung mang cả dòng tải và dòng điều khiển.
- Phối hợp Sensing → HW_OCP → Power Switch để đạt ngưỡng và thời gian ngắt toàn đường; phối hợp đường phần mềm với mục tiêu ≤10 ms của PR-PERF-11.
- Kiểm tra sai số toàn kênh đo, tương thích điện giữa các khối và trạng thái an toàn khi cấp/mất nguồn.

### 6.1. Đối chiếu ngân sách tích hợp

| Ràng buộc | Cách đối chiếu |
| --- | --- |
| Sụt áp toàn PCB | Current Sensing ≤25 mΩ và Power Switch ≤50 mΩ; phần sụt áp còn lại tối đa 0,125 V tại 1 A hoặc 0,25 V tại 2 A cho Input Protection, đầu nối và đường dẫn. Đo thực tế có xét dòng điều khiển trên đoạn chung |
| Ngắt nhanh | Tổng Current Sensing 20 µs + HW_OCP/logic 70 µs + Power Switch 10 µs ≤100 µs; đo thêm toàn đường theo PR-PERF-10 |
| Ngưỡng ngắt nhanh | Mọi tổ hợp sai số trong miền áp dụng phải bảo đảm 2 A < I_trip,thực tế ≤2,5 A; phối hợp khả năng chịu dòng trong lúc phát hiện/ngắt |
| Đo điện áp/sụt áp | Mỗi kênh điện áp ±25 mV frontend + ±25 mV MCU/ADC; hiệu hai kênh có giới hạn sai số ±0,1 V trong điều kiện áp dụng |
| Nguồn nội bộ | Kiểm tra đồng thời dòng hai rail, hiệu suất, tổn hao trước bộ nguồn và tự tiêu thụ đầu vào; dành dự phòng nhánh điều khiển khi cấu hình dòng tải |

### 6.2. Phép kiểm chứng cần chuẩn bị

| Nhóm | Nội dung kiểm chứng | Căn cứ |
| --- | --- | --- |
| Miền vận hành | Tại biên 5 V/15 V và các điểm trung gian, tải liên tục và ngắn hạn; sụt áp, nhiệt, lặp lại quá độ | PR-PERF-01, 02, 03, 12, 13 |
| Đo lường | Sai số cả ba kênh, V_drop khi ON, miền thông tin mở rộng, ảnh hưởng lấy mẫu và nhánh HW_OCP | PR-PERF-04 đến 07; mục 5 |
| Bảo vệ nhanh | Ngưỡng thực tế, thời gian từng khâu và toàn đường; set/clear đồng thời, lỗi giữ qua reset MCU | PR-PERF-09, 10; mục 4, 5.9–5.11 |
| Bảo vệ đầu vào/đầu ra | Ngược cực, quá áp kéo dài, dòng sự cố, xung và năng lượng tải trong miền sẽ đặc tả | Mục 2, 4 và 7 |
| Nguồn và fail-safe | Khởi động, mất/cấp lại nguồn, điện áp dư, mặc định OFF và không tự ON sau reset/phục hồi | PR-PWR-01 đến 04; Design Specification |
| Tích hợp firmware/UI | Dữ liệu mới ≤1 ms, ngắt phần mềm ≤10 ms, hiển thị ≤200 ms; thao tác cấu hình/ON/OFF/phục hồi | PR-PERF-08, 11; mục 5.7, 5.13–5.15 |
| Triển khai mẫu thử | BOM, kích thước, chiều cao, khối lượng, nhãn đầu nối, cố định bo và khả năng thao tác | PR-COST-01, 02; PR-PHY-01 đến 04; PR-INST-01 đến 05 |

Nguồn thử, tải đại diện cụ thể, thiết bị đo và môi trường thử hiện còn TBD. Cần chuẩn bị nguồn DC có miền áp/dòng phù hợp, tải kiểm thử phù hợp từng phép thử, thiết bị đo tham chiếu và oscilloscope/đầu đo đủ khả năng kiểm chứng sai số, thời gian và ripple đã đặt. Đây là nhu cầu thiết bị, chưa xác nhận nhóm đã có. Điều kiện thử, độ chính xác/băng thông cần thiết, định nghĩa ngắt thực tế, điều kiện đo dòng rò và tiêu chí đạt được cụ thể hóa trong Test Specification; kết quả tính toán, mô phỏng và phép đo được ghi riêng.

## 7. Những mục còn mở

| Mục | Ảnh hưởng | Bước giải quyết |
| --- | --- | --- |
| Miền nguồn sự cố, ngược cực và xung đầu vào; ngưỡng/timing/phục hồi Input Protection | Khả năng bảo vệ nhánh chung, miền `V_protected`, định mức linh kiện và bảo vệ đo `V_in` | Hoàn thiện ràng buộc Input Protection phối hợp nguồn nội bộ và Sensing trước schematic. Miền quá áp kéo dài đã chốt ≤16,7 V (mục 2); biên độ và năng lượng xung còn mở |
| Miền xung dương/âm, năng lượng, mức lặp lại và mức kẹp phía tải | Khả năng chịu đựng MOSFET, Output Sensing và tải | Xác định tải đại diện/miền hỗ trợ, hoàn thiện yêu cầu trước chọn mạch kẹp cuối cùng |
| Miền nhiệt độ và miền điện áp hai phía khi thử OFF | Điều kiện áp dụng điện trở dẫn, dòng rò và khả năng chặn ngược | Chốt điều kiện làm việc và thử trước hoàn tất đặc tả/định mức |
| Biến động dòng nguồn nội bộ và dòng khởi động | Tụ, khả năng cấp dòng, đáp ứng quá độ và phối hợp bảo vệ | Hoàn thiện biên độ, tốc độ bước dòng và thời gian ổn định; các mốc 10 µs/1 ms vẫn là đề xuất |
| Dòng sự cố lớn nhất và điều kiện lặp lại | Khả năng chịu dòng của shunt, MOSFET, đường đồng và phối hợp cầu chì | Xác định từ nguồn/tải và năng lượng sự cố trước khi hoàn tất định mức; ngưỡng ngắt ≤2,5 A không phải dòng sự cố lớn nhất |
| Mức analog, tham chiếu ADC và phân bổ sai số dòng | Sai số đo, khoảng ngưỡng HW_OCP và khả năng hoạt động khi đầu vào vượt miền | Tính toán khi chọn Sensing/MCU, gồm sai số, common-mode, bão hòa và điều kiện chưa có nguồn; các đề xuất 0–3 V, ADC ≥12 bit và dung sai HW_OCP ±5% chưa được duyệt riêng |
| Điều kiện đo timing và lệch thời điểm lấy mẫu điện áp | Khả năng kiểm chứng 100 µs/10 ms và V_drop khi biến động | Chốt tốc độ tăng dòng, mức vượt ngưỡng, tiêu chí ngắt thực tế và giới hạn lệch mẫu trong Hardware Design/Test Specification |
| MCU, Display, Buttons và interface cụ thể | Nhu cầu chân, nguồn, dòng, mức logic và cơ chế báo sẵn sàng HW_OCP | Chọn MCU/module, màn hình, giao tiếp, số nút; hoàn thiện bảng chân, V_IH/V_IL/V_OH/V_OL, định mức tải tín hiệu và timing clear trước schematic cuối cùng |
| Cơ chế nguồn và xử lý năng lượng tải | Khả năng đáp ứng ở biên 5 V, tự tiêu thụ, nhiệt và xung khi ngắt | Đánh giá buck-boost 5 V/LDO 3,3 V và phương án kẹp xung; các lựa chọn này vẫn là hướng khảo sát |
| Nguồn phụ khi nạp/debug | D20 đã chặn USB VBUS cấp ngược vào `V_CTRL_5V`. Chân 3,3 V của mạch nạp SWD vẫn cấp ngược được vào rail 3,3 V (gồm cả latch U7) | Không nối chân 3,3 V của ST-Link khi đang cấp Vin. Đo trên mẫu thử: ngưỡng 5V_UV thực tế, sụt áp D20 và rail 3,3 V ở biên |
| Quá áp >16,7 V kéo dài làm hỏng D19 | D19 là linh kiện hy sinh; F1 chỉ đứt nếu nguồn cấp đủ dòng; có rủi ro quá nhiệt cục bộ | Ghi vào hướng dẫn sử dụng: chỉ dùng nguồn ≤15 V. Test Spec: thử xung kẹp trước Q1/Q2, và thử quá áp kéo dài chỉ với nguồn giới hạn dòng có kiểm soát, có giám sát nhiệt, coi là phép thử phá hủy. Muốn chịu 24 V thì phải xem lại miền sự cố |
| Mô hình nguồn/tải, thiết bị đo và điều kiện thử | Khả năng tính toán, mô phỏng và kiểm chứng các mục tiêu | Bổ sung khi xác định phương án thử; dùng làm đầu vào Test Specification |

Các ngưỡng cấu hình phần mềm tiếp tục theo Product Requirements và Design Specification. Thông số linh kiện, giá trị mạch, topology chi tiết và tính toán triển khai được hoàn thiện trong Hardware Design, có đối chiếu các yêu cầu và hướng đã duyệt ở trên.

Bản HW Spec sơ bộ đã bao phủ các khối và giao diện của kiến trúc. Những TBD về miền sự cố, điều kiện áp dụng và điều kiện thử phải được giải quyết trước khi chốt định mức và schematic để chế tạo. Các mục tiêu hiệu năng cần có kết quả đánh giá khả thi khi thiết kế và bằng chứng kiểm chứng trên mẫu thử.
