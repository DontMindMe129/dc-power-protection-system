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
- **PR-IO-02 — Đầu vào thông tin:** lệnh vận hành và các tham số cấu hình do người dùng cung cấp, bao gồm cấu hình nguồn, đặc tính điện của tải được lưu trong profile tải, ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép. Các giá trị cấu hình phải nằm trong miền vận hành định mức của thiết bị.

### 3.2. Đầu ra

- **PR-IO-03 — Đầu ra công suất:** năng lượng DC được truyền từ nguồn đến tải dưới sự giám sát và có khả năng đóng hoặc ngắt để bảo vệ đường công suất. Điện áp đầu ra `V_load` không được điều chỉnh mà bám theo `V_in`, trừ phần sụt áp trên thiết bị.
- **PR-IO-04 — Đầu ra thông tin:** các giá trị đo, trạng thái vận hành, cảnh báo và nguyên nhân ngắt bảo vệ của hệ thống.

### 3.3. Quan hệ công suất và biên hệ thống

```text
Khi đường công suất đóng: V_load ≈ V_in − V_drop
Khi đường công suất ngắt: thiết bị không chủ động truyền năng lượng từ nguồn đến tải
```

Nguồn và tải là các đối tượng bên ngoài hệ thống. Hành vi điện của tải ảnh hưởng tới `V_load` và `I_load`, nhưng profile tải là đầu vào thông tin do người dùng chọn chứ không phải tải vật lý.

## 4. Trường hợp sử dụng

### UC-01 — Thiết lập hệ thống

Người dùng kết nối nguồn DC và tải tương thích, sau đó lựa chọn hoặc nhập cấu hình nguồn, profile tải và các ngưỡng được phép thay đổi. Hệ thống phải kiểm tra cấu hình có nằm trong miền vận hành định mức hay không. Trong quá trình thiết lập, đầu ra công suất được giữ ở trạng thái OFF.

### UC-02 — Cấp điện và giám sát tải bình thường

Sau cấp nguồn/reset, hệ thống kiểm tra khởi động và giữ đầu ra OFF. Chỉ cho phép vào READY khi không có lỗi đang xảy ra và không còn lỗi nào đang được giữ lại, kể cả lỗi từ HW_OCP; các điều kiện khởi động khác cũng phải đạt. Khi đạt điều kiện, hệ thống vào READY và chờ người dùng yêu cầu ON. Trong READY, các điều kiện cho phép bật được duy trì bằng giám sát liên tục; lệnh ON cho phép đóng đường công suất, không có bước Enable Checks riêng. Lỗi phải ngắt được ưu tiên nếu xuất hiện đồng thời với lệnh ON. Hệ thống đo và hiển thị `V_in`, `V_load`, `I_load` khi nguồn điều khiển còn hoạt động, dù đầu ra ON hay OFF; cách đánh giá phụ thuộc trạng thái switch. Chính sách giữ lỗi qua reset/mất nguồn được quy định tại FR-10.

### UC-03 — Tải có dòng khởi động

Khi tải được bật, dòng điện có thể tạm thời cao hơn dòng hoạt động bình thường. Hệ thống sử dụng profile tải để quyết định mức dòng và thời gian được chấp nhận. Nếu dòng trở về mức bình thường trong thời gian cho phép, hệ thống tiếp tục cấp điện và không ngắt nhầm tải.

### UC-04 — Phát hiện điều kiện bất thường

Hệ thống phải quan sát các tình trạng như:

- `V_in` nằm ngoài miền của cấu hình nguồn;
- `I_load` vượt miền vận hành bình thường;
- `V_drop = V_in − V_load` quá lớn;
- `V_load` không phù hợp với trạng thái đóng hoặc ngắt mong đợi.

Nếu tình trạng chưa gây nguy hiểm tức thời, hệ thống cảnh báo và cung cấp thông tin giúp người dùng đánh giá vấn đề có thể đến từ nguồn, tải hoặc đường công suất. Kết quả chẩn đoán chỉ là gợi ý nếu các phép đo hiện có chưa đủ để kết luận tuyệt đối.

