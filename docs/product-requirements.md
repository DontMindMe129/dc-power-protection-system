# Yêu cầu sản phẩm

> Phiên bản: 0.2
>
> Giai đoạn: Xác định sản phẩm
>
> Trạng thái: Bản cơ sở đang được hoàn thiện
>
> Nguồn trạng thái quyết định: [`decision-log.md`](decision-log.md)

Tài liệu này mô tả sản phẩm phải làm gì và các ràng buộc có thể quan sát hoặc kiểm chứng ở biên hệ thống. Cách phân rã khối, lựa chọn linh kiện và topology chi tiết thuộc Design Specification hoặc Hardware Specification.

Các trạng thái được sử dụng:

- **Đã xác nhận:** yêu cầu hiện hành đã được người dùng lựa chọn;
- **Tạm thời:** baseline đã được người dùng chấp nhận để tiếp tục thiết kế nhưng còn có thể thay đổi sau tính toán hoặc thử nghiệm;
- **Đề xuất chưa duyệt:** đã có phương án hoặc giá trị đề xuất nhưng người dùng chưa chấp nhận;
- **TBD:** chưa có giá trị đủ căn cứ để đề xuất hoặc chưa chọn được phương án.

## 1. Tên sản phẩm

- Tiếng Việt: **Hệ thống giám sát và bảo vệ đường nguồn DC**.
- Tiếng Anh: **DC Power Path Monitoring and Protection System**.

## 2. Mục đích

Thiết bị được mắc giữa nguồn DC bên ngoài và tải DC nhằm giám sát điện áp đầu vào, điện áp tại tải và dòng điện trên đường công suất. Dựa trên các đại lượng đo được, thiết bị phát hiện trạng thái nguồn bất thường, sụt áp quá mức hoặc quá dòng tải; cung cấp thông tin, cảnh báo và tự động ngắt tải khi cần thiết theo cấu hình vận hành đã chọn.

Thiết bị chỉ thực hiện chức năng giám sát và bảo vệ; không điều chỉnh điện áp đầu ra và không thay thế nguồn DC bên ngoài.

## 3. Đầu vào và đầu ra

### 3.1. Đầu vào

- **PR-IO-01 — Đầu vào công suất:** năng lượng điện do nguồn DC bên ngoài cung cấp.
- **PR-IO-02 — Đầu vào thông tin:** lệnh vận hành, cấu hình nguồn, profile tải, ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép do người dùng cung cấp. Mọi giá trị cấu hình phải nằm trong miền vận hành định mức.

### 3.2. Đầu ra

- **PR-IO-03 — Đầu ra công suất:** năng lượng DC được truyền từ nguồn đến tải dưới sự giám sát và có khả năng đóng hoặc ngắt bảo vệ. Điện áp đầu ra không được điều chỉnh mà bám theo điện áp đầu vào, trừ phần sụt áp trên thiết bị.
- **PR-IO-04 — Đầu ra thông tin:** giá trị đo, trạng thái vận hành, cảnh báo và nguyên nhân ngắt mà hệ thống xác định được.

### 3.3. Quan hệ công suất

```text
Khi đường công suất đóng: Vload ≈ Vin − Vdrop
Khi đường công suất ngắt: thiết bị không chủ động truyền năng lượng từ nguồn đến tải
```

Nguồn và tải là các đối tượng bên ngoài hệ thống. Profile tải là đầu vào thông tin do người dùng chọn, không phải tải vật lý.

## 4. Trường hợp sử dụng

Phạm vi của bảy use case dưới đây đã được chấp nhận làm baseline tạm thời. Các chính sách còn TBD được ghi trực tiếp trong từng use case.

### UC-01 — Thiết lập hệ thống

