# Đặc tả kiểm thử sơ bộ

> Bản sơ bộ v0.1, ngày 2026-11-05. Tài liệu mô tả kế hoạch, phương pháp và tiêu chí kiểm chứng của phiên bản rebuild. Mọi phép thử đang ở trạng thái chưa thực hiện. Các điều kiện còn mở được ghi tại mục 7.3 và phải được hoàn thiện trước khi thực hiện hoặc kết luận phép thử liên quan.

## 1. Phạm vi và phương pháp

Tài liệu đối chiếu [Product Requirements](product-requirements.md), [Design Specification](design-specification.md), [Hardware Specification](hardware-specification.md) và [Software Specification](software-specification.md). Bộ yêu cầu hiện hành là cơ sở đánh giá. Các mục tiêu hiệu năng được xem là tiêu chí thiết kế cần kiểm chứng.

Khung phương pháp, quy trình chung và một số tình huống của Phần IV trong báo cáo BTL trước đây được sử dụng làm tham khảo. Các miền nguồn, sai số, thời gian, trạng thái và thao tác phục hồi được cập nhật theo bộ spec hiện tại.

| Phương pháp | Phạm vi kết luận |
| --- | --- |
| Rà tài liệu và tính toán | Đối chiếu BOM, định mức, ngân sách, cấu hình và thiết kế với yêu cầu |
| Mô phỏng | Kiểm tra hành vi trong điều kiện và phạm vi mô hình công cụ hỗ trợ; ghi rõ linh kiện hoặc hiệu ứng được giản lược |
| Giả lập firmware/tiêm sự kiện | Kiểm tra quy đổi, bộ đếm, cấu hình, trạng thái và phân xử lệnh theo dữ liệu đưa vào |
| Đo từng khối | Kiểm chứng đặc tính và interface thực tế của phần đường đo/điều khiển được thử |
| Đo tích hợp | Kiểm chứng toàn đường từ điều kiện vật lý tới phản ứng đầu ra, cùng tương tác HW/SW/UI |

Phép thử tiêm tín hiệu tại comparator chỉ kết luận cho phần đường phía sau điểm tiêm. Thời gian ngắt toàn đường phải được đo với mốc bắt đầu ở đại lượng vật lý theo yêu cầu. Timestamp firmware phục vụ đối chiếu xử lý; phép đo ngắt thực tế cần quan sát đường công suất bằng thiết bị phù hợp.

Các mã dưới đây định danh nhóm phép thử. Mỗi biến thể, điểm đo hoặc trạng thái ban đầu phải có bản ghi kết quả riêng. Những phép thử chưa có đầy đủ điều kiện chỉ được chạy trong phạm vi đã xác định, và kết luận phải ghi rõ phạm vi đó.

## 2. Điều kiện và thiết bị chung

### 2.1. Thiết bị và thiết lập

| Nhóm | Nhu cầu | Thông tin phải hoàn thiện |
| --- | --- | --- |
| Nguồn DC | Điều chỉnh được trong miền 5–15 V và cấp đủ dòng tải cộng nhánh điều khiển; kiểm soát được giới hạn dòng | Model, khả năng ngắn hạn, hành vi giới hạn dòng/ngắt, tốc độ biến đổi áp |
| Tải | Điện trở công suất/tải điều chỉnh để tạo mức dòng; tải RC/điện tử và quạt phù hợp các profile | Giá trị, công suất, điện áp định mức, dòng khởi động, đặc tính xung và năng lượng |
| Đo tham chiếu | DMM hoặc thiết bị đo điện áp/dòng thích hợp, có thông tin độ chính xác trên thang sử dụng | Model, thang, độ không bảo đảm đo, ảnh hưởng đầu đo và cách nối |
| Đo dạng sóng | Oscilloscope và đầu đo điện áp/dòng phù hợp để quan sát nguồn, dòng tải và các tín hiệu bảo vệ | Băng thông, tốc độ lấy mẫu, độ trễ đầu đo, mốc trigger và cách đo dòng |
| Firmware/debug | Mạch nạp/debugger, UART nếu dùng; phương tiện đưa dữ liệu hoặc sự kiện giả lập | Phiên bản firmware, vị trí tiêm, điểm quan sát và ảnh hưởng của instrumentation |
| Đo triển khai | Thước, cân, BOM và thông tin chi phí chế tạo | Độ phân giải, phạm vi chi phí và cấu hình mẫu được cân/đo |

Nguồn, tải và thiết bị cụ thể đều còn TBD. Bảng này là nhu cầu kiểm thử. Không yêu cầu mua một loại tải điện tử cụ thể; lựa chọn thiết bị phải đáp ứng phép thử tương ứng.

### 2.2. Quy ước đo

- V_in lấy tại đầu nối vào trước Input Protection, V_load tại đầu nối ra, cùng tham chiếu Power Return. V_protected là nút sau Input Protection. I_load chỉ thuộc nhánh tải.
- Đo sụt áp toàn PCB giữa hai đầu nối; ghi nhận ảnh hưởng tiếp xúc, dây đo và dòng điều khiển đi trên đoạn chung. Điện trở/dòng đo tham chiếu bổ sung phải được xét khi thiết lập tải và đánh giá sai số.
- Sai số điện áp/dòng là hiệu giữa giá trị thiết bị công bố và giá trị tham chiếu tại cùng điều kiện. Phân biệt sai số toàn kênh với sai số từng khâu được đo riêng.
- Trước khi thử định lượng, xác định độ không bảo đảm của hệ đo và quy tắc kết luận đạt/không đạt. Nếu khả năng đo chưa đủ để phân biệt với giới hạn, ghi chưa đủ cơ sở kết luận.
- Với timing, ghi nguồn kích thích, mốc bắt đầu/kết thúc, tốc độ biến đổi và mức vượt ngưỡng, độ phân giải thời gian cùng sai lệch giữa đầu đo. Tiêu chí xác nhận đường cấp điện đã ngắt phải được chốt theo thiết lập thực tế. Không dùng V_load về 0 làm mốc ngắt chung vì tải có thể còn năng lượng.
- Ripple nguồn được đo theo HW Spec với giới hạn băng thông 20 MHz và đường mass đầu đo ngắn. Phép đo điện áp đỉnh khởi động cần băng thông đủ để quan sát đỉnh; điều kiện đo cụ thể được ghi riêng.

### 2.3. Chuẩn bị và quy trình chung

1. Ghi mã mẫu, phiên bản PCB/BOM, firmware, cấu hình, thiết bị và điều kiện môi trường. Xác định dữ liệu chuẩn hoặc kết quả mong đợi trước phép thử.
2. Kiểm tra đấu nối, cực tính, định mức tải/đầu đo và miền thử. Các phép thử ngược cực, sự cố dòng, quá áp hoặc xung chỉ thực hiện sau khi đã xác định miền chịu sự cố và giới hạn năng lượng tương ứng.
3. Đưa hệ thống về trạng thái ban đầu trong mô tả phép thử. Ghi rõ thao tác phục hồi lỗi và thao tác reset MCU, vì hai thao tác có hành vi khác nhau.
4. Thiết lập nguồn, tải, cấu hình và điểm tiêm/điểm đo. Các trường hợp không cần nguồn phụ phải kiểm soát đường cấp ngược từ USB/debugger/UART.
5. Thực hiện từng bước, ghi số đo, trạng thái và dạng sóng/log cần thiết. Mỗi giá trị đưa vào phải nằm trong miền thử đã xác định.
6. Đối chiếu tiêu chí, ghi kết luận cùng sai lệch. Khôi phục về điều kiện an toàn và kiểm tra phần tử cần thay, gồm cầu chì nếu đã tác động, trước biến thể tiếp theo.