### UC-05 — Ngắt bảo vệ

Khi xảy ra tình trạng nguy hiểm, chẳng hạn quá dòng nghiêm trọng hoặc ngắn mạch, hệ thống phải nhanh chóng ngắt đường cấp điện tới tải. Sau khi ngắt, hệ thống phải:

- duy trì công tắc công suất ở trạng thái ngắt;
- không chủ động truyền năng lượng từ nguồn đến tải;
- không tự động đóng lại khi chưa thỏa mãn chính sách phục hồi;
- hiển thị trạng thái và nguyên nhân gây ngắt.

Yêu cầu này không đồng nghĩa với việc bảo đảm `V_load = 0 V` ngay lập tức vì phía tải có thể còn điện tích, dòng rò hoặc nguồn cấp ngược.

### UC-06 — Phục hồi sau lỗi

Người dùng xử lý nguyên nhân lỗi và yêu cầu phục hồi thủ công; hệ thống không tự động retry. Hệ thống giữ yêu cầu đầu ra OFF, kiểm tra điều kiện chung và điều kiện theo nguyên nhân, rồi xóa và xác nhận lỗi tại firmware/HW_OCP khi hợp lệ. Thành công thì về READY với đầu ra vẫn OFF, chờ lệnh ON riêng; thất bại thì giữ FAULT và hiển thị lý do. Không coi dòng bằng 0 khi OFF là bằng chứng tải đã hết ngắn mạch. Tiêu chí định lượng và thời gian chờ cụ thể vẫn TBD.

Luồng phục hồi này áp dụng cho lỗi đang được giữ trong phiên vận hành; sau reset/mất nguồn áp dụng chính sách FR-10, không mặc định mọi lỗi được nhớ xuyên khởi động lại.

### UC-07 — Thay đổi nguồn hoặc tải

Khi chuyển sang nguồn hoặc tải khác, người dùng chọn lại cấu hình tương ứng. Hệ thống chỉ chấp nhận cấu hình nằm trong miền vận hành định mức và sử dụng cấu hình mới cho các quyết định cảnh báo, cho phép hoạt động và ngắt bảo vệ.

## 5. Yêu cầu chức năng

### FR-01 — Điều khiển đường công suất

Hệ thống phải có khả năng cho phép hoặc ngăn năng lượng truyền từ nguồn DC đến tải theo lệnh vận hành và trạng thái bảo vệ.

### FR-02 — Đo điện áp đầu vào

Hệ thống phải đo `V_in` tại đầu nối nguồn vào của PCB, trước Input Protection, để xác định trạng thái điện áp nguồn ngoài tại đầu vào thiết bị. Đầu vào đo phải được bảo vệ phù hợp mà vẫn duy trì khả năng đo/phát hiện điện áp trong miền yêu cầu; miền sự cố chịu được cụ thể là TBD.

### FR-03 — Đo điện áp tại tải

Hệ thống phải đo `V_load` tại đầu nối tải ra của PCB, sau Power Switch, khi đường công suất đang đóng và khi đã bị ngắt.

### FR-04 — Đo dòng tải

Hệ thống phải đo `I_load` chạy trên đường công suất từ nguồn đến tải. Giá trị này không bao gồm dòng tự tiêu thụ của hệ thống.

### FR-05 — Đánh giá trạng thái đường công suất

Hệ thống phải:

- so sánh `V_in`, `V_load` và `I_load` với cấu hình vận hành;
- xác định `V_drop = V_in − V_load`;
- phân biệt trạng thái bình thường, cảnh báo và lỗi;
- phát hiện điện áp đầu ra không phù hợp với trạng thái đóng hoặc ngắt mong đợi.

Khi ON, hệ thống đánh giá sụt áp qua `V_drop` sau khi xét quá độ hợp lệ; khi OFF, cho phép điện áp dư theo đặc tính tải và chỉ xác định lỗi đầu ra khi đạt tiêu chí bất thường. Không tự kết luận switch hỏng chỉ từ `V_load`, chưa yêu cầu mạch chủ động xả tụ. Ngưỡng và thời gian chẩn đoán cụ thể vẫn TBD.

