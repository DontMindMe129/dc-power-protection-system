# Đặc tả thiết kế hệ thống

> Trạng thái: bản nháp kiến trúc chức năng v0.1, tổng hợp từ các sơ đồ của nhóm. Các thông số baseline trong Product Requirements vẫn giữ trạng thái tạm thời; tài liệu này chưa chốt linh kiện hoặc cách hiện thực phần cứng.

## 1. Phạm vi và biên hệ thống

![Context diagram](diagrams/system-architecture-system-context.svg)

Thiết bị nằm giữa nguồn DC ngoài và tải DC ngoài, giám sát đường công suất và có khả năng ngắt tải. Nguồn, tải và người dùng nằm ngoài biên hệ thống. Thiết bị không điều chỉnh điện áp công suất đầu ra thành một giá trị cố định.

Yêu cầu sản phẩm được mô tả tại [Product Requirements](product-requirements.md). Bản vẽ có thể chỉnh sửa nằm trong [system-architecture.drawio](diagrams/system-architecture.drawio); mỗi page tương ứng với một SVG được nhúng trong tài liệu này.

## 2. Kiến trúc tổng thể

![System block diagram](diagrams/system-architecture-system-block-diagram.svg)

Đường công suất đi qua Input Protection, Current Sensing và Power Switch trước khi đến tải. Nguồn điều khiển lấy từ nút sau Input Protection và trước Power Switch, nên việc ngắt tải không chủ động ngắt nguồn điều khiển. Nếu nguồn DC ngoài mất hoặc sụt dưới khả năng vận hành của nguồn nội bộ, hệ thống vẫn có thể mất nguồn.

Sơ đồ phân biệt đường công suất, đường nguồn nuôi điện tử và đường tín hiệu. Các đường cấp nguồn tới từng khối được lược bỏ để dễ đọc; việc phân phối rail cụ thể được xác định trong Hardware Specification.

Hai nút đo điện áp được định nghĩa trong kiến trúc hiện tại:

- `V_in`: sau Input Protection, trước Current Sensing và Power Switch.
- `V_load`: đầu ra hệ thống sau Power Switch, đo so với Power Return chung.

Khi đường công suất ON, `V_drop = V_in − V_load` phản ánh sụt áp giữa hai nút này. Khi switch OFF hoặc điện áp đang chuyển tiếp, không dùng hiệu điện áp đó để kết luận về tổn hao đường dẫn như khi hoạt động ổn định.

## 3. Các khối chức năng

### 3.1. Input Protection

![Input Protection](diagrams/system-architecture-input-protection.svg)

Khối tiếp nhận nguồn ngoài, cô lập dòng sự cố, bảo vệ ngược cực và hạn chế xung điện áp. Đầu ra là `Protected V_in`, chưa phải điện áp được ổn áp. Transient Suppression là nhánh song song về Power Return. Thứ tự và topology linh kiện cụ thể, mức kẹp xung, giới hạn năng lượng và phương thức cô lập dòng được xác định khi thiết kế phần cứng.

Khối này bảo vệ đầu vào; bảo vệ quá dòng tải bằng phần cứng thuộc Hardware Overcurrent Protection, còn quản lý quá tải theo profile thuộc firmware.

### 3.2. Internal Control Power Supply

![Internal Control Power Supply](diagrams/system-architecture-internal-control-power-supply.svg)

Khối nhận `Protected V_in`, tạo rail 5 V và từ đó tạo rail 3.3 V để cấp cho các khối điện tử. Sơ đồ mô tả chức năng regulation, chưa khóa công nghệ converter. Cách hiện thực phải xét điện áp thực tế sau tổn hao Input Protection trên toàn miền nguồn ngoài 5–15 V.

Hai interface nguồn là `V_CTRL_5V` và `V_CTRL_3V3`. Rail nào cấp cho MCU/module, display, sensing và protection còn phụ thuộc phần cứng được chọn. Tụ và bộ lọc cục bộ cần cho regulator thuộc thiết kế chi tiết của khối.

### 3.3. Current Sensing

Khối quan sát dòng `I_load` trên đường cấp tải và cung cấp thông tin dòng tới MCU và Hardware Overcurrent Protection. Đây là một output thông tin chức năng có hai consumer; chưa khẳng định số tín hiệu vật lý, định dạng analog/digital hoặc công nghệ cảm biến.

