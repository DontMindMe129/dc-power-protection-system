# Yêu cầu sản phẩm

> Phiên bản: 0.1
>
> Giai đoạn: Xác định sản phẩm
>
> Trạng thái: Bản cơ sở đang được hoàn thiện

Tài liệu này mô tả sản phẩm phải làm gì và các ràng buộc có thể quan sát từ bên ngoài hệ thống. Tài liệu chưa quyết định MCU, cảm biến, MOSFET, mạch khuếch đại, thuật toán chi tiết hoặc cách bố trí PCB.

Các trạng thái được sử dụng trong tài liệu:

- **Đã xác nhận:** đã được chọn làm yêu cầu hiện tại;
- **Tạm thời:** giá trị định hướng để tiếp tục thiết kế, có thể thay đổi sau khi tính toán hoặc thử nghiệm;
- **TBD:** chưa đủ thông tin để đề xuất hoặc chốt giá trị.

## 1. Tên sản phẩm

- Tiếng Việt: **Hệ thống giám sát và bảo vệ đường nguồn DC**.
- Tiếng Anh: **DC Power Path Monitoring and Protection System**.

## 2. Mục đích

Thiết bị được mắc giữa nguồn DC bên ngoài và tải DC nhằm giám sát điện áp đầu vào, điện áp tại tải và dòng điện trên đường công suất. Dựa trên các đại lượng đo được, thiết bị phát hiện trạng thái nguồn bất thường, sụt áp quá mức hoặc quá dòng tải; cung cấp thông tin, cảnh báo cho người dùng và tự động ngắt tải khi cần thiết theo cấu hình vận hành đã chọn.

Thiết bị chỉ thực hiện chức năng giám sát và bảo vệ; không điều chỉnh điện áp đầu ra và không thay thế nguồn DC bên ngoài.

## 3. Đầu vào và đầu ra

### 3.1. Đầu vào

- **PR-IO-01 — Đầu vào công suất:** năng lượng điện do nguồn DC bên ngoài cung cấp.
- **PR-IO-02 — Đầu vào thông tin:** lệnh vận hành và các tham số cấu hình do người dùng cung cấp, bao gồm cấu hình nguồn, profile tải, ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép. Các giá trị cấu hình phải nằm trong miền vận hành định mức của thiết bị.

### 3.2. Đầu ra

- **PR-IO-03 — Đầu ra công suất:** năng lượng DC được truyền từ nguồn đến tải dưới sự giám sát và có khả năng đóng hoặc ngắt để bảo vệ đường công suất. Điện áp đầu ra không được điều chỉnh mà bám theo điện áp đầu vào, trừ phần sụt áp trên thiết bị.
- **PR-IO-04 — Đầu ra thông tin:** các giá trị đo, trạng thái vận hành, cảnh báo và nguyên nhân ngắt bảo vệ của hệ thống.

### 3.3. Quan hệ công suất

```text
Khi đường công suất đóng: Vload ≈ Vin − Vdrop
Khi đường công suất ngắt: thiết bị không chủ động truyền năng lượng từ nguồn đến tải
```

Nguồn và tải là các đối tượng bên ngoài hệ thống. Hành vi điện của tải ảnh hưởng tới `Vload` và `Iload`, nhưng profile tải là đầu vào thông tin do người dùng chọn chứ không phải tải vật lý.

## 4. Trường hợp sử dụng

### UC-01 — Thiết lập hệ thống

Người dùng kết nối nguồn DC và tải tương thích, sau đó lựa chọn hoặc nhập cấu hình nguồn, profile tải và các ngưỡng được phép thay đổi. Hệ thống phải kiểm tra cấu hình có nằm trong miền vận hành định mức hay không. Trong quá trình thiết lập, đầu ra công suất được giữ ở trạng thái OFF.

### UC-02 — Cấp điện và giám sát tải bình thường

Người dùng yêu cầu bật đầu ra. Nếu điều kiện ban đầu hợp lệ, hệ thống đóng đường công suất và cấp điện cho tải. Trong quá trình hoạt động, hệ thống đo và hiển thị `Vin`, `Vload`, `Iload`, hiển thị trạng thái đường công suất và tiếp tục cấp điện khi các đại lượng nằm trong giới hạn của cấu hình.

### UC-03 — Tải có dòng khởi động