## 3. Đối chiếu yêu cầu và phép thử

| Yêu cầu | Phép thử |
| --- | --- |
| PR-IO-01 đến 04 | TS-HW-01, TS-SW-01, 02, 10, TS-IN-01, 05 |
| UC-01, UC-07; FR-01 | TS-SW-01, TS-IN-07 |
| UC-02; FR-02 | TS-SW-02, 06, 10, 11, TS-HW-06 |
| FR-03, 04; PR-PERF-04, 05 | TS-HW-04, TS-IN-05 |
| FR-05; PR-PERF-06 | TS-HW-05, TS-IN-05 |
| FR-06; PR-PERF-08 | TS-SW-10, TS-IN-05 |
| UC-03, UC-04; FR-07, 08 | TS-SW-03, 05, 06, TS-IN-01, 07 |
| UC-05; FR-09, 10 | TS-SW-04, 06, 08, 09, TS-HW-08, 09, TS-IN-03, 04 |
| UC-06; FR-11 | TS-SW-07, TS-HW-08 |
| PR-PERF-01, 02, 03 | TS-IN-01, 07 |
| PR-PERF-07 | TS-HW-04, TS-SW-05 |
| PR-PERF-09, 10 | TS-HW-09, TS-IN-03 |
| PR-PERF-11 | TS-SW-03, 04, 05, TS-IN-04 |
| PR-PERF-12, 13 | TS-IN-02 |
| PR-PWR-01, 02, 03 | TS-HW-02, 03, 06, TS-SW-08, TS-IN-06 |
| PR-PWR-04 | TS-IN-06 |
| PR-COST-01, 02; PR-PHY-01 đến 04; PR-INST-01 đến 05 | TS-IN-08 |
| HW Spec mục 2, 3, 4 | TS-HW-01, 02, 03, 06, 07, 10 |
| HW Spec mục 5.1–5.8 | TS-HW-04, 05, 09, TS-IN-05 |
| HW Spec mục 5.9–5.12 | TS-HW-08, 09, TS-SW-07, 08, TS-IN-03 |
| HW Spec mục 5.13–5.16 | TS-SW-09, 10, TS-IN-05, 06, 08 |
| Design Spec mục 6; Software Spec mục 4–7 | TS-SW-01 đến 11 |
| Software Spec mục 3, 8–10 | TS-SW-09, 10, TS-IN-04, 05 |

Các phép thử có TBD chỉ chứng minh đã lập kế hoạch bao phủ yêu cầu. Kết luận đáp ứng được ghi sau khi điều kiện thử đầy đủ và có bằng chứng thực hiện.

## 4. Kiểm thử từng khối phần cứng

Các phép thử nhóm này dùng thiết bị đo, tải và giới hạn theo mục 2; nguồn điều khiển, nguồn công suất và điểm đo phải được ghi cho từng biến thể.

### TS-HW-01 — Input Protection và bảo vệ đầu đo

- **Yêu cầu và mục tiêu** — HW Spec mục 2, 5.6; Design Spec mục 3.1, 3.4. Kiểm tra đường nguồn chung, ngược cực, cô lập dòng sự cố, quá áp kéo dài và xung đầu vào.
- **Phương pháp và điều kiện đầu** — Rà định mức/phối hợp bảo vệ, mô phỏng và đo từng chức năng. Bắt đầu OFF, dùng nguồn/tải giới hạn năng lượng; miền sự cố, ngưỡng, thời gian và phục hồi còn TBD.
- **Thực hiện** — Lần lượt thử nguồn đúng cực, ngược cực, dòng sự cố, quá áp kéo dài và xung theo miền đã chốt. Quan sát V_in, V_protected, dòng tổng, phần tử bảo vệ và các rail. Thử riêng đầu đo V_in trước protection, gồm tình huống MCU chưa có nguồn.
- **Tiêu chí đạt** — Cấp V_protected đúng trong miền vận hành; ngắt quá áp nguồn chung độc lập MCU; các phần phía sau và đầu đo đáp ứng giới hạn chịu đựng được đặc tả. Đặc tuyến cầu chì/HW_OCP phải phù hợp vùng bảo vệ và dòng tổng. Thử phục hồi theo phương án phần cứng đã chọn.
- **Bằng chứng và TBD** — Tính toán/datasheet, sơ đồ nối và dạng sóng từng biến thể. Chưa chạy/kết luận thử sự cố khi thiếu miền áp/dòng/thời gian/năng lượng hoặc tiêu chí kẹp/ngắt; không tự lấy 24 V làm giới hạn đã duyệt.

### TS-HW-02 — Rail nguồn sau ổn định

- **Yêu cầu và mục tiêu** — HW Spec mục 3, 5.16. Kiểm tra điện áp, ripple, dòng và hiệu suất.
- **Phương pháp và điều kiện đầu** — Đo sau ổn định tại V_in = 5 V, 15 V và các điểm trung gian được chọn. Tải hai rail nằm trong ngân sách, có điểm tải nhẹ và điểm sử dụng tối đa đồng thời.
- **Thực hiện** — Thay đổi tải rail, ghi điện áp/ripple/dòng từng rail và công suất vào/ra bộ tạo 5 V. Xét dòng đầu vào bộ tạo 3,3 V trong tổng tải 5 V. Kiểm tra khả năng bảo vệ tích hợp theo IC thực tế sau khi xác định điều kiện thử.
- **Tiêu chí đạt** — Rail 5 V trong 4,75–5,25 V, ripple ≤50 mVpp; rail 3,3 V trong 3,201–3,399 V, ripple ≤20 mVpp. Giới hạn điện áp bao gồm ripple. Đáp ứng tải 5 V tổng ≤300 mA, 3,3 V ≤200 mA đồng thời theo ngân sách. Hiệu suất bộ tạo 5 V ≥85% tại tổng tải 300 mA trên miền đầu vào quy định.
- **Bằng chứng và TBD** — Bảng tải/điện áp/dòng/hiệu suất, dạng sóng ripple. Các điểm tải cụ thể, miền V_protected, nhiệt độ và tiêu chí bảo vệ của IC còn TBD.

### TS-HW-03 — Khởi động và mất/cấp lại nguồn