- **Actor:** người dùng.
- **Trigger:** người dùng chuẩn bị hệ thống cho một nguồn và tải cụ thể.
- **Precondition:** đường công suất đang OFF; thiết bị được lắp đặt trong điều kiện an toàn.
- **Basic flow:**
  1. Người dùng kết nối nguồn và tải tương thích.
  2. Người dùng chọn hoặc nhập cấu hình nguồn và profile tải.
  3. Hệ thống kiểm tra các giá trị có nằm trong miền vận hành định mức hay không.
  4. Hệ thống giữ đầu ra OFF và báo cấu hình đã sẵn sàng.
- **Alternate/error flow:** cấu hình vượt miền sản phẩm hoặc không đầy đủ phải bị từ chối; `Vin` không hợp lệ thì đầu ra tiếp tục OFF.
- **Postcondition:** một cấu hình hợp lệ đã được chọn và tải chưa được cấp điện.

### UC-02 — Cấp điện và giám sát tải bình thường

- **Actor:** người dùng.
- **Trigger:** người dùng yêu cầu bật đầu ra.
- **Precondition:** cấu hình hợp lệ; không có lỗi đang latch; `Vin` hợp lệ; các phép đo cần thiết đã sẵn sàng và không phi lý.
- **Basic flow:**
  1. Hệ thống kiểm tra điều kiện cho phép đóng.
  2. Hệ thống đóng đường công suất.
  3. Hệ thống đo `Vin`, `Vload`, `Iload` và cập nhật trạng thái.
  4. Hệ thống tiếp tục cấp điện khi các đại lượng nằm trong giới hạn của cấu hình.
- **Alternate/error flow:** yêu cầu ON bị từ chối nếu cấu hình không hợp lệ, `Vin` ngoài miền, đang có lỗi latch hoặc phép đo không đáng tin cậy. Nếu bất thường xuất hiện sau khi đóng, hệ thống chuyển sang UC-04 hoặc UC-05.
- **Postcondition:** đường công suất ON và được giám sát, hoặc vẫn OFF kèm lý do từ chối.

### UC-03 — Tải có dòng khởi động hoặc dòng đỉnh hợp lệ

- **Actor:** tải; người dùng quan sát kết quả.
- **Trigger:** `Iload` vượt dòng liên tục sau khi bật tải hoặc trong một sự kiện ngắn hạn.
- **Precondition:** profile đang chọn cho phép một miền dòng đỉnh; đường công suất đang ON.
- **Basic flow:**
  1. Hệ thống ghi nhận biên độ và thời gian của dòng đỉnh.
  2. Hệ thống so sánh sự kiện với giới hạn của profile và miền sản phẩm.
  3. Nếu dòng trở về miền liên tục trong thời gian cho phép, hệ thống tiếp tục cấp điện.
- **Alternate/error flow:** dòng vượt biên độ hoặc thời gian cho phép dẫn tới cảnh báo hoặc UC-05 tùy loại lỗi.
- **Postcondition:** hệ thống trở lại vận hành bình thường hoặc đã ngắt bảo vệ.

### UC-04 — Phát hiện điều kiện bất thường

- **Actor:** hệ thống; người dùng nhận thông tin.
- **Trigger:** một hoặc nhiều giá trị `Vin`, `Vload`, `Iload`, `Vdrop` hoặc trạng thái switch không phù hợp với cấu hình.
- **Precondition:** chức năng giám sát đang hoạt động.
- **Basic flow:**
  1. Hệ thống đánh giá đồng thời các phép đo, trạng thái switch, thời gian và cấu hình.
  2. Hệ thống phân loại sự kiện ở mức có thể chứng minh từ dữ liệu hiện có.
  3. Hệ thống cảnh báo hoặc yêu cầu ngắt tùy chính sách của lỗi.
- **Alternate/error flow:** nếu cảm biến hoặc phép đo phi lý, hệ thống không được đưa ra chẩn đoán nguyên nhân chắc chắn; chính sách cảnh báo/ngắt cho lỗi cảm biến hiện là TBD.
- **Postcondition:** bất thường được cảnh báo, chuyển sang UC-05 hoặc được ghi nhận là chưa thể phân loại.

### UC-05 — Ngắt bảo vệ