Khối cung cấp thông tin, không quyết định ngắt switch. Interface dành cho bảo vệ phải cho phép xử lý độc lập với firmware và đáp ứng yêu cầu phản ứng của đường bảo vệ. Không cần diagram con ở mức kiến trúc hiện tại.

### 3.4. Voltage Sensing

Hai kênh quan sát `V_in` và `V_load` cung cấp thông tin riêng tới MCU. Chúng lấy mẫu điện áp tại hai nút, không mắc nối tiếp để mang toàn bộ dòng tải. Phương pháp đo, scaling, filtering và loại interface được xác định trong Hardware Specification. Không cần diagram con cho hai kênh này.

### 3.5. Hardware Overcurrent Protection

![Hardware Overcurrent Protection](diagrams/system-architecture-hardware-overcurrent-protection.svg)

Khối phát hiện quá dòng nghiêm trọng từ Current Information mà không chờ firmware. Fault Memory giữ trạng thái lỗi, phát yêu cầu shutdown tới Power Switch và báo trạng thái trực tiếp cho MCU.

Fault Priority nghĩa là khi điều kiện phát hiện lỗi và yêu cầu clear đồng thời tồn tại, trạng thái lỗi được giữ. Việc xóa lỗi chỉ giải phóng yêu cầu shutdown; không tự tạo yêu cầu enable tải.

Ngưỡng phát hiện, độ trễ, trạng thái latch khi mất nguồn và cơ chế khởi tạo lại được xác định ở đặc tả thành phần. Không giả định Fault Memory giữ dữ liệu sau khi mất nguồn.

### 3.6. Power Switch

![Power Switch](diagrams/system-architecture-power-switch.svg)

Switch Control tiếp nhận yêu cầu enable từ MCU và yêu cầu shutdown từ bảo vệ phần cứng. Shutdown có quyền ưu tiên; Power Switching Element thực hiện đóng/ngắt công suất theo kết quả phân xử.

Yêu cầu fail-safe là mặc định OFF khi MCU reset, mất nguồn điều khiển hoặc chưa có yêu cầu enable hợp lệ. Cơ chế điện bảo đảm hành vi này sẽ được xác định trong Hardware Specification. Loại phần tử đóng/ngắt, dòng rò khi OFF, khả năng chặn dòng ngược và tổn hao khi ON chưa được chốt tại đây.

### 3.7. MCU và giao diện người dùng

MCU nhận thông tin đo, trạng thái lỗi phần cứng và thao tác người dùng. Firmware giám sát miền vận hành theo cấu hình/profile, quản lý trạng thái và phục hồi, phát yêu cầu enable/clear và cập nhật display. Firmware không thay thế đường bảo vệ quá dòng nghiêm trọng bằng phần cứng.

Buttons nhận thao tác chọn profile, điều khiển đầu ra và yêu cầu phục hồi. Display hiển thị `V_in`, `V_load`, `I_load`, trạng thái và nguyên nhân lỗi. UART phục vụ debug, kiểm thử và logging, không bắt buộc cho vận hành thông thường; buzzer không bắt buộc trong v0.1.

Không cần diagram con cho MCU, Buttons hoặc Display tại bước này. Phân rã firmware, state machine và luồng UI được mô tả trong Software Specification.

## 4. Interface chức năng

Tên chuẩn trong bảng dưới đây dùng để mô tả interface; các nhãn khác kiểu chữ trong bản vẽ hiện có được hiểu theo cùng ý nghĩa. Tên không quy định mức logic, cực tính hoặc chân linh kiện.

| Interface | Khối gửi | Khối nhận | Ý nghĩa |
| --- | --- | --- | --- |
| `V_IN_INFORMATION` | V_in Sensing | MCU | Thông tin điện áp đầu vào nội bộ |
| `V_LOAD_INFORMATION` | V_load Sensing | MCU | Thông tin điện áp đầu ra |
| `CURRENT_INFORMATION` | Current Sensing | MCU, HW Overcurrent Protection | Thông tin dòng tải; trong sơ đồ tổng thể ghi I_load Information |
| `MCU_ENABLE` | MCU | Power Switch | Yêu cầu đóng đường công suất |
| `SHUTDOWN_REQUEST` | HW Overcurrent Protection | Power Switch | Yêu cầu ngắt bắt buộc, ưu tiên hơn enable |
| `OVERCURRENT_FAULT_STATUS` | HW Overcurrent Protection | MCU | Trạng thái lỗi đã được giữ |
| `CLEAR_FAULT_REQUEST` | MCU | HW Overcurrent Protection | Yêu cầu xóa trạng thái lỗi |
| User Input | Buttons | MCU | Thao tác của người dùng |
| Display Information | MCU | Display | Giá trị đo, trạng thái và cảnh báo |
| `V_CTRL_5V`, `V_CTRL_3V3` | Internal Control Power Supply | Các khối cần nguồn | Rail nguồn điện tử; phân phối cụ thể TBD |