Khi tải được bật, dòng điện có thể tạm thời cao hơn dòng hoạt động bình thường. Hệ thống sử dụng profile tải để quyết định mức dòng và thời gian được chấp nhận. Nếu dòng trở về mức bình thường trong thời gian cho phép, hệ thống tiếp tục cấp điện và không ngắt nhầm tải.

### UC-04 — Phát hiện điều kiện bất thường

Hệ thống phải quan sát các tình trạng như:

- `Vin` nằm ngoài miền của cấu hình nguồn;
- `Iload` vượt miền vận hành bình thường;
- `Vdrop = Vin − Vload` quá lớn;
- `Vload` không phù hợp với trạng thái đóng hoặc ngắt mong đợi.

Nếu tình trạng chưa gây nguy hiểm tức thời, hệ thống cảnh báo và cung cấp thông tin giúp người dùng đánh giá vấn đề có thể đến từ nguồn, tải hoặc đường công suất. Kết quả chẩn đoán chỉ là gợi ý nếu các phép đo hiện có chưa đủ để kết luận tuyệt đối.

### UC-05 — Ngắt bảo vệ

Khi xảy ra tình trạng nguy hiểm, chẳng hạn quá dòng nghiêm trọng hoặc ngắn mạch, hệ thống phải nhanh chóng ngắt đường cấp điện tới tải. Sau khi ngắt, hệ thống phải:

- duy trì công tắc công suất ở trạng thái ngắt;
- không chủ động truyền năng lượng từ nguồn đến tải;
- không tự động đóng lại khi chưa thỏa mãn chính sách phục hồi;
- hiển thị trạng thái và nguyên nhân gây ngắt.

Yêu cầu này không đồng nghĩa với việc bảo đảm `Vload = 0 V` ngay lập tức vì phía tải có thể còn điện tích, dòng rò hoặc nguồn cấp ngược.

### UC-06 — Phục hồi sau lỗi

Sau khi nguyên nhân lỗi được loại bỏ, người dùng yêu cầu khôi phục hệ thống. Hệ thống phải kiểm tra lại điều kiện an toàn trước khi cho phép đóng đường công suất. Cơ chế phục hồi thủ công, tự động hoặc giới hạn số lần thử hiện là TBD.

### UC-07 — Thay đổi nguồn hoặc tải

Khi chuyển sang nguồn hoặc tải khác, người dùng chọn lại cấu hình tương ứng. Hệ thống chỉ chấp nhận cấu hình nằm trong miền vận hành định mức và sử dụng cấu hình mới cho các quyết định cảnh báo, cho phép hoạt động và ngắt bảo vệ.

## 5. Yêu cầu chức năng

### FR-01 — Điều khiển đường công suất

Hệ thống phải có khả năng cho phép hoặc ngăn năng lượng truyền từ nguồn DC đến tải theo lệnh vận hành và trạng thái bảo vệ.

### FR-02 — Đo điện áp đầu vào

Hệ thống phải đo `Vin` tại phía nguồn để xác định trạng thái điện áp do nguồn bên ngoài cung cấp.

### FR-03 — Đo điện áp tại tải

Hệ thống phải đo `Vload` tại phía tải khi đường công suất đang đóng và khi đã bị ngắt.

### FR-04 — Đo dòng tải

Hệ thống phải đo `Iload` chạy trên đường công suất từ nguồn đến tải. Giá trị này không bao gồm dòng tự tiêu thụ của hệ thống.

### FR-05 — Đánh giá trạng thái đường công suất

Hệ thống phải:

- so sánh `Vin`, `Vload` và `Iload` với cấu hình vận hành;
- xác định `Vdrop = Vin − Vload`;
- phân biệt trạng thái bình thường, cảnh báo và lỗi;
- phát hiện điện áp đầu ra không phù hợp với trạng thái đóng hoặc ngắt mong đợi.

### FR-06 — Quản lý cấu hình vận hành

Hệ thống phải hỗ trợ cấu hình nguồn và các profile tải `RESISTIVE`, `ELECTRONIC`, `FAN`, `CUSTOM`. Mỗi profile có thể xác định ngưỡng cảnh báo, ngưỡng ngắt và khoảng thời gian cho phép đối với hiện tượng quá độ.

Giới hạn áp dụng thực tế không được lớn hơn giá trị nhỏ nhất giữa giới hạn sản phẩm, giới hạn nguồn và giới hạn profile tải. Hệ thống phải từ chối hoặc giới hạn các giá trị cấu hình vượt ngoài miền vận hành định mức.