- **Actor:** đường bảo vệ phần cứng hoặc logic bảo vệ firmware.
- **Trigger:** lỗi nguy hiểm được phát hiện hoặc một điều kiện có thời gian cho phép đã hết hạn.
- **Precondition:** đường công suất đang ON hoặc đang trong quá trình đóng.
- **Basic flow:**
  1. Hệ thống yêu cầu ngắt đường truyền năng lượng tới tải.
  2. Công tắc công suất được giữ ở trạng thái OFF.
  3. Hệ thống ghi nhận và hiển thị nguyên nhân nếu nguồn điều khiển còn hợp lệ.
- **Alternate/error flow:** nếu `Vin` sụt làm điều khiển mất nguồn, trạng thái fail-safe phải giữ đầu ra OFF; `Vload` có thể chưa về 0 do điện tích, dòng rò hoặc backfeed.
- **Postcondition:** hệ thống không chủ động truyền năng lượng từ nguồn đến tải và không tự đóng lại ngoài chính sách phục hồi.

### UC-06 — Phục hồi sau lỗi

- **Actor:** người dùng; khả năng auto-retry chưa được chọn.
- **Trigger:** người dùng yêu cầu phục hồi sau khi đã loại bỏ nguyên nhân lỗi.
- **Precondition:** đường công suất OFF và các điều kiện cần thiết có thể được kiểm tra lại.
- **Basic flow:**
  1. Hệ thống kiểm tra `Vin`, cấu hình, trạng thái lỗi và các phép đo liên quan.
  2. Nếu điều kiện hợp lệ, hệ thống cho phép quay lại UC-02.
- **Alternate/error flow:** nếu lỗi còn tồn tại hoặc phép đo không hợp lệ, hệ thống tiếp tục giữ OFF và thông báo lý do.
- **Postcondition:** hệ thống sẵn sàng đóng lại hoặc tiếp tục ở trạng thái ngắt.
- **TBD:** phục hồi thủ công, tự động, thời gian chờ và giới hạn số lần thử.

### UC-07 — Thay đổi nguồn hoặc tải

- **Actor:** người dùng.
- **Trigger:** người dùng muốn sử dụng nguồn hoặc tải khác.
- **Precondition:** đường công suất OFF.
- **Basic flow:**
  1. Người dùng thay đổi kết nối vật lý.
  2. Người dùng chọn lại cấu hình nguồn và profile tải.
  3. Hệ thống kiểm tra cấu hình mới trước khi cho phép ON.
- **Alternate/error flow:** cấu hình vượt miền sản phẩm phải bị từ chối hoặc giới hạn.
- **Postcondition:** cấu hình mới hợp lệ đã được chọn và đầu ra vẫn OFF cho tới khi có lệnh bật.

## 5. Yêu cầu chức năng

### FR-01 — Điều khiển đường công suất

Hệ thống phải có khả năng cho phép hoặc ngăn năng lượng truyền từ nguồn DC đến tải theo lệnh vận hành và trạng thái bảo vệ.

### FR-02 — Đo điện áp đầu vào

Hệ thống phải đo `Vin` tại phía nguồn để xác định trạng thái điện áp do nguồn bên ngoài cung cấp.

### FR-03 — Đo điện áp tại tải

Hệ thống phải đo `Vload` tại phía tải khi đường công suất đang đóng và khi đã bị ngắt.

### FR-04 — Đo dòng tải

Hệ thống phải đo `Iload` được truyền từ nguồn đến tải. Giá trị này không bao gồm dòng tự tiêu thụ của hệ thống.

### FR-05 — Đánh giá trạng thái đường công suất

Hệ thống phải:

- so sánh `Vin`, `Vload` và `Iload` với cấu hình vận hành;
- xác định `Vdrop = Vin − Vload` khi điều kiện sử dụng phép tính này hợp lệ;
- phân biệt trạng thái bình thường, cảnh báo và lỗi;
- phát hiện điện áp đầu ra không phù hợp với trạng thái đóng hoặc ngắt mong đợi.

### FR-06 — Quản lý cấu hình vận hành