## 5. Các tình huống bất thường về điện

### 5.1. Hiện tượng, nguyên nhân và ảnh hưởng

Bảng dưới phân tích các hiện tượng có thể xảy ra trên nguồn, đường dẫn và tải. Việc liệt kê một hiện tượng không đồng nghĩa sản phẩm đã cam kết phát hiện hoặc bảo vệ được hiện tượng đó. Phạm vi xử lý được phân biệt riêng tại mục 5.2.

| Hiện tượng | Nguyên nhân có thể xảy ra | Biểu hiện và ảnh hưởng |
| --- | --- | --- |
| Điện áp nguồn quá cao, kéo dài | Cắm sai adapter, đặt sai điện áp nguồn điều chỉnh hoặc bộ nguồn bị lỗi | Điện áp đầu vào tăng, có thể truyền tới tải và gây quá áp cho nguồn nội bộ nếu không được hạn chế hoặc ngắt phù hợp |
| Điện áp nguồn quá thấp hoặc mất nguồn | Nguồn không đủ công suất, nguồn tự giới hạn dòng, dây cấp sụt áp hoặc nguồn bị ngắt | V_in giảm; V_load có thể giảm theo; nguồn điều khiển có thể mất ổn định hoặc mất hoàn toàn |
| Xung điện áp đầu vào | Dao động giữa điện cảm dây và tụ khi cắm nguồn, đóng/ngắt dòng hoặc nhiễu từ nguồn | Điện áp biến đổi nhanh; có thể vượt khả năng chịu đựng của linh kiện trước khi firmware phản ứng |
| Đấu ngược cực nguồn | Đấu nhầm cực dương và cực âm | Điện áp ngược xuất hiện tại đầu vào ngoài, có thể gây hỏng linh kiện hoặc dòng sự cố nếu không được chặn |
| Quá tải ở tải | Tải tiêu thụ vượt cấu hình cho phép; tải cơ tăng hoặc motor bị kẹt, tùy loại | I_load tăng, tổn hao và nhiệt tăng; nguồn có thể sụt áp |
| Ngắn mạch nhánh tải | Chập dây, đầu ra hoặc bên trong tải | I_load có thể tăng rất lớn, V_load thấp; nếu nguồn giới hạn dòng hoặc tự ngắt thì V_in cũng có thể sụt và dòng đo được có thể không đạt ngưỡng HW_OCP |
| Dòng khởi động hoặc đỉnh dòng trong vận hành | Nạp tụ đầu vào, khởi động motor hoặc tải đổi chế độ hoạt động | Dòng tăng tạm thời; có thể là hành vi hợp lệ theo profile hoặc vượt giới hạn hệ thống |
| Sụt áp quá lớn trên đường dẫn | Dòng lớn hoặc điện trở đường dẫn tăng do tiếp xúc kém, connector lỏng, phần tử công suất dẫn không tốt | V_drop tăng, tải thiếu áp; vị trí có điện trở lớn có thể phát nhiệt dù dòng chưa vượt giới hạn |
| Đường dẫn hở hoặc chập chờn | Đứt dây, mất tiếp xúc hoặc phần tử đóng/ngắt không dẫn như yêu cầu | Khi yêu cầu ON, tải có thể không nhận điện hoặc bị cấp điện gián đoạn; phép đo tại PCB không nhất thiết nhận biết mọi điểm hở ngoài PCB |
| Xung điện áp phía tải khi ngắt | Năng lượng trong điện cảm của tải hoặc dây cần đường thoát khi dòng bị ngắt | Điện áp tại nút liên quan có thể vượt miền cho phép hoặc đảo cực; bảo vệ xung đầu vào không mặc định bảo vệ được mọi nút phía tải |
| Điện áp còn lại hoặc cấp ngược từ phía tải | Tụ phía tải còn tích điện, tải có nguồn khác hoặc có khả năng trả năng lượng | V_load có thể còn khác 0 khi switch OFF; có thể xuất hiện dòng ngược nếu topology cho phép |
| Quá dòng/chập bên trong bo mạch | Lỗi lắp ráp, linh kiện hoặc rail nguồn điều khiển bị chập | Dòng đầu vào chung có thể tăng; Current Sensing đặt trên nhánh tải không quan sát trực tiếp dòng sự cố riêng của nhánh điều khiển |