- **Yêu cầu và mục tiêu** — HW Spec mục 3, 5.11; PR-PWR-02. Kiểm tra nguồn nội bộ và mặc định OFF trong quá trình cấp nguồn.
- **Phương pháp và điều kiện đầu** — Đo với các tốc độ tăng/giảm nguồn và mức tải rail sẽ quy định. Có biến thể tụ đã xả và còn điện áp dư.
- **Thực hiện** — Cấp nguồn, cắt nguồn rồi cấp lại với các khoảng nghỉ; quan sát V_protected, hai rail, MCU_ENABLE, CLEAR_FAULT_REQUEST và đường công suất.
- **Tiêu chí đạt** — Từ khi đầu vào hợp lệ và khối được cho phép tới khi cả hai rail ổn định ≤100 ms. Đỉnh khởi động ≤5,25 V và ≤3,399 V tương ứng. Khi nguồn hợp lệ trở lại, nguồn nội bộ tự khởi động theo cùng giới hạn. Switch giữ OFF khi nguồn/đường bảo vệ chưa hợp lệ và tới khi có lệnh ON hợp lệ.
- **Bằng chứng và TBD** — Dạng sóng toàn quá trình. Tốc độ ramp, khoảng nghỉ, tải rail, băng thông đo đỉnh và cơ chế xác nhận nguồn/đường bảo vệ hợp lệ còn TBD; 100 ms là chỉ tiêu nguồn nội bộ.

### TS-HW-04 — Hai kênh điện áp và V_drop

- **Yêu cầu và mục tiêu** — FR-03, 04; PR-PERF-04, 05, 07; HW Spec mục 5.4, 5.6, 5.8. Kiểm tra miền thông tin và sai số.
- **Phương pháp và điều kiện đầu** — Dùng nguồn tín hiệu/điện áp thử và thiết bị tham chiếu phù hợp. Khi thử V_load trong miền 0–15 V hoặc biểu diễn 0–20 V, chọn thiết lập tách biệt an toàn, có kiểm soát cấp ngược. MCU phải còn nguồn hợp lệ.
- **Thực hiện** — So sánh số đo ở các điểm biên/trung gian của từng miền. Lặp V_load ở ON/OFF khi thiết lập cho phép. Khi ON, so sánh V_in − V_load với sụt áp tham chiếu giữa đầu nối. Rà/đo riêng ngân sách frontend và MCU/ADC khi có điểm tiếp cận.
- **Tiêu chí đạt** — Sai số V_in ≤±0,05 V trong 5–15 V; V_load ≤±0,05 V trong 0–15 V; V_drop ≤±0,1 V khi ON và các phép đo trong miền quy định. Đáp ứng phân bổ ±25 mV frontend và ±25 mV MCU/ADC quy về đầu nối. Biểu diễn thông tin 0–20 V cho cả hai kênh; không suy rộng chỉ tiêu ±0,05 V sang miền chưa quy định.
- **Bằng chứng và TBD** — Bảng sai số từng điểm, cấu hình hiệu chuẩn và thời điểm đo. Điểm thử, điều kiện nhiệt, độ chính xác ngoài miền chuẩn và giới hạn lệch mẫu còn TBD. Bảo vệ đầu đo thử tại TS-HW-01.

### TS-HW-05 — Kênh dòng và tổn hao Current Sensing

- **Yêu cầu và mục tiêu** — FR-05; PR-PERF-06; HW Spec mục 5.1, 5.3, 5.5. Kiểm tra dòng nhánh tải và sụt áp của khối đo.
- **Phương pháp và điều kiện đầu** — Tải điều chỉnh và đo dòng tham chiếu; điểm dòng trên 1 A chỉ duy trì trong miền ngắn hạn hoặc thử riêng khối theo định mức đã xác định.
- **Thực hiện** — So sánh I_load từ 0–2 A; thay đổi tiêu thụ nhánh điều khiển để xác nhận vị trí đo. Đo sụt áp khối tại 1 A và 2 A. Kiểm tra miền thông tin 0–3 A và trạng thái đường bảo vệ khi vượt miền, theo thiết lập sự cố đã xác định.
- **Tiêu chí đạt** — Sai số toàn kênh ≤±0,05 A trong 0–2 A; không bao gồm dòng điều khiển. Sụt áp ≤25 mV tại 1 A và ≤50 mV tại 2 A, điện trở đường khối ≤25 mΩ. Miền mở rộng phải hỗ trợ HW_OCP, kể cả vượt miền biểu diễn trong phạm vi sự cố được đặc tả.
- **Bằng chứng và TBD** — Bảng sai số/sụt áp, tín hiệu CURRENT_INFORMATION và trạng thái bảo vệ. Miền dòng sự cố, khả năng chịu xung và cách tách thử trên ngưỡng HW_OCP còn TBD.

### TS-HW-06 — Ưu tiên và mặc định Power Switch

- **Yêu cầu và mục tiêu** — HW Spec mục 4, 5.10–5.11; PR-PWR-02. Kiểm tra logic điều khiển độc lập firmware.
- **Phương pháp và điều kiện đầu** — Thử từng khối bằng các mức logic 3,3 V hợp lệ, tải kiểm soát; quan sát enable, shutdown và dẫn công suất.
- **Thực hiện** — Thử bốn tổ hợp enable/shutdown; thử MCU reset, enable Hi-Z, mất nguồn điều khiển và đường bảo vệ chưa sẵn sàng. Ghi nhận hành vi khi cấp lại nguồn.
- **Tiêu chí đạt** — Chỉ ON khi enable = 1, shutdown = 0 và nguồn/đường bảo vệ hợp lệ. Shutdown có ưu tiên. Enable Hi-Z/reset về không hoạt động; clear mặc định LOW. Không xuất hiện cấp tải ngoài ý muốn trong các khoảng chưa được cho phép.
- **Bằng chứng và TBD** — Bảng logic và dạng sóng chuyển tiếp. Mức logic chi tiết và điều kiện nguồn hợp lệ theo thiết kế cuối cùng.

### TS-HW-07 — Điện trở dẫn, dòng rò và thời gian Power Switch

- **Yêu cầu và mục tiêu** — HW Spec mục 4. Kiểm tra đặc tính riêng toàn cụm Switch.
- **Phương pháp và điều kiện đầu** — Đo từng biến thể ON, OFF thuận/ngược và ngắt. Điều kiện điện áp hai phía, gate, nhiệt độ và nguồn cấp phụ phải được xác định.
- **Thực hiện** — Đo sụt áp ở 1 A/2 A khi ON. Khi OFF, đặt điện áp hai phía theo từng chiều, chờ hết quá độ rồi đo dòng rò; gồm nguồn mất nhưng phía tải còn điện. Khi đang dẫn, kích SHUTDOWN_REQUEST và đo tới mốc ngắt thực tế.
- **Tiêu chí đạt** — Điện trở dẫn tổng ≤50 mΩ, tương ứng sụt áp ≤0,05 V tại 1 A và ≤0,10 V tại 2 A. Dòng rò mỗi chiều ≤100 µA sau ổn định. Ngắt riêng khối ≤10 µs từ shutdown tại đầu vào đạt mức kích hoạt tới ngắt thực tế.
- **Bằng chứng và TBD** — Bảng điện áp/dòng/nhiệt, dạng sóng ngắt. Điều kiện thử và mốc xác nhận ngắt phải chốt trước kết luận; khả năng chặn ngược khi OFF không chứng minh có bảo vệ dòng ngược khi ON.

### TS-HW-08 — Giữ lỗi, clear và cấp/reset nguồn HW_OCP