### FR-06 — Quản lý cấu hình vận hành

Hệ thống phải hỗ trợ cấu hình nguồn và ba profile tải hiện tại: `RESISTIVE`, `ELECTRONIC` và `FAN`. Mỗi profile có thể xác định ngưỡng cảnh báo, ngưỡng ngắt và khoảng thời gian cho phép đối với hiện tượng quá độ. Khả năng bổ sung profile `CUSTOM` hiện là TBD.

Mỗi lần cấp nguồn hoặc MCU reset, hệ thống phải nạp bộ cấu hình mặc định do nhóm xác định và kiểm tra tính hợp lệ trước khi sử dụng. Phiên bản hiện tại không yêu cầu nhớ hoặc khôi phục cấu hình người dùng của lần vận hành trước. Giá trị mặc định cụ thể của cấu hình nguồn, profile và các tham số vẫn TBD. Cấu hình mặc định nằm trong giới hạn sản phẩm không đồng nghĩa phù hợp với mọi nguồn/tải đang kết nối; người dùng kiểm tra và điều chỉnh theo UC-01 trước khi yêu cầu ON. Nạp cấu hình không tự bật đầu ra; chính sách khởi động và lệnh ON riêng vẫn giữ nguyên.

Bộ cấu hình mặc định là các giá trị ban đầu do nhóm xác định, không bị ghi đè bởi thao tác chỉnh cấu hình thông thường của người dùng. Người dùng thay đổi cấu hình vận hành hiện tại; sau cấp nguồn/reset, hệ thống nạp lại mặc định theo nguyên tắc trên.

Chỉ cho phép áp dụng thay đổi cấu hình trong READY hoặc FAULT.

Với mỗi thông số cấu hình, giới hạn vận hành được áp dụng là giá trị nghiêm ngặt nhất trong các giới hạn của sản phẩm, nguồn đang chọn và profile tải đang chọn. Hệ thống phải từ chối mọi giá trị cấu hình nằm ngoài giới hạn đó và thông báo lý do cho người dùng.

Cấu hình người dùng chỉnh/chọn là cấu hình đề nghị, chỉ được thay thế cấu hình đang áp dụng sau khi toàn bộ cấu hình đề nghị được kiểm tra và chấp nhận. Nếu không hợp lệ, hệ thống từ chối cấu hình đề nghị, thông báo lý do và giữ nguyên cấu hình hợp lệ đang áp dụng; không tự chuyển FAULT chỉ vì thao tác nhập/chọn không hợp lệ. Nếu đang dùng mặc định thì giữ mặc định; nếu đã áp dụng một bộ khác hợp lệ thì giữ bộ đó, không tự quay về mặc định. Khi đầu ra đang OFF, việc từ chối giữ đầu ra OFF và không chặn lệnh ON sau đó bằng cấu hình đang áp dụng, miễn hệ thống đang READY và không có lỗi ngăn cấp điện. Chấp nhận hay từ chối cấu hình không tự bật tải hoặc xóa lỗi đang giữ; lỗi thực sự nếu xuất hiện vẫn được xử lý theo chính sách bảo vệ.

Trong RUNNING hoặc WARNING, hệ thống phải từ chối yêu cầu đổi cấu hình nguồn, profile hoặc ngưỡng bảo vệ và thông báo cần tắt đầu ra trước. Giữ nguyên cấu hình đang áp dụng và tiếp tục giám sát/bảo vệ; không tự ngắt tải hoặc tạo FAULT chỉ vì yêu cầu đổi cấu hình. Để thay đổi, người dùng yêu cầu OFF và khi hệ thống về READY thì được chỉnh/chọn cấu hình mới theo quy tắc kiểm tra trên. Sau khi áp dụng, đầu ra vẫn OFF; chỉ được ON khi hệ thống đáp ứng điều kiện READY và có lệnh ON riêng.