Hệ thống phải hỗ trợ cấu hình nguồn và profile tải do người dùng chọn. Profile có thể xác định ngưỡng cảnh báo, ngưỡng ngắt và khoảng thời gian cho phép đối với hiện tượng quá độ.

Giới hạn áp dụng thực tế không được lớn hơn giá trị nhỏ nhất giữa giới hạn sản phẩm, giới hạn nguồn và giới hạn profile tải. Hệ thống phải từ chối hoặc giới hạn cấu hình vượt ngoài miền vận hành định mức.

Danh sách profile và trạng thái phê duyệt của từng profile được quản lý trong `concept.md` và `decision-log.md`; `FR-06` không mặc định biến một profile đề xuất thành yêu cầu bắt buộc.

### FR-07 — Xử lý dòng khởi động và dòng đỉnh

Hệ thống phải cho phép tải có dòng ngắn hạn cao hơn dòng liên tục trong giới hạn về biên độ và thời gian của profile tải, nhằm tránh ngắt nhầm đối với tải hợp lệ.

### FR-08 — Cảnh báo bất thường

Khi phát hiện điều kiện bất thường nhưng chưa cần ngắt tức thời, hệ thống phải thông báo cho người dùng bằng trạng thái hoặc cảnh báo phù hợp.

### FR-09 — Ngắt bảo vệ

Khi phát hiện điều kiện nguy hiểm, hệ thống phải ngắt đường truyền năng lượng từ nguồn đến tải và ghi nhận nguyên nhân nếu nguồn điều khiển còn hợp lệ. Bảo vệ trước quá dòng nghiêm trọng hoặc ngắn mạch không được phụ thuộc hoàn toàn vào tốc độ xử lý của firmware.

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
| PR-PERF-09 | Phản ứng với lỗi quá dòng nghiêm trọng | `≤ 100 µs` | Tạm thời |
| PR-PERF-10 | Độ trễ ngắt sau khi hết thời gian cho phép | `≤ 10 ms` | Tạm thời |
| PR-PERF-11 | Sụt áp tại dòng liên tục `1 A` | `≤ 0,2 V` | Tạm thời |
| PR-PERF-12 | Sụt áp tại dòng đỉnh `2 A` | `≤ 0,4 V` | Tạm thời |

### 6.2. Miền vận hành dòng điện định mức

Hệ thống phải hỗ trợ dòng tải liên tục từ `0–1 A`. Dòng trên `1 A` đến `2 A` được phép tồn tại trong khoảng thời gian do profile tải quy định nhưng không quá `1 s`. Trong miền vận hành này, hệ thống phải hoạt động đúng chức năng và không hư hỏng nếu cấu hình đang chọn cho phép điều kiện đó.

Dòng vượt `2 A` hoặc vượt giới hạn thời gian nằm ngoài miền vận hành định mức và phải được xử lý bởi chức năng bảo vệ. `2 A/1 s` không phải giới hạn phá hủy tuyệt đối của linh kiện. Ngưỡng bảo vệ nhanh, dung sai và giới hạn chịu đựng tuyệt đối hiện là TBD.

### 6.3. Thời gian phản ứng bảo vệ

Hai giá trị `100 µs` và `10 ms` được giữ làm baseline. Để biến chúng thành tiêu chí pass/fail hoàn chỉnh, Test Specification phải chốt:

- điểm bắt đầu của `100 µs`: thời điểm dòng vật lý vượt ngưỡng, thời điểm comparator đổi trạng thái hay thời điểm fault tới logic;
- điểm kết thúc của `100 µs`: gate chuyển OFF, switch bắt đầu ngắt hay `Iload` giảm dưới một mức xác định;
- điểm bắt đầu và kết thúc tương ứng của ngân sách `10 ms`;
- mức dòng hoặc điện áp được dùng để kết luận đường công suất đã ngắt.

Các điểm đo trên hiện là TBD; chưa được phép tuyên bố đã kiểm chứng hai yêu cầu thời gian chỉ dựa trên mô phỏng logic.