- **Yêu cầu và mục tiêu** — HW Spec mục 5.9–5.11; Design Spec mục 3.5. Kiểm tra set-dominant latch và interface lỗi.
- **Phương pháp và điều kiện đầu** — Tiêm thông tin dòng/điều kiện phát hiện theo thiết kế, quan sát shutdown, status, clear và enable. Bắt đầu ở trạng thái không lỗi đã xác nhận.
- **Thực hiện** — Tạo lỗi rồi đưa thông tin dòng về bình thường; thử clear riêng và clear đồng thời lỗi. Reset MCU khi lỗi còn giữ; thử lỗi ngay khi cấp nguồn, mất nguồn và cấp lại.
- **Tiêu chí đạt** — Lỗi được giữ sau khi kích thích hết, phát shutdown/status tích cực HIGH. Lỗi ưu tiên clear; MCU reset không tự clear latch còn được nuôi. Khi khởi động thấy lỗi giữ, MCU vào FAULT/OFF. Khi nguồn/đo chưa hợp lệ Switch OFF. Sau mất nguồn hoàn toàn, lỗi HW_OCP chưa được phục hồi vẫn hiện FAULT nhờ bản ghi Flash, kể cả khi latch đã mất trạng thái. Cấp nguồn lạnh khi không có bản ghi mà latch lên ở trạng thái lỗi thì MCU tự khởi tạo latch, không vào FAULT. Cần đo tần suất latch lên ở trạng thái lỗi khi cấp nguồn lạnh.
- **Bằng chứng và TBD** — Dạng sóng từng tổ hợp và log trạng thái. Timing clear/status, điều kiện nguồn và khả năng giữ latch qua từng kiểu reset phải được ghi rõ.

### TS-HW-09 — Ngưỡng HW_OCP và ngân sách từng khâu

- **Yêu cầu và mục tiêu** — PR-PERF-09; HW Spec mục 5.2, 5.9. Kiểm tra ngưỡng thực tế và phần trễ trước Switch.
- **Phương pháp và điều kiện đầu** — Kết hợp rà sai số toàn đường với đo dòng tham chiếu/tín hiệu. Thiết lập công suất phải giới hạn năng lượng và có miền chịu sự cố đã xác định; tiêm analog riêng chỉ kiểm chứng phần đường tương ứng.
- **Thực hiện** — Xác định dòng tác động bằng các lượt tăng dòng/clear có kiểm soát. Đo tại các điều kiện nguồn/nhiệt thiết kế quy định. Đo từ dòng vượt ngưỡng tới thông tin phù hợp tại HW_OCP và từ đó tới shutdown tại đầu vào Switch, với độ dốc/mức vượt ngưỡng đã ghi.
- **Tiêu chí đạt** — Toàn sai số phải bảo đảm 2 A < I_trip,thực tế ≤2,5 A. Độ trễ Current Sensing ≤20 µs; HW_OCP và logic trước Switch ≤70 µs trong điều kiện thử được đặc tả. Toàn đường kiểm chứng thêm tại TS-IN-03.
- **Bằng chứng và TBD** — Bảng ngưỡng, tính toán dung sai và dạng sóng từng mốc. Ngưỡng danh định, nhiệt độ, độ dốc, mức vượt ngưỡng và độ không bảo đảm đo còn TBD; chưa áp dụng dung sai ±5% như yêu cầu đã chốt.

### TS-HW-10 — Xung và năng lượng phía tải

- **Yêu cầu và mục tiêu** — HW Spec mục 4, 7; Design Spec mục 5.2. Kiểm tra khả năng chịu/xử lý xung phù hợp tải hỗ trợ.
- **Phương pháp và điều kiện đầu** — Rà/mô phỏng trước, đo với tải và miền năng lượng đã xác định. Tải cảm mạnh hoặc trả năng lượng lớn cần được đối chiếu phạm vi trước thử.
- **Thực hiện** — Đóng/ngắt tải tại các điều kiện quy định; ghi V_load, dòng, điện áp trên Switch và đầu đo. Thử điện áp dư sau OFF và trường hợp phía tải còn điện khi nguồn đầu vào mất trong miền hỗ trợ.
- **Tiêu chí đạt** — Xung/năng lượng nằm trong khả năng của phần bảo vệ, Switch và Sensing đã đặc tả; không gây cấp tải ngoài ý muốn hoặc hư hỏng trong miền cam kết. Điện áp dư được diễn giải theo Design Spec.
- **Bằng chứng và TBD** — Dạng sóng và đối chiếu định mức. Biên độ, thời gian, năng lượng, mức lặp lại, cơ chế kẹp và điều kiện nhiệt còn TBD; chưa đủ dữ liệu để đặt tiêu chí số cuối cùng.

## 5. Kiểm thử logic firmware

Các phép thử nhóm này dùng dữ liệu/sự kiện giả lập có thời điểm xác định hoặc thiết lập phần cứng tương đương. Mặc định quan sát trạng thái, cấu hình, warning/fault và yêu cầu GPIO. Mỗi phép thử lưu dữ liệu đầu vào, trình tự lệnh, thời điểm và đầu ra mong đợi/thực tế. Kiểm chứng toàn đường vật lý được thực hiện bổ sung tại mục 6.

### TS-SW-01 — Vòng đời cấu hình

- **Yêu cầu và mục tiêu** — FR-01; UC-01, 07; Software Spec mục 4. Kiểm tra mặc định, kiểm tra toàn bộ cấu hình và giới hạn trạng thái áp dụng.
- **Điều kiện và thực hiện** — Từ bộ mặc định hợp lệ, thử bộ hợp lệ, sai quan hệ ngưỡng, ngoài miền, sai thời gian và thiếu dự phòng nguồn. Lặp khi đang READY, FAULT, RUNNING, WARNING, INITIALIZING và RECOVERING. Thử hủy chỉnh và cập nhật đồng thời lượt giám sát.
- **Tiêu chí đạt** — Chỉ áp dụng ở READY/FAULT; cấu hình không hợp lệ bị từ chối kèm lý do, giữ bộ gần nhất, không tự tạo FAULT. RUNNING/WARNING tiếp tục theo bộ cũ và thông báo cần OFF trước. Bộ mới được áp dụng trọn vẹn; FAULT vẫn giữ lỗi, mọi lần áp dụng không tự ON/clear.
- **Bằng chứng và TBD** — Log cấu hình trước/đề nghị/sau và kết quả từng trạng thái. Giá trị mặc định, miền/bước chỉnh, quan hệ biên và dự phòng dòng cụ thể phải được hoàn thiện trước các lượt thử số.

### TS-SW-02 — Startup Checks và lệnh ON/OFF

- **Yêu cầu và mục tiêu** — FR-02; Software Spec mục 6.1–6.2. Kiểm tra khởi động và đường cấp tải.
- **Điều kiện và thực hiện** — Cấp nguồn/reset với từng điều kiện khởi động đạt, chưa đủ dữ liệu hoặc xác định thất bại. Từ READY gửi ON, sau đó OFF; lặp OFF ở WARNING. Thử ON khi không READY và khi đồng thời xuất hiện fault.
- **Tiêu chí đạt** — INITIALIZING/OFF khi còn chờ dữ liệu; đạt checks về READY/OFF, có lỗi về FAULT/OFF. READY nhận ON hợp lệ trực tiếp sang RUNNING; OFF bình thường về READY. Fault đồng thời ưu tiên OFF/FAULT. Lệnh ON sai trạng thái không được lưu để tự bật sau đó.
- **Bằng chứng và TBD** — Log checks, trạng thái và GPIO. Timeout khởi tạo và tiêu chí hợp lệ/sẵn sàng theo Software Spec mục 11.