Trong FAULT, người dùng được chỉnh/chọn và đề nghị áp dụng cấu hình mới. Cấu hình không hợp lệ bị từ chối, giữ cấu hình đang áp dụng; cấu hình hợp lệ được áp dụng nhưng hệ thống vẫn giữ FAULT và yêu cầu đầu ra OFF. Đổi cấu hình không tự xóa lỗi firmware hoặc gửi yêu cầu clear HW_OCP. Người dùng phải yêu cầu phục hồi riêng; kiểm tra theo cấu hình đang áp dụng và nguyên nhân lỗi, xóa và xác nhận lỗi thành công theo FR-11 thì mới về READY, vẫn OFF và chờ lệnh ON riêng.

Cấu hình hợp lệ chỉ xác nhận bộ tham số đáp ứng các ràng buộc cấu hình, không bảo đảm phù hợp với nguồn/tải thực tế. Một bộ cấu hình hợp lệ nhưng chọn không phù hợp có thể khiến điều kiện đo đạt tiêu chí fault; khác với cấu hình không hợp lệ vốn bị từ chối trước khi áp dụng. Hệ thống đánh giá theo thông tin đo và tham số đang áp dụng, không tự nhận biết người dùng đã chọn nhầm loại nguồn hoặc profile.

Khả năng cấp dòng khai báo của nguồn là ngân sách cho toàn thiết bị, không chỉ nhánh tải. Khi xác định giới hạn dòng tải liên tục, phải trừ phần dự phòng dòng tự tiêu thụ quy về đầu vào nguồn:

```text
Giới hạn dòng tải liên tục cho phép ≤ min(
    giới hạn dòng tải liên tục của sản phẩm,
    khả năng cấp dòng liên tục của nguồn − dự phòng dòng tự tiêu thụ đầu vào,
    giới hạn dòng liên tục của profile tải
)
```

Phần dự phòng phải xét miền V_in, chế độ hoạt động và dung sai; không dùng trực tiếp dòng trên rail 5 V hoặc 3.3 V thay cho dòng lấy từ nguồn ngoài. Giá trị dự phòng cụ thể còn TBD, được tính toán và kiểm chứng khi thiết kế phần cứng; chưa yêu cầu thêm cảm biến dòng tổng. Baseline dòng tải liên tục `1 A` không đổi: để sử dụng đủ mức này, nguồn phải còn đáp ứng phần tự tiêu thụ của thiết bị. Ngưỡng fault quá tải và thời gian tác động phải phối hợp với giới hạn tải cho phép, có xét sai số đo và dung sai; không mặc định ngưỡng fault bằng đúng giới hạn cấu hình hoặc khả năng cấp dòng của nguồn.

### FR-07 — Xử lý dòng khởi động và dòng đỉnh

Khi dòng tải vượt giới hạn dòng liên tục nhưng vẫn nằm trong giới hạn dòng đỉnh và thời gian cho phép của cấu hình vận hành, hệ thống phải tiếp tục cấp điện cho tải và không tác động ngắt bảo vệ. Yêu cầu này không áp dụng đối với điều kiện quá dòng nghiêm trọng thuộc phạm vi bảo vệ nhanh.

Miền dòng đỉnh được cấu hình cũng phải phù hợp với ngân sách nguồn sau khi dành phần tự tiêu thụ và khả năng cấp dòng của nguồn trong khoảng thời gian tương ứng. Baseline của mạch `2 A/1 s` không đồng nghĩa mọi nguồn đang kết nối đều cho phép cấp mức dòng đó.

### FR-08 — Cảnh báo bất thường