### 6.4. Sụt áp đường công suất

Các giới hạn sụt áp được đánh giá giữa đầu nối vào và đầu nối ra của PCB, không bao gồm dây dẫn bên ngoài. `Vdrop` chỉ được dùng để đánh giá chất lượng đường công suất khi:

- switch đang ON;
- `Iload` đủ lớn để sụt áp dự kiến vượt đáng kể sai số kết hợp của hai kênh điện áp.

Khi dòng gần 0, `Vin − Vload` có thể chủ yếu phản ánh sai số giữa hai kênh đo; ngưỡng `Iload` tối thiểu để sử dụng chẩn đoán `Vdrop` hiện là TBD.

### 6.5. Độ phân giải và độ chính xác

Độ phân giải hiển thị không được dùng để thay thế yêu cầu về độ chính xác. Độ phân giải hiển thị và tần số lấy mẫu nội bộ hiện là TBD.

Mạch đo `Vin` phải có khả năng nhận biết điện áp nằm phía trên giới hạn hoạt động `15 V` để hệ thống không cấp điện cho tải trong điều kiện quá áp. Miền phát hiện quá áp, độ chính xác ngoài miền hoạt động và giới hạn điện áp chịu đựng tuyệt đối hiện là TBD.

## 7. Chi phí sản xuất

| Mã | Hạng mục | Mục tiêu hiện tại | Trạng thái |
| --- | --- | --- | --- |
| PR-COST-01 | BOM cho một bo mạch | `≤ 600.000 VNĐ` | Tạm thời |
| PR-COST-02 | Một prototype hoàn chỉnh gồm PCB và linh kiện | `≤ 1.000.000 VNĐ` | Tạm thời |

Giá thành không bao gồm nguồn DC bên ngoài, tải kiểm thử, mạch nạp/debugger, thiết bị đo, chi phí nghiên cứu và linh kiện mua dư.

## 8. Nguồn nuôi hệ thống

- **PR-PWR-01 — Đã xác nhận:** Khi `Vin` còn hợp lệ, khối điều khiển, đo lường và giao diện phải tiếp tục hoạt động dù đường cấp điện tới tải đã bị ngắt.
- **PR-PWR-02 — Đã xác nhận:** Khi nguồn nuôi điều khiển mất hoặc không ổn định, đường công suất phải mặc định OFF.
- **PR-PWR-03 — Đã xác nhận:** Hệ thống không yêu cầu một nguồn phụ bên ngoài trong vận hành bình thường.
- **PR-PWR-04 — Đề xuất chưa duyệt:** Mục tiêu công suất tự tiêu thụ của thiết bị là `≤ 1 W` trong miền điện áp hoạt động.

Cách lấy nguồn trước switch, bảo vệ nhánh điều khiển và tạo điện áp nội bộ được ghi trong `concept.md`; topology triển khai cuối cùng thuộc Design/Hardware Specification.

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

Loại đầu nối, số lượng lỗ bắt vít và vị trí lắp cụ thể là TBD.

## 11. Phương pháp kiểm chứng dự kiến

| Nhóm yêu cầu | Phương pháp kỹ thuật dự kiến | Tính khả thi hiện tại |
| --- | --- | --- |
| Đầu vào/đầu ra và chức năng | Kiểm tra tài liệu thiết kế và thử nghiệm tích hợp | Có thể thực hiện trên prototype |
| Đo `Vin`, `Vload`, `Iload` | So sánh với thiết bị đo tham chiếu tại nhiều điểm | Thiết bị tham chiếu cụ thể: TBD |
| Dòng liên tục, dòng đỉnh và sụt áp | Tạo tải có kiểm soát và đo điện áp/dòng | Giá trị tải và thiết bị cụ thể: TBD |
| Phản ứng bảo vệ nhanh | Tạo lỗi có giới hạn năng lượng và đo dạng sóng | Cần thiết bị đủ băng thông; khả năng tiếp cận: TBD |
| Cập nhật giao diện và logic ngắt | Đo thời gian sự kiện–phản ứng | Điểm đo cần chốt trong Test Specification |
| Chi phí | Tổng hợp BOM, hóa đơn PCB và linh kiện | Thực hiện sau khi có BOM |
| Nguồn nuôi và fail-safe | Thử đóng/ngắt nguồn, brownout và reset MCU | Cần nguồn thử điều chỉnh được; thiết bị cụ thể: TBD |
| Kích thước, khối lượng và lắp đặt | Đo trực tiếp và kiểm tra bằng quan sát | Có thể thực hiện trên prototype |