### TS-SW-03 — Cảnh báo theo mức và ba bộ đếm độc lập

- **Yêu cầu và mục tiêu** — FR-07, 08, 09; Software Spec mục 5. Kiểm tra V_in thấp, V_in cao và I_load cao.
- **Điều kiện và thực hiện** — Đang RUNNING với bộ cấu hình hợp lệ. Đưa từng đại lượng vào vùng cảnh báo ở thời gian trước, tại và sau giới hạn; thử cảnh báo ngắt quãng, các khoảng rời nhau và nhiều cảnh báo chồng lấn. Thử cùng dạng dòng lúc khởi động và runtime.
- **Tiêu chí đạt** — Chưa đạt điều kiện ngắt thì WARNING và tiếp tục cấp điện. Điều kiện hết thì xóa riêng warning/reset riêng bộ đếm; không cộng dồn các khoảng rời nhau. Hết thời gian cho phép mà điều kiện còn thì OFF/FAULT. Còn bất kỳ warning nào thì không về RUNNING. Không có miễn bảo vệ riêng cho khởi động tải.
- **Bằng chứng và TBD** — Trace theo thời gian cho từng điều kiện và timer. Giá trị T, cách so sánh tại biên và hysteresis dùng cấu hình đã chốt; toàn đường đo tại TS-IN-04.

### TS-SW-04 — Ngắt trực tiếp theo mức

- **Yêu cầu và mục tiêu** — FR-09, 10; Software Spec mục 5.1–5.2. Kiểm tra ngắt thấp áp/quá áp/quá dòng khi đạt ngưỡng ngắt.
- **Điều kiện và thực hiện** — Từ RUNNING, đưa từng đại lượng trực tiếp tới miền ngắt, gồm lúc timer cảnh báo chưa hết và trường hợp bỏ qua miền cảnh báo. Giữ điều kiện ngắn/dài theo mô hình lấy mẫu được đặc tả.
- **Tiêu chí đạt** — Yêu cầu OFF, lưu đúng nguyên nhân và FAULT; không chờ hết timer cảnh báo. Hết điều kiện vẫn giữ FAULT cho tới phục hồi hợp lệ hoặc luồng reset đã quy định. Thời gian thật từ điều kiện vật lý tới ngắt kiểm chứng tại TS-IN-04.
- **Bằng chứng và TBD** — Trace đầu vào, phát hiện, fault và yêu cầu đầu ra. Biên ngưỡng, lọc và điều kiện xung tối thiểu cần phối hợp yêu cầu timing; không tự thêm khoảng miễn ngắt.

### TS-SW-05 — V_drop và diễn giải V_load

- **Yêu cầu và mục tiêu** — FR-04, 07, 08, 09; Software Spec mục 3, 5. Kiểm tra phép trừ và điều kiện đánh giá.
- **Điều kiện và thực hiện** — Giả lập V_in/V_load đồng thời trong các miền bình thường, warning và ngắt; lặp khi OFF, khi còn điện áp dư và khi một kênh invalid. Thử hai điện áp cùng giảm nhưng chênh lệch nhỏ.
- **Tiêu chí đạt** — Tính đúng V_drop khi dữ liệu phù hợp; chỉ đánh giá sụt áp khi ON. V_drop đạt ngưỡng ngắt thì OFF/FAULT, không thêm timeout cảnh báo riêng. Khi OFF, điện áp dư hoặc V_load thấp không tự tạo lỗi; không tự thêm lỗi thấp V_load trùng điều kiện V_in/V_drop.
- **Bằng chứng và TBD** — Bộ giá trị, trạng thái và kết quả mong đợi/thực tế. Sai số phép đo kiểm tra tại TS-HW-04; giới hạn lệch mẫu/hysteresis còn theo mục 7.3.

### TS-SW-06 — Nhiều nguyên nhân và ưu tiên sự kiện

- **Yêu cầu và mục tiêu** — FR-08 đến 10; Software Spec mục 5.2, 6.4. Kiểm tra ghi nhận riêng và phân xử.
- **Điều kiện và thực hiện** — Tạo nhiều warning/fault; cho một nguyên nhân hết khi các nguyên nhân khác còn. Gửi ON và OFF trong cùng lượt xử lý; lặp với fault và với kết quả phục hồi vừa đạt.
- **Tiêu chí đạt** — Fault ưu tiên ON/warning/OFF bình thường và kết quả phục hồi. Khi chỉ ON/OFF đồng thời, OFF thắng và bỏ lệnh ON đó. Xóa một warning không xóa warning khác; giải quyết một lỗi chưa cho READY nếu lỗi khác còn ngăn phục hồi.
- **Bằng chứng và TBD** — Thứ tự sự kiện, danh sách warning/fault và GPIO. Phân loại nguyên nhân cụ thể theo khả năng phát hiện được hiện thực.

### TS-SW-07 — Phục hồi lỗi MCU, HW_OCP và nhiều lỗi

- **Yêu cầu và mục tiêu** — FR-11; Software Spec mục 6.3, 7. Kiểm tra phục hồi thủ công và xác nhận clear.
- **Điều kiện và thực hiện** — Từ FAULT, thử yêu cầu phục hồi khi nguyên nhân còn/hết, dữ liệu invalid, cấu hình vừa đổi và có nhiều lỗi. Với HW_OCP, thử clear thành công, thất bại, timeout, lỗi xuất hiện đồng thời hoặc tái xuất hiện ngay sau clear.
- **Tiêu chí đạt** — RECOVERING luôn OFF. Lỗi MCU không bắt buộc clear HW nếu latch không giữ lỗi. HW clear chỉ sau checks đạt và phải đọc lại xác nhận; lỗi ưu tiên clear. Thất bại về FAULT/OFF với lý do. Mọi lỗi ngăn phục hồi được xử lý mới READY/OFF; cần ON mới. I_load gần 0 khi OFF không tự xác nhận tải hết ngắn mạch.
- **Bằng chứng và TBD** — Log nguyên nhân/checks và dạng sóng enable/clear/status cho phép thử tích hợp. Timing clear, timeout và tiêu chí theo nguyên nhân còn TBD.

### TS-SW-08 — Reset và chính sách lỗi qua khởi động lại

- **Yêu cầu và mục tiêu** — Design Spec mục 6.3; Software Spec mục 6. Kiểm tra reset trong sáu trạng thái.
- **Điều kiện và thực hiện** — Đưa hệ thống tới từng trạng thái, chỉnh cấu hình và tạo lỗi phù hợp trước reset. Thử lỗi phần mềm đã hết/còn sau reset và lỗi HW còn được latch giữ; thử mất nguồn hoàn toàn như biến thể riêng.
- **Tiêu chí đạt** — Về INITIALIZING/OFF, dùng cấu hình mặc định; không khôi phục lỗi phần mềm cũ nhưng phải phát hiện lại lỗi hiện tại. HW còn giữ lỗi phải FAULT, không tự clear. Qua mất nguồn hoàn toàn, lỗi HW_OCP đã ghi nhận được khôi phục từ bản ghi Flash và vẫn cần phục hồi thủ công; lỗi phần mềm khác không được lưu bền vững. Thử thêm: mất nguồn đúng lúc đang ghi bản ghi (bản ghi hỏng phải được xử lý như có lỗi), và phục hồi thành công thì bản ghi bị xóa.
- **Bằng chứng và TBD** — Log trước/sau, cấu hình và waveform nguồn/enable/clear/status. Điều kiện latch giữ qua reset phải khớp thiết kế nguồn thực tế.