Khi phát hiện điều kiện bất thường nhưng chưa cần ngắt, hệ thống phải thông báo cho người dùng bằng trạng thái hoặc cảnh báo phù hợp. Các nhóm warning vận hành hiện tại gồm `V_in`, `I_load` và `V_drop` khi đang cấp điện; quá độ hợp lệ theo profile không tự là warning. Khi đang cấp điện, còn bất kỳ warning nào thì hệ thống giữ WARNING; chỉ về RUNNING khi mọi warning đã hết và không có fault. Các nguyên nhân được ghi nhận/xóa riêng, và warning không được che mất fault. Các tiêu chí định lượng vẫn TBD.

### FR-09 — Ngắt bảo vệ

Khi phát hiện điều kiện nguy hiểm, hệ thống phải ngắt đường truyền năng lượng từ nguồn đến tải và ghi nhận nguyên nhân. Bảo vệ trước quá dòng nghiêm trọng hoặc ngắn mạch không được phụ thuộc hoàn toàn vào tốc độ xử lý của firmware.

### FR-10 — Duy trì trạng thái sau khi ngắt

Trong phiên vận hành hiện tại, sau khi xảy ra ngắt bảo vệ, hệ thống phải duy trì yêu cầu đường công suất OFF cho đến khi một yêu cầu phục hồi hợp lệ được chấp nhận theo FR-11. Trong thời gian nguồn nuôi khối điều khiển còn hợp lệ, hệ thống phải duy trì và cung cấp cho người dùng trạng thái ngắt cùng nguyên nhân đã ghi nhận.

Sau MCU reset, hệ thống về INITIALIZING, nạp cấu hình mặc định và giữ đầu ra OFF; không khôi phục bản ghi fault phần mềm của phiên trước. Nếu kiểm tra lại phát hiện lỗi thì vào FAULT; nếu mọi Startup Checks đều đạt thì về READY/OFF và cần lệnh ON riêng. Reset vì vậy có thể thay thế bước phục hồi của fault phần mềm không còn được phát hiện sau khởi động; điều này không chứng minh nguyên nhân vật lý đã được khắc phục và không phải tự động retry.

MCU reset không tự gửi yêu cầu clear HW_OCP. Khi khởi động, nếu trạng thái hợp lệ từ phần cứng vẫn báo lỗi quá dòng được giữ thì hệ thống phải vào FAULT và phục hồi thủ công theo FR-11; chưa đọc được trạng thái hợp lệ thì chưa được kết luận đủ điều kiện vào READY. Việc giữ trạng thái phần cứng qua reset phụ thuộc nguồn nuôi và thiết kế latch. Phiên bản hiện tại không yêu cầu lưu lỗi bền vững qua mất nguồn hoàn toàn, không mặc định latch giữ lỗi sau mất nguồn.

Với HW_OCP, thông tin lỗi được cam kết là trạng thái đã phát hiện quá dòng nghiêm trọng và đang giữ khóa ngắt. Không cam kết nhận diện nguyên nhân vật lý cụ thể, thu được dòng đỉnh hay điện áp tại thời điểm sự cố; các giá trị đo sau ngắt không thay thế dữ liệu tại thời điểm lỗi. Chưa yêu cầu chức năng thu thập/lưu dữ liệu sự cố hoặc lịch sử lỗi qua reset/mất nguồn.

### FR-11 — Phục hồi hoạt động

Hệ thống phải hỗ trợ phục hồi thủ công theo yêu cầu người dùng, không tự động thử cấp điện lại sau lỗi. Kiểm tra phục hồi phải xét điều kiện chung, nguyên nhân lỗi và nơi giữ lỗi; chỉ về READY khi kiểm tra đạt và mọi lỗi ngăn phục hồi đã được xóa thành công. Đầu ra vẫn OFF và người dùng phải gửi yêu cầu ON riêng. Trong READY, hệ thống tiếp tục giám sát để duy trì điều kiện cho phép bật; không có bước Enable Checks riêng sau lệnh ON. Xóa latch thành công không chứng minh tải đã hết ngắn mạch; bảo vệ vẫn phải sẵn sàng khi cấp điện lại.

### FR-12 — Giao diện người dùng

Hệ thống dùng Buttons để nhận thao tác và Display để cung cấp thông tin cho người dùng. Giao diện phải hỗ trợ:

