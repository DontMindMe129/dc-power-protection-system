# Concept hệ thống giám sát và bảo vệ đường nguồn DC

> Trạng thái: bản cơ sở của Giai đoạn 1. Các giá trị ghi **Tạm thời** vẫn phải được xác nhận bằng tính toán, thiết kế và thử nghiệm. Nguồn truy vết trạng thái là [`decision-log.md`](decision-log.md).

## 1. Vấn đề

Khi một tải DC được nối trực tiếp với nguồn bên ngoài, người dùng không có đủ thông tin về điện áp thực tế tại hai phía, dòng điện đang chạy hoặc nguyên nhân khiến tải hoạt động bất thường. Dự án bổ sung một thiết bị trung gian để quan sát đường công suất, cung cấp thông tin cho người dùng và ngắt tải khi phát hiện điều kiện không an toàn.

## 2. Ranh giới hệ thống — Đã xác nhận

Nguồn và tải đều nằm ngoài phạm vi thiết kế. Chỉ điện áp DC thấp đi vào thiết bị; việc biến đổi điện lưới 220 V AC thành DC thuộc về adapter bên ngoài.

```text
Nguồn DC ngoài
      │
      ▼
┌────────────────────────────────────────────┐
│ Hệ thống giám sát và bảo vệ đường nguồn DC │
└────────────────────────────────────────────┘
      │
      ▼
Tải DC ngoài
```

Sản phẩm cuối của dự án phải được triển khai trên PCB, lắp ráp và kiểm thử thực tế. Breadboard hoặc module chỉ được dùng để thử nghiệm từng khối trước khi chốt PCB.

## 3. Đầu vào, đầu ra và các luồng của hệ thống — Đã xác nhận

### 3.1. Luồng công suất

- Đầu vào công suất là năng lượng do nguồn DC bên ngoài cung cấp.
- Đầu ra công suất là năng lượng DC được truyền tới tải dưới sự giám sát và có khả năng đóng hoặc ngắt.
- Tải vật lý nằm ở phía đầu ra công suất. Hành vi của tải ảnh hưởng tới điện áp và dòng điện mà hệ thống quan sát được.

```text
Nguồn DC ──> [ giám sát + quyết định + đóng/ngắt ] ──> Tải DC
```

### 3.2. Luồng thông tin

- Đầu vào thông tin gồm lệnh vận hành, cấu hình nguồn và profile tải do người dùng chọn.
- Đầu ra thông tin gồm giá trị đo, trạng thái vận hành, cảnh báo và nguyên nhân ngắt.
- Profile tải là dữ liệu mô tả miền hành vi được chấp nhận; profile không phải tải vật lý và V1 không tự nhận diện loại tải.

## 4. Mô hình đường công suất — Đã xác nhận

Thiết bị hoạt động theo kiểu truyền thẳng, không điều chỉnh điện áp đầu ra:

```text
Switch bật:  Vload ≈ Vin − Vdrop
Switch tắt:  thiết bị không chủ động truyền năng lượng từ nguồn đến tải
```

Tải phải tương thích với nguồn đang kết nối. Thay đổi cấu hình không biến đổi điện áp; cấu hình chỉ xác định miền hoạt động và cách xử lý lỗi.

Trạng thái switch tắt không bảo đảm `Vload = 0 V` ngay lập tức vì tải có thể còn điện tích, dòng rò hoặc nguồn cấp ngược.

## 5. Đại lượng giám sát — Đã xác nhận

Thiết bị cần đo:

- `Vin`: điện áp ở phía đầu vào thiết bị;
- `Vload`: điện áp tại phía tải;
- `Iload`: dòng điện được truyền từ nguồn tới tải, không bao gồm dòng tự tiêu thụ của hệ thống;
- `Vdrop = Vin − Vload`: độ sụt áp qua đường công suất khi switch đang bật.

`Vload` thấp không tự nó chứng minh tải bị ngắn mạch vì hiện tượng này cũng xuất hiện khi switch đã tắt hoặc nguồn bên ngoài bị sụt áp. Việc phân loại sự kiện phải xét đồng thời các phép đo, trạng thái switch và cấu hình đang dùng.

`Vdrop` chỉ có ý nghĩa chẩn đoán chất lượng đường công suất khi switch đang ON và `Iload` đủ lớn để sụt áp dự kiến vượt đáng kể sai số kết hợp của hai kênh điện áp. Khi dòng gần 0, hiệu `Vin − Vload` có thể chủ yếu phản ánh sai lệch giữa hai kênh đo.

## 6. Miền vận hành, cấu hình và giới hạn phần cứng

Ba lớp giới hạn phải được phân biệt:

1. **Miền vận hành định mức của sản phẩm:** miền mà sản phẩm cam kết hoạt động đúng.
2. **Giới hạn của cấu hình nguồn và tải:** do người dùng chọn và luôn phải nằm trong miền vận hành định mức.
3. **Giới hạn tuyệt đối của phần cứng:** giới hạn chịu đựng của linh kiện và PCB; không phải miền sử dụng được công bố.