Các hiện tượng trên có thể đi cùng nhau. Ngắn mạch là một nguyên nhân, quá dòng và sụt áp là những biểu hiện có thể phát sinh; chúng không phải các lỗi vật lý độc lập trong mọi trường hợp. Khi gần ổn định, V_drop xấp xỉ I_load nhân với điện trở đường dẫn, nên sụt áp lớn có thể do dòng tăng hoặc điện trở tăng.

Không kết luận nguyên nhân chỉ từ một phép đo: V_load thấp có thể do switch OFF bình thường, nguồn sụt, ngắn mạch hoặc đường dẫn hở. Phải xét vị trí đo, trạng thái switch, các đại lượng còn lại và cấu hình. Dòng khởi động/đỉnh dòng hợp lệ theo profile không mặc định là warning hoặc fault.

Tài liệu kỹ thuật tham khảo cho các nhóm hiện tượng: [TI — 11 Ways to Protect Your Power Path](https://www.ti.com/product-category/power-management/power-protection-switches-controllers/11-ways-to-protect-your-power-path.html) và [Analog Devices — Complete System Power Protection Design Guide](https://www.analog.com/en/resources/technical-articles/complete-system-power-protection-design-guide.html). Đây là tài liệu giải thích kỹ thuật, không phải danh sách chức năng sản phẩm đã được duyệt.

### 5.2. Phạm vi xử lý và phân chia trách nhiệm

Bảng này ghi nhận yêu cầu đã có, quyết định chức năng đã chốt và những cơ chế còn mở. Một chức năng có trong block diagram chưa đồng nghĩa khả năng bảo vệ đã được tính toán hoặc kiểm chứng. Không tự bổ sung chức năng bảo vệ ngoài phạm vi chỉ vì hiện tượng có trong mục 5.1.

| Tình huống | Phạm vi/trạng thái hiện tại | Phản ứng hoặc cơ chế | Khối chịu trách nhiệm và điểm còn mở |
| --- | --- | --- | --- |
| V_in gần giới hạn thấp/cao nhưng vẫn trong miền cho phép cấp điện | Warning V_in đã chốt ở mức chức năng | Khi đang ON, cảnh báo và tiếp tục cấp điện; hết cảnh báo thì xóa warning tương ứng. Không ngắt chỉ vì warning kéo dài nếu chưa đạt tiêu chí fault | MCU dựa trên V_in Sensing; miền cảnh báo, thời gian đánh giá và hysteresis TBD |
| V_in đạt tiêu chí quá thấp/quá cao của cấu hình | Fault V_in đã chốt ở mức chức năng | Giữ/ngắt đầu ra OFF, ghi nhận nguyên nhân và giữ FAULT; nguồn bình thường trở lại không tự xóa lỗi | MCU; ngưỡng và timing TBD. Chỉ áp dụng trong miền phần cứng và nguồn điều khiển còn có thể hoạt động; không thay thế bảo vệ quá áp gây hư hỏng |
| Dòng tải quá độ hợp lệ | Đã có trong FR-07 | Tiếp tục cấp điện trong miền dòng/thời gian được cấu hình cho phép; không trì hoãn bảo vệ quá dòng nghiêm trọng | MCU quản lý profile; giới hạn phần cứng và ngưỡng HW_OCP cần phối hợp |
| Quá tải nhánh tải do MCU giám sát | Yêu cầu quản lý dòng đã có; phân loại warning/fault cụ thể chưa chốt | Đánh giá theo cấu hình/profile; các miền cảnh báo và tiêu chí ngắt còn cần xác định | MCU dựa trên Current Sensing; ngưỡng, thời gian và tiêu chí phục hồi theo lỗi TBD |
| Quá dòng nghiêm trọng/ngắn mạch nhánh tải | Bảo vệ phần cứng độc lập firmware đã nằm trong phạm vi | HW_OCP phát hiện, giữ lỗi và cưỡng bức Power Switch OFF; báo trạng thái cho MCU | HW_OCP + Power Switch; ngưỡng, độ trễ và miền sự cố xử lý được TBD. Khi nguồn tự giới hạn dòng thấp, cần xét thêm thông tin điện áp và logic hệ thống; không bảo đảm HW_OCP luôn nhận diện ngắn mạch |
| Sụt áp đường công suất quá mức | Đã có yêu cầu giám sát V_drop; phân loại warning/fault cụ thể chưa chốt | Đánh giá hiệu điện áp giữa hai nút đo theo trạng thái ON và cấu hình | MCU dựa trên V_in/V_load Sensing; phải xét sai số đo, vị trí đo và timing. Đối chiếu lại vị trí đo với Product Requirements |
| Ngược cực và xung điện áp đầu vào | Có chức năng bảo vệ trong kiến trúc Input Protection | Chặn ngược cực; hạn chế xung bằng Transient Suppression với giới hạn được thiết kế | Input Protection; topology, mức điện áp còn lại và năng lượng chịu được TBD. Chưa chốt việc báo/latch sự kiện này về MCU; không mặc định mọi xung bị kẹp đều tạo FAULT |
| Quá áp đầu vào gây nguy hiểm cho phần cứng, đặc biệt kéo dài | Cơ chế xử lý chưa xác định đầy đủ | Không mặc định TVS hoặc MCU có thể bảo vệ được; cần đánh giá có phải bổ sung ngắt/giới hạn áp bằng hardware hay không | Phân tích tại Input Protection và Internal Supply; mở rộng bảo vệ áp hardware ở nhánh tải chưa được chốt |
| Dòng sự cố lớn trên phần đường nguồn chung được bảo vệ | Có chức năng Fault-current Isolation trong kiến trúc | Cô lập dòng sự cố; có thể làm mất nguồn cả điều khiển và tải | Input Protection; chưa chốt fuse/eFuse hay giải pháp khác, thời gian tác động và phối hợp chọn lọc với HW_OCP |
| Quá dòng/chập nhánh nguồn điều khiển | Khả năng bảo vệ cụ thể còn TBD | Đánh giá bảo vệ của Internal Supply và khả năng cô lập sự cố đầu vào; không coi HW_OCP tải là cơ chế bảo vệ trực tiếp nhánh này | Internal Supply/Input Protection; chưa chốt cơ chế hoặc cam kết duy trì MCU hoạt động trong sự cố này |
| Hở/chập chờn hoặc đầu ra không phù hợp lệnh ON/OFF | FR-05 đã yêu cầu phát hiện điện áp đầu ra không phù hợp; khả năng chẩn đoán nguyên nhân còn mở | Đối chiếu V_load với lệnh switch và các phép đo khác; chưa cam kết nhận diện mọi lỗi dây hoặc linh kiện | MCU, sensing và Power Switch; tiêu chí, giới hạn quan sát và phản ứng cụ thể TBD |
| Xung phía tải, dòng ngược và tải trả năng lượng | Cần xét tương thích với tải đại diện; tải cảm mạnh hoặc trả năng lượng lớn ngoài phạm vi ban đầu theo concept | Không mặc định kẹp xung đầu vào xử lý được xung đầu ra, hoặc switch OFF bảo đảm V_load bằng 0/chặn dòng ngược | Thiết kế nhánh tải; đường thoát năng lượng, khả năng chặn ngược và miền xung được hỗ trợ TBD. Bảng này không mở rộng phạm vi tải |

Hai vùng bảo vệ được phân chia theo vùng cấp nguồn và vị trí tác động, không theo quy tắc tách riêng dòng với áp. Input Protection nằm trước điểm chia nhánh; bảo vệ nhánh tải tác động lên Power Switch. Ngắt riêng tải không chủ động cắt Internal Supply, nhưng dòng sự cố tải vẫn có thể làm nguồn chung sụt và khiến MCU reset. Khả năng duy trì điều khiển cần được phân tích và thử nghiệm, không được suy ra chỉ từ việc có hai nhánh.

### 5.3. Nội dung tiếp tục đặc tả

Với từng nhóm trong phạm vi, cần hoàn thiện điều kiện warning/fault, trạng thái áp dụng, phản ứng và nguyên tắc phục hồi ở cấp hệ thống. Ngưỡng còn mở, timing, cách đo, thuật toán và phối hợp bảo vệ được cụ thể hóa trong Hardware/Software Specification; các phép thử và tiêu chí đạt được đưa vào Test Specification. Giới hạn chịu đựng phần cứng không được đồng nhất với giới hạn vận hành công bố.

## 6. Trạng thái và hành vi hệ thống

### 6.1. State diagram

![System state diagram](diagrams/system-architecture-system-state-diagram.svg)

**Ghi chú:** Đầu ra chỉ được phép ON trong RUNNING và WARNING. Xử lý lỗi phải ngắt có ưu tiên cao hơn các chuyển trạng thái khác. MCU reset từ bất kỳ trạng thái nào đều đưa hệ thống về INITIALIZING với đầu ra OFF.

`Start` là điểm bắt đầu của sơ đồ, không phải một trạng thái vận hành. CHECKING BEFORE ON và RECOVERING thể hiện các bước xử lý có giữ đầu ra OFF.

| Trạng thái | Đầu ra | Ý nghĩa |
| --- | --- | --- |
| INITIALIZING | OFF | Khởi tạo và kiểm tra ban đầu sau cấp nguồn/reset |
| READY | OFF | Đủ điều kiện cho phép bật, chờ yêu cầu ON của người dùng |
| CHECKING BEFORE ON | OFF | Kiểm tra lại điều kiện tại thời điểm nhận yêu cầu ON |
| RUNNING | ON | Cấp điện và giám sát tải bình thường |
| WARNING | ON | Có cảnh báo nhưng chưa đạt điều kiện phải ngắt |
| FAULT | OFF | Giữ lỗi và chờ yêu cầu phục hồi thủ công |
| RECOVERING | OFF | Kiểm tra phục hồi và xử lý xóa lỗi |

### 6.2. Điều kiện chuyển trạng thái

| Từ | Điều kiện/sự kiện | Đến |
| --- | --- | --- |
| Điểm bắt đầu hoặc bất kỳ trạng thái nào | Cấp nguồn/MCU reset | INITIALIZING |
| INITIALIZING | Kiểm tra khởi động đạt | READY |
| INITIALIZING | Kiểm tra khởi động thất bại | FAULT |
| READY | Người dùng yêu cầu ON | CHECKING BEFORE ON |
| READY | Phát hiện lỗi | FAULT |
| CHECKING BEFORE ON | Kiểm tra cho phép bật đạt | RUNNING |
| CHECKING BEFORE ON | Kiểm tra cho phép bật thất bại do điều kiện lỗi | FAULT |
| RUNNING | Phát hiện cảnh báo | WARNING |
| RUNNING | Phát hiện lỗi phải ngắt | FAULT |
| WARNING | Hết cảnh báo và không có lỗi phải ngắt | RUNNING |
| WARNING | Đạt điều kiện phải ngắt hoặc phát hiện lỗi | FAULT |
| RUNNING hoặc WARNING | Người dùng yêu cầu OFF, không đồng thời có lỗi phải giữ | READY |
| FAULT | Người dùng yêu cầu phục hồi | RECOVERING |
| RECOVERING | Kiểm tra phục hồi đạt và lỗi đã được xóa thành công | READY |
| RECOVERING | Kiểm tra hoặc xóa lỗi thất bại | FAULT |

Nếu không có điều kiện chuyển hợp lệ, hệ thống giữ trạng thái hiện tại. Điều kiện cảnh báo kéo dài chỉ dẫn tới ngắt khi đạt tiêu chí tương ứng với loại lỗi/profile; không mặc định mọi cảnh báo có chung một timeout.

### 6.3. Giám sát, khởi động và yêu cầu ON/OFF

Khi nguồn điều khiển còn hoạt động, hệ thống tiếp tục giám sát V_in, V_load, I_load và trạng thái bảo vệ dù Power Switch ON hay OFF, đồng thời cung cấp trạng thái trên giao diện. Việc diễn giải thông tin đo phụ thuộc trạng thái switch.

Kiểm tra khởi động đánh giá điều kiện nguồn, V_in, tính hợp lệ của thông tin đo/cấu hình và trạng thái lỗi phần cứng. Khi đạt, hệ thống vào READY với đầu ra OFF, chờ người dùng yêu cầu ON; không tự khôi phục trạng thái ON trước reset.

Kiểm tra trước ON xác nhận lại điều kiện vận hành hiện tại và không có lỗi đang giữ. Chỉ khi đạt mới phát MCU_ENABLE. READY nghĩa là đủ điều kiện để thử cấp điện, chưa chứng minh tải không có sự cố. Profile có thể cho phép quá độ khởi động hợp lệ nhưng không được trì hoãn bảo vệ quá dòng nghiêm trọng.

Lệnh OFF của người dùng ngắt tải bình thường, không tự phát sinh FAULT và không xóa lỗi đang giữ. Lỗi do firmware hoặc hardware đều đưa hệ thống về OFF + FAULT. Với lỗi phần cứng, switch ngắt độc lập trước khi MCU tiếp nhận trạng thái và cập nhật giao diện.

### 6.4. Phục hồi và bật lại

1. Khi khởi động/reset, giữ Power Switch OFF cho đến khi có điều kiện và yêu cầu enable hợp lệ.
2. Khi bảo vệ phần cứng phát hiện quá dòng nghiêm trọng, Fault Memory giữ lỗi và SHUTDOWN_REQUEST ngắt switch độc lập với MCU.
3. MCU tiếp nhận OVERCURRENT_FAULT_STATUS, giữ MCU_ENABLE không hoạt động và hiển thị lỗi.
4. Không tự động retry. Người dùng phải yêu cầu phục hồi.
5. MCU giữ đầu ra OFF, thực hiện các kiểm tra phục hồi được định nghĩa và gửi CLEAR_FAULT_REQUEST khi hợp lệ.
6. MCU xác nhận trạng thái lỗi đã được xóa, đưa hệ thống về READY với đầu ra vẫn OFF. Người dùng phải gửi yêu cầu ON riêng để cấp điện lại.

Kiểm tra phục hồi quyết định có được xóa lỗi đang giữ hay không; kiểm tra trước ON quyết định có được cấp điện tại thời điểm nhận lệnh hay không. Hai bước có thể dùng chung kiểm tra điều kiện vận hành, nhưng phục hồi có thêm xử lý nguyên nhân lỗi và xóa trạng thái lỗi. Điều kiện phải được kiểm tra lại trước ON vì chúng có thể thay đổi sau khi phục hồi.

Khi tải đã ngắt, `I_load ≈ 0` không đủ chứng minh ngắn mạch đã được loại bỏ. Tiêu chí phục hồi, cách kiểm tra lỗi còn tồn tại và phản ứng nếu lỗi tái xuất hiện phải được xác định trước khi triển khai; không mặc định có thể kiểm tra mọi lỗi chỉ bằng phép đo khi OFF.

## 7. Các điểm cần hoàn thiện

- Sơ đồ tổng thể ghi `V_Ctrl_5V → MCU`: cần xác định khối MCU là module nhận 5 V hay chip dùng rail 3.3 V; chưa coi đây là quyết định cấp 5 V trực tiếp cho chip.
- Sửa lỗi chữ `Dectection` trong diagram bảo vệ và thống nhất tên tín hiệu giữa diagram tổng thể, diagram con và bảng interface.
- Chốt phần cứng, phương pháp sensing, dạng interface, mức logic, ngưỡng bảo vệ và timing ở đặc tả thành phần.
- Xác định tiêu chí phục hồi và hành vi khi nguồn nội bộ mất/sụt; trạng thái latch sau mất nguồn vẫn TBD, yêu cầu đầu ra mặc định OFF giữ nguyên.
- Chưa chốt việc bổ sung yêu cầu từ MCU để latch phần cứng giữ mọi lỗi firmware; không bổ sung interface này trong kiến trúc hiện tại.
- Đối chiếu vị trí đo V_in và các quyết định kiến trúc với Product Requirements trước khi chốt bản đặc tả để triển khai.

## 8. Quản lý bản vẽ

Chỉnh sửa trên file `.drawio`, sau đó xuất lại SVG của page tương ứng và đưa hai bản vào cùng thay đổi Git. SVG dùng để xem trên GitHub; `.drawio` là nguồn chỉnh sửa. Không cần xuất DOCX/PPTX vào repo.