- bật hoặc tắt đầu ra;
- chọn hoặc thay đổi cấu hình;
- theo dõi `V_in`, `V_load`, `I_load`;
- nhận biết trạng thái vận hành, cảnh báo và nguyên nhân ngắt;
- yêu cầu phục hồi sau lỗi.

Loại màn hình, số lượng nút, giao tiếp và cách thao tác/nhập cấu hình chi tiết còn TBD.

## 6. Yêu cầu hiệu năng

### 6.1. Bảng thông số hiện tại

| Mã | Thông số | Giá trị hiện tại | Trạng thái |
| --- | --- | --- | --- |
| PR-PERF-01 | Miền điện áp nguồn ngoài tại đầu nối vào (`V_in`) | `5–15 V DC` | Đã xác nhận |
| PR-PERF-02 | Dòng tải liên tục định mức | `0–1 A` | Tạm thời |
| PR-PERF-03 | Dòng đỉnh vận hành định mức | Trên `1 A` đến `2 A` | Tạm thời |
| PR-PERF-04 | Thời gian liên tục tối đa trong miền dòng đỉnh | `1 s` | Tạm thời |
| PR-PERF-05 | Sai số đo `V_in` | `±0,1 V` trong miền hoạt động `5–15 V` | Tạm thời |
| PR-PERF-06 | Sai số đo `V_load` | `±0,1 V` trong miền đo `0–15 V` | Tạm thời |
| PR-PERF-07 | Sai số đo `I_load` | `±0,05 A` trong miền đo `0–2 A` | Tạm thời |
| PR-PERF-08 | Chu kỳ cập nhật giá trị và trạng thái trên giao diện | `≤ 200 ms` | Tạm thời |
| PR-PERF-09 | Phản ứng với lỗi quá dòng nghiêm trọng | `≤ 100 µs` từ lúc dòng nhánh tải vượt ngưỡng HW_OCP đến khi Power Switch thực sự ngắt đường cấp điện từ nguồn tới tải | Tạm thời |
| PR-PERF-10 | Độ trễ ngắt sau khi hết thời gian cho phép | `≤ 10 ms` | Tạm thời |
| PR-PERF-11 | Sụt áp tại dòng liên tục `1 A` | `≤ 0,2 V` | Tạm thời |
| PR-PERF-12 | Sụt áp tại dòng đỉnh `2 A` | `≤ 0,4 V` | Tạm thời |

### 6.2. Miền vận hành dòng điện định mức

Hệ thống phải hỗ trợ dòng tải liên tục từ `0–1 A`. Dòng trên `1 A` đến `2 A` được phép tồn tại trong khoảng thời gian do profile tải quy định nhưng không quá `1 giây`. Trong miền vận hành này, hệ thống phải hoạt động đúng chức năng và không hư hỏng nếu cấu hình đang chọn cho phép điều kiện đó.

Dòng vượt `2 A` hoặc vượt giới hạn thời gian nằm ngoài miền vận hành định mức và phải được xử lý bởi chức năng bảo vệ. `2 A/1 s` là biên trên của miền sử dụng được công bố và là điều kiện tối thiểu mà thiết kế phải đáp ứng; đây không phải giới hạn phá hủy tuyệt đối của linh kiện.

Ngưỡng tác động chính xác của bảo vệ nhanh và giới hạn chịu đựng tuyệt đối của phần cứng hiện là TBD.

### 6.3. Thời gian phản ứng bảo vệ

Bảo vệ nhanh áp dụng cho lỗi quá dòng nghiêm trọng và không được phụ thuộc hoàn toàn vào firmware. Theo PR-PERF-09, thời gian phản ứng được tính từ lúc dòng nhánh tải vượt ngưỡng HW_OCP đến khi Power Switch thực sự ngắt đường cấp điện từ nguồn tới tải. Khoảng này bao gồm độ trễ đo/phát hiện, giữ lỗi và điều khiển, cùng độ trễ ngắt phần tử công suất; không chỉ tính từ lúc xác lập tín hiệu lỗi hoặc đến lúc phát yêu cầu shutdown. Giá trị `≤ 100 µs` vẫn là baseline tạm thời.