### TS-SW-09 — Dữ liệu giám sát chưa sẵn sàng hoặc mất hiệu lực

- **Yêu cầu và mục tiêu** — Software Spec mục 3, hành vi đã xác nhận. Kiểm tra mất cập nhật, invalid và dữ liệu dùng chung.
- **Điều kiện và thực hiện** — Ngăn một hoặc nhiều kênh cập nhật, báo lỗi ADC mà driver hỗ trợ hoặc đưa dữ liệu quá hạn; thử ở INITIALIZING, READY, RUNNING/WARNING và RECOVERING. Thử cập nhật xen kẽ đọc dữ liệu/cấu hình.
- **Tiêu chí đạt** — Chưa có dữ liệu đầu tiên thì INITIALIZING/OFF. Khi xác định dữ liệu mất hiệu lực, ngăn ON nếu OFF; đang ON thì OFF/FAULT và ghi lỗi giám sát. Không phục hồi thành công khi dữ liệu bắt buộc invalid; không dùng lẫn bộ dữ liệu/cấu hình chưa công bố trọn vẹn.
- **Bằng chứng và TBD** — Timestamp, cờ hợp lệ, trạng thái và yêu cầu đầu ra. Tuổi dữ liệu tối đa, tiêu chí xác định mất hiệu lực và phục hồi còn TBD; phân biệt sự cố thật với điều kiện điện bất thường nhưng phép đo vẫn hợp lệ.

### TS-SW-10 — Buttons, Display và cấu hình qua UI

- **Yêu cầu và mục tiêu** — FR-01, 02, 06, 08, 11; Software Spec mục 8. Kiểm tra thao tác và thông tin hiển thị.
- **Điều kiện và thực hiện** — Thử nhấn/giữ/thả/dội nút, chỉnh/xác nhận/hủy cấu hình và chuyển màn hình khi có warning/fault. Giữ nút ON hoặc phục hồi lúc cấp nguồn/reset; sau đó nhả/nhấn lại. Lặp khi nút có nhiều chức năng theo UI thực tế.
- **Tiêu chí đạt** — Một thao tác được nhận đúng số lần, giữ ON/phục hồi không tạo lặp lệnh. Nút giữ lúc khởi động chỉ nhận thao tác mới sau nhả/nhấn lại. Hiển thị đúng dữ liệu, đơn vị, cấu hình, trạng thái và nguyên nhân; dữ liệu invalid không bị trình bày như dữ liệu hiện tại. Menu không tự bật/clear lỗi hoặc cản xử lý bảo vệ.
- **Bằng chứng và TBD** — Log thao tác, ảnh/video màn hình và trạng thái. Số nút, debounce, nhấn dài/lặp chỉnh và layout theo thiết kế UI; chu kỳ hiển thị đo tại TS-IN-05.

### TS-SW-11 — Bao phủ state diagram

- **Yêu cầu và mục tiêu** — Design Spec mục 6.1–6.2; Software Spec mục 6. Kiểm tra mọi nhánh chuyển và các trạng thái giữ.
- **Điều kiện và thực hiện** — Lập danh sách từng hàng trong bảng chuyển trạng thái chuẩn; cho đủ điều kiện/sự kiện để đi qua từng hàng, giữ trạng thái khi chưa có điều kiện chuyển và thử lệnh không hợp lệ.
- **Tiêu chí đạt** — Khớp cả sáu trạng thái và mọi nhánh hiện hành. Chỉ RUNNING/WARNING có quyền yêu cầu ON; INITIALIZING/READY/FAULT/RECOVERING yêu cầu OFF. Không có bước Checking Before ON riêng. Không tự retry hoặc thực hiện ON tồn đọng sau fault/phục hồi.
- **Bằng chứng và TBD** — Bảng từng nhánh với đầu vào, trạng thái đầu/cuối, yêu cầu GPIO và mã biến thể đã chạy. Dùng các lượt TS-SW-02 đến 10 làm bằng chứng khi đã đi qua nhánh tương ứng.

## 6. Kiểm thử tích hợp và hiệu năng

### TS-IN-01 — Miền nguồn, dòng liên tục và ngắn hạn

- **Yêu cầu và mục tiêu** — PR-PERF-01 đến 03; UC-02, 03. Kiểm tra thiết bị khi mang tải trong miền công bố.
- **Phương pháp và điều kiện đầu** — Đo toàn hệ thống ở V_in = 5 V, 15 V và các điểm trung gian, nguồn đủ dòng tải cộng điều khiển, cấu hình phù hợp tải. Ghi đặc tính giới hạn dòng nguồn.
- **Thực hiện** — Thử các mức tải 0–1 A sau ổn định; thử các đợt >1–2 A trong giới hạn thời gian cấu hình và phép thử khả năng chịu ngắn hạn có kiểm soát. Lặp khi khởi động/runtime và theo điều kiện lặp đã xác định.
- **Tiêu chí đạt** — Vận hành đúng trong 5–15 V, chịu tải liên tục đến 1 A và ngắn hạn đến 2 A tối đa 1 s theo yêu cầu. Cấu hình và độ trễ thực tế không cho vận hành ngoài giới hạn. Đo sụt áp, sai số và nguồn nội bộ vẫn theo các yêu cầu áp dụng.
- **Bằng chứng và TBD** — Bảng nguồn/tải/thời gian/nhiệt và waveform dòng. Thời gian chạy liên tục tới ổn định, miền nhiệt, số lượt và khoảng nghỉ còn TBD. Thử chịu dòng 2 A/1 s phải có phương án điều khiển được rà để không vô hiệu hóa bảo vệ nhanh hoặc tự vượt miền phần cứng.

### TS-IN-02 — Sụt áp toàn PCB

- **Yêu cầu và mục tiêu** — PR-PERF-12, 13. Kiểm tra tổn hao đường công suất giữa hai đầu nối.
- **Phương pháp và điều kiện đầu** — Switch ON, đo điện áp tham chiếu tại hai đầu nối với tải 1 A và 2 A trong miền ngắn hạn. Ghi nguồn, nhiệt độ, dòng điều khiển và trạng thái các rail.
- **Thực hiện** — Đo trực tiếp chênh lệch giữa đầu nối, đối chiếu V_in − V_load do hệ thống tính; khi cần đo thêm từng đoạn để đối chiếu ngân sách.
- **Tiêu chí đạt** — Sụt áp toàn PCB ≤0,2 V tại 1 A và ≤0,4 V tại 2 A. Đánh giá sai số V_drop riêng theo PR-PERF-07. Các giới hạn sụt áp này không tự được dùng làm ngưỡng warning/fault.
- **Bằng chứng và TBD** — Bảng số đo, cách đấu đo và điều kiện nhiệt. Điểm nguồn/nhiệt và số lượt theo kế hoạch điều kiện thử cuối cùng.

### TS-IN-03 — Ngắt quá dòng phần cứng toàn đường