### FR-07 — Xử lý dòng khởi động và dòng đỉnh

Hệ thống phải cho phép tải có dòng ngắn hạn cao hơn dòng liên tục trong giới hạn về biên độ và thời gian của profile tải, nhằm tránh ngắt nhầm đối với tải hợp lệ.

### FR-08 — Cảnh báo bất thường

Khi phát hiện điều kiện bất thường nhưng chưa cần ngắt tức thời, hệ thống phải thông báo cho người dùng bằng trạng thái hoặc cảnh báo phù hợp.

### FR-09 — Ngắt bảo vệ

Khi phát hiện điều kiện nguy hiểm, hệ thống phải ngắt đường truyền năng lượng từ nguồn đến tải và ghi nhận nguyên nhân. Bảo vệ trước quá dòng nghiêm trọng hoặc ngắn mạch không được phụ thuộc hoàn toàn vào tốc độ xử lý của firmware.

### FR-10 — Duy trì trạng thái sau khi ngắt

Sau khi bảo vệ tác động, hệ thống phải giữ công tắc công suất ở trạng thái ngắt, không tự cấp điện lại ngoài chính sách phục hồi và cho phép người dùng xem trạng thái cùng nguyên nhân lỗi khi nguồn điều khiển còn hợp lệ.

### FR-11 — Phục hồi hoạt động

Hệ thống phải hỗ trợ yêu cầu phục hồi sau lỗi và kiểm tra lại điều kiện an toàn trước khi cho phép đóng đường công suất.

### FR-12 — Giao diện người dùng

Hệ thống phải cung cấp phương thức để người dùng:

- bật hoặc tắt đầu ra;
- chọn hoặc thay đổi cấu hình;
- theo dõi `Vin`, `Vload`, `Iload`;
- nhận biết trạng thái vận hành, cảnh báo và nguyên nhân ngắt;
- yêu cầu phục hồi sau lỗi.

## 6. Yêu cầu hiệu năng

### 6.1. Bảng thông số hiện tại

| Mã | Thông số | Giá trị hiện tại | Trạng thái |
| --- | --- | --- | --- |
| PR-PERF-01 | Miền điện áp hoạt động | `5–15 V DC` | Đã xác nhận |
| PR-PERF-02 | Dòng tải liên tục định mức | `0–1 A` | Tạm thời |
| PR-PERF-03 | Dòng đỉnh vận hành định mức | Trên `1 A` đến `2 A` | Tạm thời |
| PR-PERF-04 | Thời gian liên tục tối đa trong miền dòng đỉnh | `1 s` | Tạm thời |
| PR-PERF-05 | Sai số đo `Vin` | `±0,1 V` trong miền hoạt động `5–15 V` | Tạm thời |
| PR-PERF-06 | Sai số đo `Vload` | `±0,1 V` trong miền đo `0–15 V` | Tạm thời |
| PR-PERF-07 | Sai số đo `Iload` | `±0,05 A` trong miền đo `0–2 A` | Tạm thời |
| PR-PERF-08 | Chu kỳ cập nhật giá trị và trạng thái trên giao diện | `≤ 200 ms` | Tạm thời |
| PR-PERF-09 | Phản ứng với lỗi quá dòng nghiêm trọng | `≤ 100 µs` kể từ khi ngưỡng bảo vệ nhanh được phát hiện | Tạm thời |
| PR-PERF-10 | Độ trễ ngắt sau khi hết thời gian cho phép | `≤ 10 ms` | Tạm thời |
| PR-PERF-11 | Sụt áp tại dòng liên tục `1 A` | `≤ 0,2 V` | Tạm thời |
| PR-PERF-12 | Sụt áp tại dòng đỉnh `2 A` | `≤ 0,4 V` | Tạm thời |

### 6.2. Miền vận hành dòng điện định mức

Hệ thống phải hỗ trợ dòng tải liên tục từ `0–1 A`. Dòng trên `1 A` đến `2 A` được phép tồn tại trong khoảng thời gian do profile tải quy định nhưng không quá `1 giây`. Trong miền vận hành này, hệ thống phải hoạt động đúng chức năng và không hư hỏng nếu cấu hình đang chọn cho phép điều kiện đó.