### 6.1. Miền vận hành định mức hiện tại

| Thông số | Giá trị hiện tại | Trạng thái |
| --- | --- | --- |
| Điện áp hoạt động | `5–15 V DC` | Đã xác nhận |
| Dòng tải liên tục định mức | `0–1 A` | Tạm thời |
| Dòng đỉnh vận hành định mức | Trên `1 A` đến `2 A` | Tạm thời |
| Thời gian liên tục tối đa trong miền dòng đỉnh | `1 s` | Tạm thời |

`2 A/1 s` là biên trên của miền sử dụng được công bố và là điều kiện tối thiểu mà thiết kế phải đáp ứng. Nó không phải dòng hoặc thời gian phá hủy tuyệt đối của phần cứng.

### 6.2. Cấu hình nguồn và tải

Cấu hình nguồn và profile tải là hai nhóm thông tin riêng:

```text
Cấu hình nguồn:
Vin_expected_min
Vin_expected_max
I_supply_max

Profile tải:
I_warning
I_continuous_limit
I_startup_peak
Startup_duration
I_trip
Trip_delay
Reset_mode
```

Giới hạn áp dụng thực tế không được lớn hơn giá trị nhỏ nhất giữa giới hạn sản phẩm, giới hạn nguồn và giới hạn profile tải.

Khả năng cấp dòng tối đa của nguồn, `I_supply_max`, là thông tin cấu hình; hệ thống không thể suy ra giá trị này một cách đáng tin cậy chỉ từ `Vin` và `Iload`. Thiết bị cũng không được giả định nguồn bên ngoài sẽ luôn tự bảo vệ khi ngắn mạch.

### 6.3. Giới hạn tuyệt đối

Điện áp chịu đựng tuyệt đối, ngưỡng bảo vệ nhanh, dòng sự cố và giới hạn năng lượng/nhiệt của phần cứng hiện là TBD. Các giá trị này sẽ được xác định trong Hardware Specification và phải có dự phòng so với miền vận hành định mức.

## 7. Nguồn nuôi nội bộ và trạng thái fail-safe — Đã xác nhận

Hệ thống tự cấp nguồn cho khối điều khiển, đo lường và giao diện từ `Vin`. Nguồn đầu vào được chia thành:

```text
                         ┌─ bảo vệ đầu vào + nguồn nội bộ ──> MCU, đo lường, UI
Vin ── bảo vệ đầu vào ───┤
                         └─ đo dòng + công tắc công suất ───> Vload
```

Nhánh nguồn điều khiển được lấy trước công tắc tải, có bảo vệ và điều chỉnh điện áp riêng. Hệ thống không yêu cầu nguồn phụ trong vận hành bình thường.

Khi `Vin` còn hợp lệ nhưng tải bị ngắt, khối điều khiển và giao diện phải tiếp tục hoạt động. Khi nguồn điều khiển mất hoặc không ổn định, công tắc tải phải trở về trạng thái mặc định OFF bằng hành vi fail-safe của phần cứng.

Hệ thống không cam kết tiếp tục hiển thị hoặc ghi nhận sự kiện khi nguồn đầu vào đã mất hoàn toàn.

## 8. Phạm vi tải và profile V1

Tên sản phẩm không chứa từ “cấu hình”, nhưng việc hệ thống sử dụng profile do người dùng chọn là chức năng **Đã xác nhận**. Danh sách profile có trạng thái riêng:

| Profile | Nhóm tải đại diện | Đặc điểm chính | Trạng thái |
| --- | --- | --- | --- |
| `RESISTIVE` | Điện trở công suất | Dòng ổn định, gần như không có dòng khởi động | Tạm thời |
| `ELECTRONIC` | Tải điện tử/RC có tụ đầu vào | Có thể xuất hiện dòng nạp tụ ngắn khi vừa bật | Tạm thời |
| `FAN` | Quạt DC nhỏ | Có dòng khởi động và có thể xuất hiện xung dòng khi đang chạy | Tạm thời |
| `CUSTOM` | Tải do người dùng khai báo | Các ngưỡng được nhập trong miền vận hành của sản phẩm | Đề xuất chưa duyệt |

Người dùng chủ động chọn profile; V1 không tự nhận diện loại tải. Điện trở công suất hoặc tải điện tử được dùng để tạo các điểm kiểm thử có kiểm soát và lặp lại được.

Động cơ DC chổi than hoặc servo có thể được dùng làm phép thử mở rộng nếu điện áp, dòng khởi động và hành vi năng lượng nằm trong miền phần cứng, nhưng V1 chưa cam kết hỗ trợ chung cho các tải đó.

### 8.1. Mô hình tải điện tử/RC

Trong một mô hình tải có nhánh điện trở và tụ đầu vào, dòng mà hệ thống truyền tới tải trong giai đoạn nạp có thể được mô tả:

```text
Iload = IR + IC
```

Khi switch OFF, dòng xả của tụ có thể chủ yếu tuần hoàn bên trong tải nên cảm biến trên đường truyền nguồn–tải có thể đọc `Iload ≈ 0` trong khi `Vload` vẫn khác 0. Vì vậy `Vload` là đại lượng phù hợp hơn để quan sát điện áp hoặc năng lượng còn lại phía tải sau khi ngắt.

Điện dung tải tối đa, giới hạn inrush và việc dùng soft-start/current-limit vẫn là TBD.

### 8.2. Mô hình quạt DC

Đối với quạt, cần phân biệt:

- dòng khởi động sau khi đóng switch;
- xung dòng ngắn xuất hiện trong runtime rồi trở về bình thường;
- dòng cao kéo dài do tải cơ lớn, kẹt rotor hoặc tình trạng bất thường khác.

Dòng khởi động và xung runtime có thể cùng chịu trần dòng đỉnh của sản phẩm nhưng thời gian cho phép không nhất thiết giống nhau. Các thời gian cụ thể thuộc profile và hiện là TBD.

## 9. Các tình trạng cần quan sát

Các nhóm sự kiện cần được xem xét gồm:

- `Vin` nằm ngoài miền hợp lệ của cấu hình nguồn;
- `Vdrop` quá lớn khi switch đang bật;
- `Iload` vượt ngưỡng trong một khoảng thời gian;
- dòng tăng rất nhanh do quá tải nặng hoặc ngắn mạch;
- nguồn bên ngoài sụt áp, giới hạn dòng, tự ngắt hoặc khởi động lại theo chu kỳ;
- điện áp đầu ra không phù hợp với trạng thái switch mong đợi;
- MCU mất nguồn hoặc reset trong khi đang xảy ra lỗi.

Bảo vệ trước dòng có khả năng phá hủy phải có đường tác động phần cứng và không được phụ thuộc hoàn toàn vào firmware. Khi MCU mất nguồn hoặc reset, đường công suất phải mặc định OFF.

## 10. Những điều chưa xác định

Các quyết định sau vẫn còn mở:

1. Giá trị cuối cùng của dòng liên tục `1 A`, dòng đỉnh `2 A` và giới hạn `1 s` sau khi tính toán nhiệt và chọn linh kiện.
2. Ngưỡng tác động, dung sai và hành vi latch của bảo vệ quá dòng nhanh.
3. Ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép cụ thể của từng profile.
4. Profile nào bắt buộc phải được trình diễn trong V1.
5. Đầu nối nguồn và tải sử dụng loại nào.
6. Những lỗi nào chỉ cảnh báo và những lỗi nào phải ngắt tải.
7. Cơ chế phục hồi sau trip và giới hạn số lần tự thử lại, nếu có.
8. Loại giao diện và các trường cấu hình mà người dùng có thể thay đổi.
9. Miền đo mở rộng phía trên `15 V` để phát hiện quá áp và giới hạn điện áp chịu đựng tuyệt đối.
10. Điều kiện nhiệt độ, độ ẩm và tần suất dòng đỉnh lặp lại.
11. Các ràng buộc còn lại của môn học về số lớp PCB, linh kiện và thiết bị kiểm thử.
12. Đảo cực đầu vào phải được bảo vệ, chỉ cảnh báo hay nằm ngoài phạm vi.
13. Cách xử lý cảm biến/ADC mất tín hiệu hoặc trả giá trị phi lý.
14. Phạm vi phát hiện và xử lý backfeed từ tải.
15. Khả năng phát hiện switch bị chập và vẫn dẫn khi được yêu cầu OFF.
16. Quy tắc thermal derating và giới hạn lặp lại của các xung dòng đỉnh.
17. `CUSTOM` có trở thành profile bắt buộc của V1 hay không.

## 11. Chưa thuộc phạm vi V1

Các hạng mục sau chưa được đưa vào phiên bản đầu tiên:

- USB Power Delivery/PPS tích hợp trên PCB;
- tự động nhận diện khả năng cấp dòng tối đa của nguồn;
- tự động nhận diện loại tải;
- nguồn điều khiển phụ hoặc pin dự phòng;
- tải cảm mạnh, solenoid và tải có khả năng trả năng lượng lớn về đường nguồn;
- cam kết hỗ trợ chung cho mọi động cơ DC hoặc servo;
- Bluetooth, Wi-Fi hoặc ứng dụng điện thoại;
- sạc pin;
- làm việc trực tiếp với điện lưới;
- tuyên bố hoạt động với mọi nguồn và mọi loại tải DC.

USB Type-C mới chỉ được cân nhắc như một loại đầu nối. Nếu dùng Type-C để nhận điện áp cao hơn mức USB mặc định, thiết kế phải có cơ chế thương lượng nguồn phù hợp; jack DC hoặc terminal vẫn là các phương án đơn giản hơn cho prototype.