Ngưỡng HW_OCP cụ thể, dung sai, điều kiện thử và tiêu chí điện xác nhận đã ngắt sẽ được xác định trong Hardware/Test Specification. Không yêu cầu V_load về 0 tức thời vì tụ phía tải có thể còn điện. Kiểm chứng bằng thiết bị đo ngoài, không yêu cầu MCU ghi lại dạng sóng hoặc dữ liệu lúc sự cố.

Bảo vệ có xử lý thời gian chỉ bắt đầu tính độ trễ ngắt sau khi điều kiện lỗi đã tồn tại hết khoảng thời gian mà profile cho phép.

Ví dụ, nếu profile cho phép một mức dòng trong `500 ms`, tổng thời gian trước khi ngắt có thể đạt `510 ms`: `500 ms` thời gian được phép và tối đa `10 ms` độ trễ xử lý ngắt.

### 6.4. Sụt áp đường công suất

Các giới hạn sụt áp được đánh giá giữa đầu nối vào và đầu nối ra của PCB, không bao gồm dây dẫn bên ngoài. Chúng kiểm soát tổng ảnh hưởng của Input Protection, phần tử đo dòng, công tắc công suất, đường đồng, đầu nối và mối tiếp xúc. Với vị trí đo theo FR-02 và FR-03, `V_drop = V_in − V_load` bao gồm các phần này khi đường công suất ON và đã xét quá độ hợp lệ. Kiểm chứng giới hạn hiệu năng dùng thiết bị đo tham chiếu tại hai đầu nối; phép tính từ hai kênh đo nội bộ phải xét sai số, không thay thế phép đo tham chiếu.

Ở điểm kiểm thử `1 A`, giới hạn `0,2 V` tương ứng với tỉ số `V_drop/I_load` tối đa `0,2 Ω` và tích `V_drop × I_load` tối đa `0,2 W`. Đây là các giá trị quy đổi theo dòng tải, không mặc định là điện trở vật lý hay tổng tổn hao của mọi phần tử trên đường công suất: đoạn chung qua Input Protection còn mang dòng cấp nhánh điều khiển. Giá trị tại `2 A` là điều kiện ngắn hạn.

### 6.5. Độ phân giải và độ chính xác

Độ phân giải hiển thị không được dùng để thay thế yêu cầu về độ chính xác. Độ phân giải hiển thị và tần số lấy mẫu nội bộ hiện là TBD. Sai số xấu nhất của `V_drop` tính từ hai kênh điện áp có thể lớn hơn sai số của từng kênh và phải được xét khi xây dựng ngưỡng chẩn đoán.

Mạch đo `V_in` phải có khả năng nhận biết điện áp nằm phía trên giới hạn hoạt động `15 V` để hệ thống không cấp điện cho tải trong điều kiện quá áp. Miền phát hiện quá áp, độ chính xác ngoài miền hoạt động và giới hạn điện áp chịu đựng tuyệt đối hiện là TBD.

Miền nguồn 5–15 V được xác định trước Input Protection, không phải tại nút điện áp sau bảo vệ (`V_protected`). Ngưỡng warning/fault phải xét sai số đo, cấu hình và hysteresis; chưa chốt giá trị cụ thể chỉ từ hai biên vận hành. Bảo vệ đầu vào đo V_in phải xét cả tình huống có điện áp nguồn ngoài khi khối đo/MCU chưa được cấp nguồn; chi tiết được xác định trong Hardware Specification.

## 7. Chi phí sản xuất

| Mã | Hạng mục | Mục tiêu hiện tại | Trạng thái |
| --- | --- | --- | --- |
| PR-COST-01 | BOM cho một bo mạch | `≤ 600.000 VNĐ` | Tạm thời |
| PR-COST-02 | Một prototype hoàn chỉnh gồm PCB và linh kiện | `≤ 1.000.000 VNĐ` | Tạm thời |