Dòng vượt `2 A` hoặc vượt giới hạn thời gian nằm ngoài miền vận hành định mức và phải được xử lý bởi chức năng bảo vệ. `2 A/1 s` là biên trên của miền sử dụng được công bố và là điều kiện tối thiểu mà thiết kế phải đáp ứng; đây không phải giới hạn phá hủy tuyệt đối của linh kiện.

Ngưỡng tác động chính xác của bảo vệ nhanh và giới hạn chịu đựng tuyệt đối của phần cứng hiện là TBD.

### 6.3. Thời gian phản ứng bảo vệ

Bảo vệ nhanh áp dụng cho lỗi quá dòng nghiêm trọng và không được phụ thuộc hoàn toàn vào firmware. Bảo vệ có xử lý thời gian chỉ bắt đầu tính độ trễ ngắt sau khi điều kiện lỗi đã tồn tại hết khoảng thời gian mà profile cho phép.

Ví dụ, nếu profile cho phép một mức dòng trong `500 ms`, tổng thời gian trước khi ngắt có thể đạt `510 ms`: `500 ms` thời gian được phép và tối đa `10 ms` độ trễ xử lý ngắt.

### 6.4. Sụt áp đường công suất

Các giới hạn sụt áp được đánh giá giữa đầu nối vào và đầu nối ra của PCB, không bao gồm dây dẫn bên ngoài. Chúng kiểm soát tổng ảnh hưởng của phần tử đo dòng, công tắc công suất, đường đồng, đầu nối và mối tiếp xúc.

Giới hạn `0,2 V` tại `1 A` tương đương điện trở đường công suất tối đa `0,2 Ω` và tổn hao tối đa `0,2 W` tại điểm kiểm thử này. Giá trị tại `2 A` là điều kiện ngắn hạn.

### 6.5. Độ phân giải và độ chính xác

Độ phân giải hiển thị không được dùng để thay thế yêu cầu về độ chính xác. Độ phân giải hiển thị và tần số lấy mẫu nội bộ hiện là TBD. Sai số xấu nhất của `Vdrop` tính từ hai kênh điện áp có thể lớn hơn sai số của từng kênh và phải được xét khi xây dựng ngưỡng chẩn đoán.

Mạch đo `Vin` phải có khả năng nhận biết điện áp nằm phía trên giới hạn hoạt động `15 V` để hệ thống không cấp điện cho tải trong điều kiện quá áp. Miền phát hiện quá áp, độ chính xác ngoài miền hoạt động và giới hạn điện áp chịu đựng tuyệt đối hiện là TBD.

## 7. Chi phí sản xuất

| Mã | Hạng mục | Mục tiêu hiện tại | Trạng thái |
| --- | --- | --- | --- |
| PR-COST-01 | BOM cho một bo mạch | `≤ 600.000 VNĐ` | Tạm thời |
| PR-COST-02 | Một prototype hoàn chỉnh gồm PCB và linh kiện | `≤ 1.000.000 VNĐ` | Tạm thời |

Giá thành sản phẩm có thể bao gồm linh kiện trên PCB, chế tạo PCB, lắp ráp và vỏ nếu có. Giá thành không bao gồm nguồn DC bên ngoài, tải kiểm thử, mạch nạp/debugger, thiết bị đo, chi phí nghiên cứu và linh kiện mua dư.

## 8. Nguồn nuôi hệ thống

**PR-PWR-01 — Đã xác nhận:** Hệ thống phải tự cấp nguồn cho khối điều khiển, đo lường và giao diện từ nguồn DC đầu vào, thông qua một nhánh nguồn nội bộ được bảo vệ và điều chỉnh điện áp riêng. Nhánh này phải được lấy trước công tắc công suất của tải để hệ thống tiếp tục hoạt động khi tải bị ngắt, miễn là `Vin` còn nằm trong miền cho phép.

**PR-PWR-02 — Đã xác nhận:** Khi nguồn nuôi điều khiển mất hoặc không ổn định, đường công suất phải mặc định ở trạng thái OFF.

**PR-PWR-03 — Đã xác nhận:** Hệ thống không yêu cầu nguồn phụ trong vận hành bình thường.

**PR-PWR-04 — Tạm thời:** Công suất tự tiêu thụ của thiết bị không được vượt quá `1 W` trong miền điện áp hoạt động.

Công suất tự tiêu thụ không bao gồm công suất truyền tới tải và tổn hao trên đường công suất.

## 9. Kích thước và khối lượng