- **Yêu cầu và mục tiêu** — PR-PERF-09, 10; HW Spec mục 5.2. Kiểm tra từ dòng thực tế tới ngắt công suất, độc lập firmware.
- **Phương pháp và điều kiện đầu** — Thiết lập tăng dòng có kiểm soát trong miền chịu sự cố đã xác định, oscilloscope đo dòng tải, shutdown và đại lượng xác nhận ngắt. Dòng nguồn phải đủ đi qua ngưỡng để phép thử có ý nghĩa.
- **Thực hiện** — Xác định ngưỡng tác động thực tế; tạo vượt ngưỡng với độ dốc/mức vượt đã quy định. Lặp khi MCU đang cập nhật Display/UART hoặc không kịp xử lý sự kiện. Ghi cả phản ứng vật lý và trạng thái MCU sau ngắt.
- **Tiêu chí đạt** — Ngưỡng thực tế >2 A và ≤2,5 A. Từ lúc I_load vượt ngưỡng HW_OCP áp dụng đến khi ngắt đường cấp điện thực tế ≤100 µs. Shutdown giữ tới clear hợp lệ; MCU phản ánh FAULT khi còn hoạt động. Đối chiếu thêm phân bổ 20/70/10 µs.
- **Bằng chứng và TBD** — Dạng sóng có đánh dấu hai mốc và điều kiện đo. Cần chốt miền sự cố, cách đo dòng, mức vượt, độ dốc và tiêu chí ngắt. Nguồn tự giới hạn trước ngưỡng phải ghi không đủ điều kiện thử HW_OCP ở lượt đó và kiểm tra phối hợp đường khác.

### TS-IN-04 — Ngắt phần mềm toàn đường

- **Yêu cầu và mục tiêu** — PR-PERF-11; FR-09. Kiểm tra đủ trường hợp theo mức và thời gian.
- **Phương pháp và điều kiện đầu** — Đo từ V_in, I_load hoặc V_drop vật lý tới ngắt thực tế. Cấu hình ngưỡng trong miền thử cho phép, bảo vệ phần cứng vẫn hoạt động. Phép thử dòng phải chọn vùng quan sát được ngắt phần mềm trước HW_OCP.
- **Thực hiện** — Với từng đại lượng, tạo bước qua ngưỡng ngắt. Với thấp áp, quá áp và dòng cao, giữ trong vùng cảnh báo tới hết thời gian cho phép. Lặp theo các thời điểm tương đối với chu kỳ lấy mẫu và khi UI/UART hoạt động đồng thời.
- **Tiêu chí đạt** — Ngắt thực tế ≤10 ms từ khi đại lượng đạt điều kiện ngắt theo mức; trường hợp theo thời gian tính từ khi thời gian cho phép hết mà điều kiện còn. Bao gồm Sensing, lấy mẫu, lọc, xử lý và Switch. V_drop không có timeout riêng. Lỗi đúng nguyên nhân và giữ FAULT/OFF.
- **Bằng chứng và TBD** — Waveform kích thích/ngắt, cấu hình, log đối chiếu. Cần chốt biên ngưỡng/hysteresis, cách tính thời điểm vật lý của điều kiện thời gian và tiêu chí ngắt. Thử dữ liệu giả lập chỉ kết luận cho logic/đường phía sau điểm tiêm.

### TS-IN-05 — Chu kỳ dữ liệu và giao diện khi xử lý đồng thời

- **Yêu cầu và mục tiêu** — HW Spec mục 5.7; PR-PERF-08; Software Spec mục 9. Kiểm tra dữ liệu mới và hiển thị dưới tải xử lý.
- **Phương pháp và điều kiện đầu** — Dùng timestamp/điểm quan sát và tín hiệu đo biến đổi đã biết để phân biệt bộ dữ liệu mới với việc đọc lại giá trị cũ. Chạy các trạng thái, menu, thao tác nút và mức log được hỗ trợ.
- **Thực hiện** — Đo khoảng cách công bố dữ liệu ba kênh và hoàn tất cập nhật giao diện. Tạo warning/fault giữa lúc giao tiếp màn hình/UART, thử quá tải log và lỗi giao tiếp mà driver hỗ trợ.
- **Tiêu chí đạt** — Bộ dữ liệu mới mỗi ≤1 ms, hiển thị dữ liệu/trạng thái mỗi ≤200 ms trong vận hành. Không trộn kênh/cấu hình; cảnh báo/lỗi được đưa vào cập nhật trong giới hạn khi UI còn hoạt động. Ngắt toàn đường vẫn đạt TS-IN-03, 04 ở các điều kiện tải xử lý tương ứng.
- **Bằng chứng và TBD** — Trace chu kỳ, log tải xử lý và ảnh/video hoặc đo giao tiếp kèm xác nhận hiển thị. Phương tiện đo hoàn tất cập nhật, thời gian quan sát và điều kiện tải xử lý lớn nhất còn TBD.

### TS-IN-06 — Tự tiêu thụ và ngân sách nguồn

- **Yêu cầu và mục tiêu** — PR-PWR-01 đến 04; Design Spec mục 3.2. Kiểm tra nguồn chung và dự phòng điều khiển.
- **Phương pháp và điều kiện đầu** — Không nối tải ngoài, thiết bị ổn định, các khối điều khiển/đo/UI hoạt động bình thường. Loại trừ hoặc ghi rõ các đường cấp phụ.
- **Thực hiện** — Đo công suất tại đầu nối vào ở cả ON/OFF trên miền 5–15 V. Kiểm tra MCU/đo/UI tiếp tục hoạt động khi tắt tải nếu nguồn còn hợp lệ. Thử vận hành không có nguồn phụ. Riêng khi mang tải, xác định dòng điều khiển quy về đầu vào và đối chiếu ngân sách nguồn/cấu hình/cầu chì.
- **Tiêu chí đạt** — Công suất không tải sau ổn định ≤2 W tại cả ON/OFF. Tắt tải không chủ động cắt nguồn điều khiển; vận hành bình thường bằng nguồn DC chung. Giới hạn cấu hình tải dành đủ dự phòng điều khiển theo thiết kế, có xét miền nguồn và chế độ. Kết quả đo không tải không thay cho ngân sách khi mang tải.
- **Bằng chứng và TBD** — Bảng công suất theo V_in/trạng thái, dòng rail và tính toán dự phòng. Danh sách điểm nguồn, chế độ UI, thời gian ổn định và mức dự phòng đầu vào còn TBD.

### TS-IN-07 — Tải đại diện và đổi nguồn/tải