Giá thành sản phẩm có thể bao gồm linh kiện trên PCB, chế tạo PCB, lắp ráp và vỏ nếu có. Giá thành không bao gồm nguồn DC bên ngoài, tải kiểm thử, mạch nạp/debugger, thiết bị đo, chi phí nghiên cứu và linh kiện mua dư.

## 8. Nguồn nuôi hệ thống

**PR-PWR-01 — Đã xác nhận:** Hệ thống phải tự cấp nguồn cho khối điều khiển, đo lường và giao diện từ nguồn DC đầu vào, thông qua một nhánh nguồn nội bộ được bảo vệ và điều chỉnh điện áp riêng. Nhánh này lấy sau Input Protection và trước công tắc công suất của tải để việc ngắt tải không chủ động cắt nguồn điều khiển. Thiết kế phải xét tổn hao Input Protection trên toàn miền V_in 5–15 V. Khả năng tiếp tục giám sát khi tải bị ngắt phụ thuộc nguồn nội bộ còn hoạt động; không bảo đảm duy trì MCU nếu Input Protection đã cô lập nguồn hoặc nguồn ngoài mất/sụt ngoài khả năng vận hành.

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
| Đo `V_in`, `V_load`, `I_load` | So sánh với thiết bị đo tham chiếu tại nhiều điểm trong miền hoạt động |
| Dòng liên tục, dòng đỉnh và sụt áp | Tạo tải có kiểm soát và đo bằng đồng hồ cùng oscilloscope |
| Phản ứng bảo vệ nhanh | Tạo sự kiện vượt ngưỡng HW_OCP có kiểm soát và giới hạn năng lượng; dùng oscilloscope đo từ lúc dòng vượt ngưỡng đến khi Power Switch thực sự ngắt cấp điện, theo PR-PERF-09 và tiêu chí Hardware/Test Specification |
| Cập nhật giao diện và logic ngắt | Đo thời gian sự kiện–phản ứng và quan sát trạng thái hệ thống |
| Chi phí | Tổng hợp BOM, hóa đơn PCB và linh kiện sử dụng thực tế |
| Nguồn nuôi và fail-safe | Thử đóng/ngắt nguồn, brownout, reset MCU và trạng thái công tắc tải |
| Kích thước, khối lượng và lắp đặt | Đo trực tiếp và kiểm tra bằng quan sát |

Các phép thử chi tiết, điều kiện thử, thiết bị và tiêu chí pass/fail sẽ được định nghĩa trong Test Specification.

## 12. Các vấn đề chưa chốt

Các quyết định sau chưa được chốt trong Product Requirement v0.1:

1. Ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép cụ thể của từng profile tải.
2. Ngưỡng tác động và dung sai của bảo vệ quá dòng nhanh.
3. Giới hạn điện áp, dòng và năng lượng tuyệt đối của phần cứng ngoài miền vận hành định mức.
4. Tiêu chí kiểm tra phục hồi theo từng loại lỗi và thời gian chờ cụ thể; phục hồi thủ công, không tự động retry và cần lệnh ON riêng đã được chốt.
5. Độ phân giải hiển thị và tần số lấy mẫu nội bộ cần thiết.
6. Loại màn hình, số lượng nút, giao tiếp và cách thao tác/nhập cấu hình chi tiết; hình thức Buttons + Display đã được xác định.
7. Loại đầu nối nguồn và tải.
8. Điều kiện nhiệt độ, độ ẩm định lượng cho môi trường vận hành.
9. Tần suất cho phép của các sự kiện dòng đỉnh lặp lại.
10. Khả năng bổ sung profile tải `CUSTOM`.
11. Giá trị cụ thể của bộ cấu hình mặc định khi cấp nguồn/reset.

Các mục này phải được giải quyết hoặc chuyển thành ràng buộc rõ ràng trong Design Specification, Hardware Specification, Software Specification hoặc Test Specification trước khi chế tạo PCB.