| Mã | Thông số | Giá trị hiện tại | Trạng thái |
| --- | --- | --- | --- |
| PR-PHY-01 | Kích thước PCB tối đa | `100 × 100 mm` | Tạm thời |
| PR-PHY-02 | Chiều cao linh kiện tối đa | `30 mm` | Tạm thời |
| PR-PHY-03 | Khối lượng PCB đã lắp ráp | `≤ 150 g` | Tạm thời |
| PR-PHY-04 | Vỏ sản phẩm | Không bắt buộc cho prototype đầu tiên | Tạm thời |

Các giới hạn không bao gồm nguồn, tải, dây kết nối rời hoặc mạch nạp. Bố trí vật lý phải ưu tiên khả năng lắp ráp, đo kiểm và tản nhiệt hơn việc thu nhỏ kích thước.

## 10. Lắp đặt

- **PR-INST-01:** Thiết bị được thiết kế để sử dụng trên bàn thí nghiệm trong môi trường trong nhà, khô ráo và thông thoáng.
- **PR-INST-02:** PCB phải được cố định bằng lỗ bắt vít và chân đỡ cách điện hoặc đặt trong vỏ phù hợp; không được đặt trực tiếp lên bề mặt dẫn điện.
- **PR-INST-03:** Cổng nguồn vào và cổng tải ra phải dễ tiếp cận, được phân biệt rõ ràng và có ký hiệu cực tính.
- **PR-INST-04:** Thiết bị chỉ được kết nối với nguồn DC thấp áp trong miền cho phép, không được nối trực tiếp với điện lưới AC.
- **PR-INST-05:** Việc đấu nối không yêu cầu dụng cụ chuyên dụng ngoài dụng cụ cơ bản phù hợp với loại đầu nối được chọn.

Loại đầu nối, số lượng lỗ bắt vít và vị trí lắp cụ thể là TBD và sẽ được xác định trong Hardware Specification.

## 11. Phương pháp kiểm chứng dự kiến

| Nhóm yêu cầu | Phương pháp dự kiến |
| --- | --- |
| Đầu vào/đầu ra và chức năng | Kiểm tra tài liệu thiết kế và thử nghiệm tích hợp |
| Đo `Vin`, `Vload`, `Iload` | So sánh với thiết bị đo tham chiếu tại nhiều điểm trong miền hoạt động |
| Dòng liên tục, dòng đỉnh và sụt áp | Tạo tải có kiểm soát và đo bằng đồng hồ cùng oscilloscope |
| Phản ứng bảo vệ nhanh | Tạo sự kiện lỗi có giới hạn năng lượng và đo thời gian bằng oscilloscope |
| Cập nhật giao diện và logic ngắt | Đo thời gian sự kiện–phản ứng và quan sát trạng thái hệ thống |
| Chi phí | Tổng hợp BOM, hóa đơn PCB và linh kiện sử dụng thực tế |
| Nguồn nuôi và fail-safe | Thử đóng/ngắt nguồn, brownout, reset MCU và trạng thái công tắc tải |
| Kích thước, khối lượng và lắp đặt | Đo trực tiếp và kiểm tra bằng quan sát |

Các phép thử chi tiết, điều kiện thử, thiết bị và tiêu chí pass/fail sẽ được định nghĩa trong Test Specification.

## 12. Các vấn đề còn mở

Các quyết định sau chưa được chốt trong Product Requirement v0.1:

1. Ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép cụ thể của từng profile tải.
2. Ngưỡng tác động và dung sai của bảo vệ quá dòng nhanh.
3. Giới hạn điện áp, dòng và năng lượng tuyệt đối của phần cứng ngoài miền vận hành định mức.
4. Cơ chế phục hồi sau lỗi và giới hạn số lần tự thử lại, nếu có.
5. Độ phân giải hiển thị và tần số lấy mẫu nội bộ cần thiết.
6. Loại giao diện người dùng và phương thức nhập cấu hình.
7. Loại đầu nối nguồn và tải.
8. Điều kiện nhiệt độ, độ ẩm định lượng cho môi trường vận hành.
9. Tần suất cho phép của các sự kiện dòng đỉnh lặp lại.

Các mục này phải được giải quyết hoặc chuyển thành ràng buộc rõ ràng trong Design Specification, Hardware Specification, Software Specification hoặc Test Specification trước khi chế tạo PCB.