Nếu không tiếp cận được thiết bị đo đủ băng thông, yêu cầu `100 µs` vẫn được giữ nhưng chưa thể tuyên bố đã kiểm chứng trực tiếp. Các test case, điều kiện thử, thiết bị và tiêu chí pass/fail chi tiết thuộc Test Specification.

## 12. Traceability cấp cao

| Use case | Yêu cầu liên quan | Specification tương lai | Nhóm kiểm chứng |
| --- | --- | --- | --- |
| UC-01 | PR-IO-02, FR-06, FR-12 | Software/UI Specification | Kiểm tra cấu hình và giới hạn |
| UC-02 | FR-01 đến FR-05, FR-12, PR-PERF-05 đến PR-PERF-08 | Hardware + Software Specification | Đo lường và thử nghiệm tích hợp |
| UC-03 | FR-04, FR-07, FR-09, PR-PERF-02 đến PR-PERF-04 | Hardware + Software Specification | Tải có dòng đỉnh |
| UC-04 | FR-02 đến FR-05, FR-08 | Software + Test Specification | Fault injection và kiểm tra cảnh báo |
| UC-05 | FR-01, FR-09, FR-10, PR-PERF-09, PR-PERF-10 | Hardware + Software Specification | Ngắn mạch/quá dòng có giới hạn năng lượng |
| UC-06 | FR-10, FR-11 | Software Specification | Thử phục hồi và interlock |
| UC-07 | FR-06, FR-12 | Software/UI Specification | Kiểm tra thay đổi cấu hình |

## 13. Các vấn đề còn mở

1. Ngưỡng cảnh báo, ngưỡng ngắt và thời gian cụ thể của từng profile.
2. Ngưỡng, dung sai, điểm đo thời gian và hành vi latch của bảo vệ nhanh.
3. Giới hạn điện áp, dòng, năng lượng và nhiệt tuyệt đối của phần cứng.
4. Cơ chế phục hồi sau lỗi và giới hạn số lần tự thử lại.
5. Độ phân giải hiển thị và tần số lấy mẫu nội bộ.
6. Loại giao diện người dùng và phương thức nhập cấu hình.
7. Loại đầu nối nguồn và tải.
8. Điều kiện nhiệt độ, độ ẩm và quy tắc thermal derating.
9. Tần suất cho phép của các sự kiện dòng đỉnh lặp lại.
10. Ngưỡng `Iload` tối thiểu để sử dụng `Vdrop` cho chẩn đoán.
11. Phạm vi xử lý đảo cực đầu vào.
12. Hành vi khi điện áp vượt miền đo hoặc miền chịu đựng.
13. Hành vi khi cảm biến/ADC mất tín hiệu hoặc trả giá trị phi lý.
14. Phạm vi phát hiện và xử lý backfeed từ tải.
15. Khả năng phát hiện switch bị chập và vẫn dẫn khi yêu cầu OFF.
16. Công suất tự tiêu thụ cuối cùng; `≤ 1 W` hiện chỉ là đề xuất chưa duyệt.
17. Thiết bị kiểm thử cụ thể và khả năng tiếp cận phòng thí nghiệm.
18. `CUSTOM` có trở thành profile bắt buộc của V1 hay không.

Mỗi fault chưa quyết định phải được phân loại trong các specification sau là: phải bảo vệ, chỉ cảnh báo, phát hiện nhưng không xử lý, hoặc nằm ngoài phạm vi V1.