- **Yêu cầu và mục tiêu** — UC-01, 03, 07; Concept mục profile; Software Spec mục 4, 5. Kiểm tra áp dụng cấu hình cho RESISTIVE, ELECTRONIC và FAN.
- **Phương pháp và điều kiện đầu** — Tải đại diện đã chọn và xác định điện áp/dòng/năng lượng phù hợp miền thiết bị. Tải RC dùng khi phù hợp đại diện nạp tụ của profile ELECTRONIC; mô hình RC không thay mọi hành vi tải điện tử.
- **Thực hiện** — Tắt tải, thay nguồn/tải và chọn cấu hình; thử ON, khởi động, biến động dòng runtime và OFF. Với quạt, điều kiện tăng tải/kẹt chỉ thử nếu định mức và phương án thử đã xác định. Tạo các đợt dòng còn cho phép và đạt điều kiện ngắt.
- **Tiêu chí đạt** — Dùng cùng quy tắc ngưỡng/thời gian; hành vi theo cấu hình, có cảnh báo khi tới miền warning và ngắt khi đủ điều kiện. Không tự ON khi đổi cấu hình, không miễn bảo vệ do khởi động. Các mức nguồn/dòng/xung nằm trong miền hỗ trợ và thỏa TS-IN-01, TS-HW-10.
- **Bằng chứng và TBD** — Thông số/model tải, cấu hình, waveform khởi động/runtime/ngắt. Linh kiện RC, quạt, cách tạo biến động, điều kiện lặp và phạm vi năng lượng còn TBD.

### TS-IN-08 — Chi phí, kích thước và lắp đặt

- **Yêu cầu và mục tiêu** — PR-COST-01, 02; PR-PHY-01 đến 04; PR-INST-01 đến 05; HW Spec mục 5.16. Kiểm tra mẫu bàn giao.
- **Phương pháp và điều kiện đầu** — Rà BOM/chi phí và đo mẫu PCB đã lắp ráp, quan sát thao tác lắp đặt. Ghi phiên bản và phần được tính vào mẫu.
- **Thực hiện** — Tổng hợp linh kiện, PCB, lắp ráp/vỏ nếu có; đo chiều dài/rộng, chiều cao và khối lượng. Kiểm tra nhãn đầu nối, cực tính, lỗ/chân đỡ, tiếp cận thay cầu chì, điểm đo và nạp/debug; thử đấu nối bằng dụng cụ cơ bản.
- **Tiêu chí đạt** — BOM ≤600.000 đồng, mẫu hoàn chỉnh ≤1.000.000 đồng theo phạm vi chi phí của Requirement. PCB ≤100 × 100 mm, chiều cao linh kiện ≤30 mm, khối lượng PCB lắp ráp ≤150 g. Đầu nối dễ tiếp cận/phân biệt, cố định cách điện và dùng DC trong môi trường bàn thí nghiệm trong nhà khô ráo/thông thoáng. Vỏ không bắt buộc.
- **Bằng chứng và TBD** — BOM/hóa đơn, bảng kích thước/khối lượng, ảnh và checklist lắp đặt. Đối chiếu loại trừ nguồn/tải/dây/mạch nạp và chi phí thiết bị theo Requirement; model phương tiện đo còn TBD.

## 7. Ghi nhận kết quả và các mục còn mở

### 7.1. Mẫu bản ghi

| Trường | Nội dung cần lưu |
| --- | --- |
| Định danh | Mã phép thử, mã biến thể/điểm đo, ngày và người thực hiện |
| Phiên bản | PCB/BOM, firmware, cấu hình, phiên bản yêu cầu/spec |
| Thiết lập | Sơ đồ nối/điểm tiêm/điểm đo, nguồn/tải/thiết bị/thang đo, môi trường |
| Điều kiện đầu | Trạng thái, lỗi đang giữ, nguồn, tải và giá trị tham số áp dụng |
| Thực hiện | Giá trị/sự kiện theo từng bước, thời điểm và mọi khác biệt so với kế hoạch |
| Kết quả | Số đo/trạng thái mong đợi và thực tế, sai số/độ không bảo đảm, tiêu chí đối chiếu |
| Bằng chứng | Dạng sóng gốc, bảng đo, log, ảnh/video hoặc kết quả tính toán/mô phỏng |
| Kết luận | Đạt, không đạt hoặc chưa đủ cơ sở kết luận; các biến thể chưa chạy ghi chưa thực hiện |
| Theo dõi | Sai lệch, nguyên nhân cần điều tra, thay đổi và phép thử phải chạy lại |

Bằng chứng được lưu khi thực hiện kiểm chứng, theo mã phép thử và phiên bản mẫu. Các tài liệu báo cáo DOCX/PPT tiếp tục tuân theo quy tắc Git ignore hiện hành. Kết quả thực tế và trạng thái thực hiện được cập nhật trong bản ghi kiểm chứng tương ứng.

### 7.2. Xử lý sai lệch và thử lại

Kết quả vượt giới hạn được ghi không đạt khi hệ đo đủ cơ sở kết luận. Nếu thiếu thiết bị, thiếu điều kiện hoặc thiếu độ chính xác, ghi rõ phần chưa kết luận được. Khi thay đổi phần cứng, firmware hoặc cấu hình mặc định, xác định các yêu cầu bị ảnh hưởng và chạy lại nhóm liên quan. Lưu cả bằng chứng trước/sau và lý do thay đổi.

### 7.3. Điều kiện cần hoàn thiện trước các lượt thử liên quan

| Mục còn mở | Ảnh hưởng | Phần cần phối hợp |
| --- | --- | --- |
| Nguồn, tải và thiết bị cụ thể | Miền áp/dòng, khả năng tạo vượt ngưỡng, độ chính xác và năng lượng | HW Design và chuẩn bị thiết bị trước thử công suất |
| Miền sự cố đầu vào/đầu ra, dòng sự cố và năng lượng | TS-HW-01, 05, 09, 10; TS-IN-03, 07 | HW Spec mục 7 và lựa chọn linh kiện/phương án bảo vệ |
| Cấu hình mặc định, ngưỡng và thời gian, hysteresis, dự phòng dòng | TS-SW-01 đến 08; TS-IN-01, 04, 07 | HW/Software Design; giữ nguyên giới hạn chung đã đặt |
| Tuổi dữ liệu, timeout khởi tạo/clear, điều kiện phục hồi và sẵn sàng | TS-SW-02, 07, 09; TS-HW-03, 08 | HW/Software Spec và khả năng quan sát của phần cứng |
| Điểm thử, miền nhiệt, thời gian ổn định và số lần lặp | Sai số, nhiệt, dòng rò, liên tục/ngắn hạn, rail và độ lặp ngưỡng | HW Design và kế hoạch chạy thử cuối cùng |
| Cách xác nhận ngắt thực tế, tốc độ biến đổi và mức vượt ngưỡng | TS-HW-07, 09; TS-IN-03, 04 | Thiết kế bàn thử và thiết bị đo; ghi mốc cho từng dạng thử |
| Độ không bảo đảm và quy tắc kết luận | Khả năng chứng minh sai số, dòng rò, ngưỡng, thời gian và ripple | Chọn thang/đầu đo, đánh giá hệ đo trước kết luận định lượng |
| MCU/Display/Buttons và UART cụ thể | Timing, UI, lỗi driver, cơ chế quan sát dữ liệu | Hardware/Software Design; hoàn thiện biến thể thử theo triển khai |
| Phạm vi và độ trung thực mô phỏng | Khả năng kiểm chứng bằng Proteus hoặc công cụ phù hợp | Ghi mô hình, điều kiện giản lược và yêu cầu cần đo bổ sung |

Test Spec sơ bộ đã lập nhóm phép thử cho các yêu cầu hiện hành. Bộ tài liệu đủ để tiếp tục thiết kế và hoàn thiện điều kiện kiểm chứng; kết luận đáp ứng sản phẩm phụ thuộc các phép thử thực tế và bằng chứng được ghi nhận.
