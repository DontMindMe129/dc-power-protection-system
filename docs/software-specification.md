# Đặc tả phần mềm sơ bộ

> Bản sơ bộ v0.1, ngày 2026-11-05. Tài liệu cụ thể hóa hành vi đã thống nhất thành yêu cầu firmware, chỉ tiêu và cơ chế xử lý sơ bộ. Các lựa chọn bổ sung khi soạn bản này được ghi rõ ở từng mục. Các giá trị chưa đủ căn cứ được theo dõi tại mục 11. Các mục tiêu cần được kiểm chứng khi triển khai.

## 1. Phạm vi và ràng buộc chung

Firmware thu thập thông tin đo, đánh giá điều kiện vận hành, quản lý cấu hình/trạng thái, điều khiển yêu cầu cấp tải và giao diện người dùng. Đầu vào của tài liệu là [Product Requirements](product-requirements.md), [Design Specification](design-specification.md) và [Hardware Specification](hardware-specification.md).

Tài liệu sử dụng mức yêu cầu chức năng, chỉ tiêu định lượng và cơ chế sơ bộ. Cấu trúc file, hàm, kiểu dữ liệu cụ thể, driver, tham số thuật toán và cấu hình ngoại vi được xây dựng trong Software Design. Bare-metal/RTOS, model MCU và thư viện triển khai còn TBD.

| Ràng buộc kế thừa | Yêu cầu đối với firmware | Căn cứ |
| --- | --- | --- |
| Định nghĩa phép đo | V_in trước Input Protection tại đầu nối vào; V_load tại đầu nối ra; I_load chỉ thuộc nhánh tải; V_drop = V_in − V_load | Design Spec mục 2 |
| Miền vận hành | V_in 5–15 V; I_load 0–1 A liên tục, trên 1–2 A tối đa 1 s khi cấu hình cho phép | PR-PERF-01 đến 03 |
| Sai số đo | V_in ±0,05 V trong 5–15 V; V_load ±0,05 V trong 0–15 V; I_load ±0,05 A trong 0–2 A; V_drop ±0,1 V trong điều kiện áp dụng | PR-PERF-04 đến 07 |
| Thu thập dữ liệu | Có bộ giá trị mới của cả ba đại lượng mỗi ≤1 ms | HW Spec mục 5.7 |
| Hiển thị | Cập nhật dữ liệu và trạng thái mỗi ≤200 ms | PR-PERF-08 |
| Ngắt phần mềm | Toàn đường ≤10 ms từ khi điều kiện ngắt theo mức hoặc thời gian đạt tới ngắt cấp điện thực tế | PR-PERF-11 |
| Bảo vệ nhanh | HW_OCP độc lập firmware; ngưỡng thực tế >2 A và ≤2,5 A, toàn đường ≤100 µs | PR-PERF-09, 10 |
| Mặc định an toàn | Yêu cầu đầu ra OFF khi reset, khởi tạo, FAULT và RECOVERING; phục hồi thành công vẫn OFF | Design Spec mục 6 |

Firmware chỉ đánh giá khi còn nguồn điều khiển và thông tin sử dụng được. Input Protection có thể ngắt nguồn chung trước khi MCU ghi nhận sự kiện; phạm vi hiển thị/chẩn đoán phụ thuộc khả năng quan sát của phần cứng.

## 2. Tổ chức firmware tổng thể

### 2.1. Phân chia trách nhiệm

Các nhóm dưới đây là phân chia chức năng. Cách ánh xạ thành module, vòng lặp, ngắt hoặc task được quyết định khi thiết kế.

| Nhóm | Đầu vào | Trách nhiệm và đầu ra |
| --- | --- | --- |
| Thu thập và quy đổi | ADC, tham số quy đổi, thời gian | Cung cấp V_in, V_load, I_load, thời điểm và trạng thái hợp lệ của dữ liệu |
| Quản lý cấu hình | Mặc định, bộ tham số người dùng đề nghị, giới hạn sản phẩm/nguồn | Kiểm tra và công bố trọn bộ cấu hình đang áp dụng hoặc lý do từ chối |
| Giám sát và bảo vệ | Dữ liệu đo, cấu hình, trạng thái đầu ra, thời gian | Xác định cảnh báo, điều kiện ngắt và các nguyên nhân lỗi |
| Quản lý trạng thái | Kết quả giám sát, trạng thái HW_OCP, lệnh người dùng | Thực hiện state diagram, Startup Checks và Recovery Checks |
| Điều khiển đầu ra | Quyết định trạng thái và phục hồi | Phát MCU_ENABLE, CLEAR_FAULT_REQUEST theo quyền ưu tiên đã định |
| Giao diện người dùng | Buttons, dữ liệu/trạng thái công bố | Tạo lệnh thao tác, quản lý bản cấu hình đề nghị và cập nhật Display |
| Chẩn đoán | Thông tin hoạt động và sự kiện | Cung cấp dữ liệu debug/kiểm thử qua UART khi được sử dụng |

Luồng xử lý sơ bộ gồm thu thập dữ liệu → đánh giá theo cấu hình → xử lý trạng thái và quyết định đầu ra → công bố thông tin cho UI/chẩn đoán. HW_OCP đi trực tiếp tới Power Switch và được MCU đọc lại qua trạng thái lỗi.

### 2.2. Quyền điều khiển và tính nhất quán

- Một đầu mối điều khiển chịu trách nhiệm phát MCU_ENABLE và CLEAR_FAULT_REQUEST. UI và chẩn đoán gửi yêu cầu tới đầu mối này.
- Một lần đánh giá phải sử dụng dữ liệu đo nhất quán và một bộ cấu hình đã chấp nhận trọn vẹn. Việc cập nhật đồng thời phải tránh trộn các kênh hoặc các phiên bản cấu hình.
- Phân biệt điều kiện đang xảy ra, cảnh báo đang có và nguyên nhân lỗi được giữ trong phiên vận hành. Điều kiện trở lại bình thường không tự xóa lỗi đang giữ.
- Thông tin đầu ra công bố cho UI phải phân biệt yêu cầu ON/OFF, trạng thái HW_OCP và điện áp đo được. Hiện kiến trúc chưa có tín hiệu xác nhận trực tiếp trạng thái dẫn của Switch.

Đầu mối điều khiển và công bố dữ liệu nhất quán là cơ chế tổ chức sơ bộ của bản này. Cách bảo vệ dữ liệu dùng chung được lựa chọn theo mô hình thực thi.

## 3. Thu thập và xử lý dữ liệu đo

### Yêu cầu

- Thu thập cả V_in, V_load và I_load định kỳ trong mọi trạng thái khi nguồn điều khiển còn hoạt động. Khởi tạo bắt đầu thu thập trước khi kết luận Startup Checks.
- Mỗi ≤1 ms có bộ dữ liệu mới theo HW Spec. Ghi nhận thời điểm cập nhật để nhận biết dữ liệu chưa sẵn sàng hoặc quá hạn.
- Quy đổi dữ liệu ADC sang V và A theo phần cứng thực tế. Sai số lượng tử hóa, tham chiếu, tính toán, làm tròn và hiệu chỉnh phải nằm trong ngân sách đo đã đặt.
- Tính V_drop từ hai phép đo điện áp có thời điểm phù hợp. Chỉ dùng để đánh giá sụt áp đường công suất khi đầu ra ON và dữ liệu hợp lệ theo Design Spec.
- Giá trị dùng cho bảo vệ phải đáp ứng thời gian ngắt toàn đường. Lọc để hiển thị hoặc làm mượt số liệu phải được phối hợp để bảo toàn yêu cầu này.
- Nhận biết dữ liệu ngoài miền biểu diễn, lỗi chuyển đổi hoặc lỗi truyền dữ liệu mà phần cứng/driver thực sự cung cấp. Dữ liệu đo hợp lệ có thể nằm trong miền lỗi về điện.

### Cơ chế sơ bộ và trường hợp bất thường

Dùng ADC và cơ sở thời gian của MCU để thu thập, quy đổi rồi công bố bộ dữ liệu kèm thời điểm và trạng thái hợp lệ. DMA, ngắt, lọc và hiệu chuẩn được đánh giá khi thiết kế. Miền thông tin điện áp 0–20 V và dòng ít nhất 0–3 A kế thừa HW Spec; yêu cầu sai số chỉ áp dụng trong các miền đã quy định.

Hành vi đã được nhóm xác nhận là khi dữ liệu giám sát bắt buộc bị xác định mất hiệu lực, nếu đầu ra đang OFF thì ngăn ON; nếu đang ON thì yêu cầu Power Switch OFF, ghi nhận lỗi giám sát và vào FAULT. Khi khởi tạo còn đang chờ bộ dữ liệu đầu tiên, giữ INITIALIZING/OFF. Ngưỡng quá hạn, cách xác định lỗi, thời gian chờ và tiêu chí phục hồi tương ứng còn TBD. Không dùng giá trị cũ để tiếp tục kết luận hệ thống an toàn sau khi đã xác định dữ liệu mất hiệu lực.

Kiểm chứng dự kiến gồm đưa dữ liệu chuẩn tại các điểm trong miền đo, thử biên quy đổi, dữ liệu thiếu/quá hạn, sai lệch thời điểm hai kênh điện áp và đo chu kỳ cập nhật khi UI/chẩn đoán cùng hoạt động.

## 4. Quản lý cấu hình vận hành

### 4.1. Các nhóm tham số

| Nhóm | Tham số cấu hình |
| --- | --- |
| V_in | Ngưỡng cảnh báo thấp/cao, ngưỡng ngắt thấp/cao; thời gian cho phép thấp áp và quá áp tương ứng |
| I_load | Ngưỡng cảnh báo dòng cao, ngưỡng ngắt quá dòng phần mềm; thời gian cho phép dòng cao |
| V_drop | Ngưỡng cảnh báo và ngưỡng ngắt sụt áp |

Các giới hạn của sản phẩm và ngưỡng phần cứng HW_OCP thuộc ràng buộc thiết kế. Giá trị mặc định, khoảng điều chỉnh, bước chỉnh và đơn vị hiển thị chi tiết còn TBD. Các profile RESISTIVE, ELECTRONIC và FAN có thể cung cấp bộ tham số phù hợp nhưng dùng chung quy tắc đánh giá.

### 4.2. Vòng đời và kiểm tra cấu hình

- Phân biệt cấu hình mặc định, cấu hình đang áp dụng và cấu hình đề nghị. Sau reset/cấp nguồn, nạp mặc định; giá trị do người dùng chỉnh không thay bộ mặc định.
- Chỉ áp dụng thay đổi trong READY hoặc FAULT với yêu cầu đầu ra OFF. RUNNING/WARNING từ chối và thông báo cần tắt tải trước. INITIALIZING/RECOVERING không áp dụng cấu hình mới.
- Kiểm tra toàn bộ tham số trước khi thay cấu hình đang áp dụng. Bộ không hợp lệ bị từ chối kèm lý do, giữ bộ hợp lệ gần nhất; việc từ chối riêng lẻ không tạo FAULT.
- Kiểm tra quan hệ ngưỡng cảnh báo/ngắt, miền giá trị và thời gian. Các ngưỡng/thời gian phải bảo đảm giới hạn liên tục và ngắn hạn, có xét sai số và độ trễ ngắt.
- Giới hạn dòng tải phải phối hợp khả năng nguồn sau khi dành dự phòng dòng điều khiển quy về đầu vào, giới hạn sản phẩm và profile. Khả năng cấp dòng ngắn hạn của nguồn phải được xét riêng.
- Mặc định phải được kiểm tra trong Startup Checks. Bộ mặc định không hợp lệ khiến khởi động thất bại; thiếu thông số nguồn/tải thực tế phải được thông báo để người dùng thiết lập phù hợp.

Áp dụng cấu hình trong FAULT vẫn giữ lỗi và OFF, chờ yêu cầu phục hồi riêng. Áp dụng trong READY vẫn giữ OFF và đánh giá lại điều kiện hiện tại theo bộ mới trước khi xử lý lệnh ON. Cơ chế sơ bộ là chỉnh trên bản đề nghị, kiểm tra và thay toàn bộ cấu hình được chấp nhận trong một lần cập nhật nhất quán.

Kiểm chứng dự kiến gồm cấu hình đúng/sai quan hệ, ngoài miền, không đủ ngân sách nguồn, đổi cấu hình ở từng trạng thái và reset sau khi đã chỉnh cấu hình. Các giá trị trong phép thử cụ thể được xác định khi hoàn thiện danh mục tham số.

## 5. Giám sát và bảo vệ phần mềm

### 5.1. Điều kiện và phản ứng

| Nhóm | Cảnh báo khi chưa đạt điều kiện ngắt | Điều kiện ngắt |
| --- | --- | --- |
| V_in thấp | Trong vùng cảnh báo thấp áp, chưa hết thời gian cho phép | Đạt ngưỡng ngắt thấp áp hoặc hết thời gian cho phép liên tục trong vùng cảnh báo |
| V_in cao | Trong vùng cảnh báo quá áp, chưa hết thời gian cho phép | Đạt ngưỡng ngắt quá áp hoặc hết thời gian cho phép liên tục trong vùng cảnh báo |
| I_load | Vượt ngưỡng cảnh báo dòng cao, chưa vượt ngưỡng ngắt và chưa hết thời gian cho phép | Vượt ngưỡng ngắt phần mềm hoặc hết thời gian cho phép liên tục trong vùng cảnh báo dòng cao |
| V_drop khi ON | Đạt ngưỡng cảnh báo, chưa đạt ngưỡng ngắt | Đạt ngưỡng ngắt; không đặt thêm thời gian cho phép cảnh báo sụt áp |
| HW_OCP | Không cần cảnh báo trước | Trạng thái phần cứng báo lỗi được giữ; MCU yêu cầu OFF và ghi nhận FAULT |

Các điều kiện kế thừa Design Spec mục 6.4 và Product Requirements mục 5.1. Chi tiết so sánh tại điểm biên, hysteresis và xử lý nhiễu cần được xác định cùng danh mục ngưỡng; áp dụng nhất quán cho cấu hình, giám sát, Startup Checks và Recovery Checks.

### 5.2. Thời gian và nhiều nguyên nhân

- Tính thời gian riêng cho thấp áp, quá áp và dòng cao, theo từng khoảng liên tục. Điều kiện tương ứng hết thì đặt lại bộ đếm đó; các bộ đếm còn lại tiếp tục theo điều kiện riêng.
- Bộ đếm đạt hạn trong khi điều kiện vẫn tồn tại thì tạo điều kiện ngắt. Ngắt theo mức được xử lý ngay trong ngân sách phản ứng, không chờ bộ đếm.
- Cơ sở thời gian phải đo được thời gian trôi qua thực tế, kể cả khi vòng xử lý không đều. Cách tính phải xử lý đúng giới hạn biểu diễn và tràn bộ đếm thời gian.
- Lưu riêng các cảnh báo hiện hành và các nguyên nhân lỗi được phát hiện. Khi đang ON, còn ít nhất một cảnh báo và không có lỗi phải ngắt thì WARNING; hết tất cả cảnh báo mới RUNNING.
- Fault ưu tiên hơn warning và lệnh ON. Ngắt đầu ra phải được thực hiện trước các thao tác hiển thị/log có thể kéo dài.
- Dòng khởi động và dòng tăng cao trong runtime dùng cùng quy tắc. Cấu hình và độ trễ phải bảo đảm miền >1–2 A không bị kéo dài quá 1 s. Các đợt quá độ lặp lại còn phụ thuộc ràng buộc nhiệt của phần cứng.

### 5.3. Diễn giải khi OFF

Khi OFF vẫn đo và theo dõi V_in, V_load, I_load. Không đánh giá V_drop như sụt áp đường dẫn đang ON; V_load thấp và I_load gần 0 không tự là lỗi. Điện áp dư ở đầu ra không tự chứng minh Switch hỏng hoặc tải cấp ngược. Các lỗi chẩn đoán riêng dựa trên điện áp/dòng khi OFF chỉ được bổ sung sau khi có tiêu chí và khả năng quan sát phù hợp.

Khi ON, V_load giảm được xem xét cùng V_in thông qua V_drop và điều kiện nguồn. Không đặt thêm lỗi thấp V_load trùng với các điều kiện này. Giới hạn sụt áp thiết kế 0,2 V tại 1 A và 0,4 V tại 2 A được kiểm chứng riêng theo PR-PERF-12, 13; các con số đó không tự trở thành ngưỡng cảnh báo/ngắt.

Cơ chế sơ bộ là đánh giá các điều kiện độc lập rồi tổng hợp warning, fault và hành động ngắt. Kiểm chứng gồm từng ngưỡng, thời gian trước/đúng/sau hạn, cảnh báo ngắt quãng, nhiều điều kiện đồng thời, quá độ khởi động và sự kiện HW_OCP trong lúc MCU đang xử lý UI.

## 6. Quản lý trạng thái và đầu ra

### 6.1. Trạng thái và chuyển trạng thái

State diagram chuẩn nằm trong [Design Spec mục 6.1](design-specification.md#61-state-diagram). Bảng dưới cụ thể hóa trách nhiệm firmware.

| Trạng thái | MCU_ENABLE | Công việc chính |
| --- | --- | --- |
| INITIALIZING | LOW | Đưa điều khiển về OFF, nạp mặc định, khởi tạo ngoại vi/đo lường và thực hiện Startup Checks |
| READY | LOW | Giám sát liên tục, cho chỉnh cấu hình, chờ lệnh ON mới |
| RUNNING | HIGH | Cấp tải và giám sát; có warning thì WARNING, có fault thì OFF/FAULT |
| WARNING | HIGH | Duy trì cấp tải trong miền còn cho phép; hết mọi warning thì RUNNING, đạt điều kiện ngắt thì OFF/FAULT |
| FAULT | LOW | Giữ lỗi và thông báo, cho chỉnh cấu hình; chỉ bắt đầu phục hồi khi có yêu cầu riêng |
| RECOVERING | LOW | Kiểm tra và xóa lỗi đúng nơi giữ; thành công về READY/OFF, thất bại về FAULT/OFF |

HIGH là yêu cầu cấp điện trong RUNNING/WARNING. HW_OCP hoặc nguồn điều khiển chưa hợp lệ vẫn có thể giữ Switch OFF theo phần cứng. Khi phát hiện lỗi trong bất kỳ trạng thái nào, firmware ưu tiên ngắt/giữ OFF và xử lý FAULT.

READY nhận ON trực tiếp theo điều kiện hiện tại; không thêm trạng thái Enable Checks. Lệnh OFF trong RUNNING/WARNING về READY nếu không có lỗi phải giữ. Reset từ mọi trạng thái về INITIALIZING/OFF. Các lệnh không phù hợp trạng thái bị từ chối hoặc thông báo theo chức năng tương ứng, không được lưu để tự bật sau đó.

### 6.2. Startup Checks

1. Khởi tạo và đọc được thông tin đo/trạng thái bảo vệ hợp lệ; `POWER_GOOD_MCU` đang HIGH, tức nguồn của đường bảo vệ hợp lệ theo HW Spec mục 5.11.
2. Cấu hình mặc định đang áp dụng hợp lệ.
3. V_in nằm trong miền cho phép cấp điện theo cấu hình.
4. Không có lỗi hiện hành hoặc lỗi đang giữ ngăn cấp điện, gồm HW_OCP.

Chưa đủ dữ liệu thì giữ INITIALIZING/OFF. Tất cả điều kiện đạt thì READY/OFF. Xác định lỗi hoặc khởi tạo thất bại thì FAULT/OFF. Thời hạn chờ và tiêu chí nguồn/đo sẵn sàng còn TBD; thời gian khởi động nguồn nội bộ ≤100 ms trong HW Spec không tự là timeout khởi động firmware.

Khi khởi động, CLEAR_FAULT_REQUEST giữ LOW. Firmware **không dùng cờ POR (`PORRSTF`) để suy ra trạng thái latch HW_OCP**. Lý do: latch CMOS có thể vẫn giữ lỗi khi rail 3,3 V sụt xuống dưới ngưỡng PDR của STM32, nên cờ POR không chứng minh latch đã mất thông tin. Thay vào đó, firmware dùng **bản ghi lỗi HW_OCP lưu trong Flash** (mục 7) để phân biệt lỗi thật với trạng thái ngẫu nhiên của latch lúc cấp nguồn:

| Bản ghi HW_OCP trong Flash | Latch (`OVERCURRENT_FAULT_STATUS`) | Xử lý |
| --- | --- | --- |
| Có, hợp lệ | Lỗi | FAULT, nguyên nhân quá dòng phần cứng; phục hồi thủ công theo mục 6.3 |
| Có, hợp lệ | Không lỗi | FAULT, nguyên nhân quá dòng phần cứng (latch đã mất thông tin do mất nguồn); phục hồi thủ công |
| Không có | Lỗi | Coi là trạng thái latch không hợp lệ lúc cấp nguồn: gửi **một** xung clear theo mục 7 rồi đọc lại. Hết lỗi thì tiếp tục Startup Checks. Còn lỗi thì đây là lỗi hiện hành: ghi bản ghi và vào FAULT. Thao tác này không tạo ON. |
| Không có | Không lỗi | Tiếp tục Startup Checks |
| Hỏng hoặc không đọc được (ghi dở, sai CRC) | Bất kỳ | Xử lý như "Có, hợp lệ": FAULT, phục hồi thủ công |

Sau reset, lỗi phần mềm của phiên trước không được khôi phục; đánh giá lại điều kiện hiện tại và vẫn cần lệnh ON riêng. Cờ lý do reset (`RCC_CSR`) vẫn được đọc và xóa bằng `RMVF` để chẩn đoán và ghi log, nhưng không quyết định việc clear.

Kẽ hở được chấp nhận: nếu MCU bị reset trong khoảng vài chục µs giữa lúc latch bật lỗi và lúc ghi xong bản ghi, lần khởi động sau sẽ rơi vào hàng "Không có / Lỗi" và tự clear. Tải vẫn OFF và vẫn cần lệnh ON mới.

### 6.3. Recovery Checks

- Chỉ bắt đầu từ yêu cầu phục hồi của người dùng trong FAULT; giữ MCU_ENABLE LOW xuyên suốt quá trình.
- Kiểm tra dữ liệu sẵn sàng/hợp lệ, cấu hình đang áp dụng hợp lệ và V_in trong miền cho phép. Nếu cấu hình đã đổi trong FAULT, dùng bộ mới được chấp nhận.
- Kiểm tra theo từng nguyên nhân lỗi những điều kiện có thể quan sát khi OFF; lỗi mới ngăn phục hồi phải được ghi nhận và xử lý.
- Lỗi firmware được xóa khi các điều kiện tương ứng đạt. Nếu HW_OCP giữ lỗi, thực hiện quy trình clear tại mục 7 và xác nhận thành công.
- Chỉ về READY khi mọi lỗi ngăn phục hồi được xử lý. Kiểm tra/clear thất bại thì FAULT và hiển thị lý do; không tự thử lại.

Với quá tải/ngắn mạch, dòng gần 0 sau ngắt không xác nhận tải đã được sửa. Phục hồi là cho phép người dùng thử cấp điện lại sau khi xử lý nguyên nhân; các đường bảo vệ phải tiếp tục hoạt động khi nhận ON sau đó. Không yêu cầu trạng thái latch bằng 0 trước khi gửi clear vì đó là trạng thái đang cần xóa.

### 6.4. Phân xử lệnh và sự kiện

Fault được ưu tiên hơn ON, OFF bình thường hoặc kết quả phục hồi. Lỗi xuất hiện trong cùng lượt với yêu cầu ON phải giữ OFF/FAULT; lỗi mới trong lúc phục hồi phải ngăn kết luận phục hồi thành công.

Nhóm đã xác nhận OFF ưu tiên ON khi hai lệnh cùng xuất hiện trong một lượt xử lý mà không có fault. Thực hiện yêu cầu ngắt Power Switch và bỏ lệnh ON đó; muốn bật lại cần lệnh ON mới. Một thao tác phục hồi không đồng thời tạo ON; bỏ các yêu cầu ON cũ khi reset, vào FAULT hoặc bắt đầu phục hồi. Sau khi READY cần một yêu cầu ON mới. Cơ chế sơ bộ là phân xử sự kiện tại đầu mối quản lý trạng thái và cập nhật đầu ra theo kết quả ưu tiên.

Kiểm chứng gồm mọi nhánh chuyển trong Design Spec, lệnh sai trạng thái, lệnh/sự kiện đồng thời, fault lúc clear, reset ở cả sáu trạng thái và phục hồi nhiều nguyên nhân.

## 7. Phối hợp HW_OCP

| Tín hiệu | Firmware sử dụng | Trạng thái mặc định |
| --- | --- | --- |
| MCU_ENABLE | HIGH yêu cầu ON; LOW yêu cầu OFF | LOW |
| OVERCURRENT_FAULT_STATUS | HIGH cho biết lỗi quá dòng phần cứng đang được giữ. Đầu vào có ngắt cạnh lên (EXTI, PB12) để ghi bản ghi Flash ngay | Đọc như một đầu vào, chỉ dùng sau khi interface hợp lệ |
| CLEAR_FAULT_REQUEST | HIGH yêu cầu HW_OCP xóa lỗi trong phục hồi, hoặc một lần khi khởi động nếu latch báo lỗi mà không có bản ghi Flash (mục 6.2) | LOW |
| POWER_GOOD_MCU | HIGH: nguồn của đường bảo vệ hợp lệ. Cạnh xuống báo sự cố nguồn (thấp áp Vin, thấp áp 5 V hoặc quá áp) | Đầu vào có ngắt cạnh xuống |

Các tín hiệu dùng logic 3,3 V theo HW Spec. SHUTDOWN_REQUEST là đường trực tiếp HW_OCP → Power Switch, hoạt động độc lập với việc MCU tiếp nhận sự kiện.

Phần cứng đã cắt tải ngay khi `POWER_GOOD` LOW. Firmware chịu phần **giữ OFF**: ở ngắt cạnh xuống của `POWER_GOOD_MCU`, đưa MCU_ENABLE LOW trong ISR, ghi nguyên nhân "sự cố nguồn", vào FAULT và bỏ mọi yêu cầu ON cũ. Có thể dùng ADC V_IN để phân biệt quá áp với thấp áp khi hiển thị. Cờ pending EXTI bắt được cả xung ngắn. Sau khi `POWER_GOOD` lên lại, Q9 có thể bắt đầu dẫn sau chỉ khoảng 0,14–0,31 ms (HW Spec mục 5.11), nên ISR phải hạ MCU_ENABLE ngay trong ngắt; cần có watchdog. Phục hồi từ sự cố nguồn theo mục 6.3, yêu cầu `POWER_GOOD_MCU` HIGH. Sự cố nguồn không tác động tới latch HW_OCP: lỗi quá dòng đang giữ vẫn giữ.

Khi nhận trạng thái HW_OCP có lỗi (ngắt cạnh lên PB12), firmware yêu cầu MCU_ENABLE LOW, **ghi ngay bản ghi "HW_OCP đang giữ" vào Flash**, ghi nhận nguyên nhân quá dòng phần cứng và giữ FAULT.

**Bản ghi lỗi HW_OCP trong Flash:**
- Dùng riêng một trang Flash làm nhật ký, **xóa sẵn từ trước**. Khi có lỗi chỉ lập trình một ô 16 bit (khoảng 50–70 µs), không xóa trang trong ISR vì xóa trang mất khoảng 20–40 ms.
- Mỗi bản ghi có mã đánh dấu và kiểm tra (CRC hoặc mẫu bit bổ sung) để phát hiện ghi dở khi mất nguồn.
- Bản ghi chỉ được xóa (đánh dấu đã xử lý) sau khi quy trình phục hồi HW_OCP thành công: clear latch, xác nhận latch hết lỗi.
- Các bản ghi được ghi nối tiếp trong trang; chỉ xóa trang khi đầy và ở thời điểm an toàn (OFF, nguồn ổn định). Tuổi thọ khoảng 10.000 lần xóa mỗi trang là đủ vì sự kiện HW_OCP hiếm.
- Hệ quả: lỗi HW_OCP được giữ qua cả lần rút nguồn cắm lại, cho tới khi người dùng phục hồi. Chỉ biết sự kiện quá dòng được phần cứng báo; dữ liệu ADC gần thời điểm MCU đọc lại phải được mô tả theo thời điểm đo, không gán thành dòng đỉnh/điện áp đúng lúc sự cố.

Trình tự sơ bộ khi phục hồi HW_OCP gồm giữ enable LOW → hoàn tất điều kiện phục hồi → phát clear theo timing phần cứng → đưa clear về LOW → xác nhận trạng thái lỗi đã hết → xóa bản ghi HW_OCP trong Flash. Lỗi ưu tiên clear theo phần cứng. Không xác nhận được hoặc lỗi tái xuất hiện thì giữ OFF/FAULT. Chỉ clear lỗi phần mềm không bắt buộc gửi clear phần cứng nếu HW_OCP không giữ lỗi.

Thời gian clear, thời gian chờ xác nhận và cách đọc ngắt/polling còn TBD. Độ trễ MCU nhận sự kiện được phân bổ khi thiết kế để cập nhật trạng thái/hiển thị đúng yêu cầu; giới hạn ngắt phần cứng 100 µs thuộc đường phần cứng.

Kiểm chứng bằng kích hoạt HW_OCP, giữ/tái xuất hiện lỗi khi clear, MCU reset khi latch còn lỗi và xác nhận không có lần enable ngoài ý muốn trong toàn quy trình.

## 8. Buttons và Display

### 8.1. Nhận thao tác người dùng

- Chuyển tín hiệu nút thành các yêu cầu ON, OFF, phục hồi, chọn/chỉnh, xác nhận hoặc hủy cấu hình theo bố trí UI sẽ chọn.
- Chống dội phối hợp phần cứng để một thao tác được nhận đúng số lần. Nhấn giữ không lặp lệnh ON/phục hồi; chức năng tăng/giảm tham số có thể hỗ trợ lặp khi giữ theo thiết kế UI.
- Kiểm tra tính hợp lệ của lệnh theo trạng thái trước khi thực hiện. Yêu cầu ON ngoài READY không được chờ để tự thực hiện khi READY.
- Bản tham số đang chỉnh chỉ trở thành cấu hình áp dụng sau thao tác xác nhận và kiểm tra toàn bộ. Hủy chỉnh giữ nguyên bộ đang áp dụng.

Nhóm đã xác nhận khi nút ON/phục hồi đang bị giữ lúc cấp nguồn hoặc MCU reset, chỉ nhận thao tác mới sau khi người dùng đã nhả rồi nhấn lại. Quy tắc vẫn áp dụng nếu một nút vật lý được gán nhiều chức năng. Số nút, cách phân biệt nhấn ngắn/dài, chống dội và tốc độ lặp chỉnh còn TBD theo HW/UI thực tế.

### 8.2. Nội dung và cập nhật hiển thị

| Nhóm thông tin | Nội dung cần hỗ trợ |
| --- | --- |
| Vận hành | V_in, V_load, I_load và đơn vị; trạng thái hệ thống, yêu cầu đầu ra, trạng thái lỗi phần cứng |
| Cảnh báo/lỗi | Các cảnh báo hiện hành, nguyên nhân lỗi đang giữ và kết quả phục hồi |
| Cấu hình | Bộ đang áp dụng, tham số đang chỉnh, trạng thái xác nhận và lý do từ chối |
| Dữ liệu chưa hợp lệ | Thể hiện chưa có hoặc mất hiệu lực dữ liệu; tránh hiển thị giá trị cũ như phép đo hiện tại |

Chu kỳ cập nhật ≤200 ms áp dụng trong vận hành, kể cả khi ở menu cấu hình. Lỗi/nguyên nhân mới được đưa vào lần cập nhật tiếp theo trong giới hạn này khi giao diện còn hoạt động. Có thể chia nhiều màn hình để đọc được đầy đủ thông tin; cách bố trí cụ thể theo model màn hình.

Cơ chế sơ bộ là UI sử dụng bản thông tin được công bố từ nhóm đo/trạng thái và gửi lệnh tới đầu mối xử lý. Việc nhập tham số, vẽ màn hình hoặc chờ thiết bị giao tiếp phải được tổ chức để đáp ứng timing bảo vệ. Lỗi Display/UART chỉ được phân loại thành fault ngăn cấp điện sau khi xác định chính sách tương ứng tại mục 11.

Kiểm chứng bằng dội/giữ nút, nút bị giữ lúc reset, hủy/xác nhận cấu hình, thay đổi trạng thái trong menu và đo chu kỳ hiển thị khi xử lý bảo vệ đồng thời.

## 9. Timing và tổ chức thực thi

| Đường xử lý | Yêu cầu | Nội dung cần phân bổ khi thiết kế |
| --- | --- | --- |
| Đo phục vụ MCU | Bộ dữ liệu mới mỗi ≤1 ms | Thu thập, quy đổi, công bố và tính hợp lệ |
| Ngắt phần mềm theo mức | ≤10 ms từ điều kiện thực tế đạt ngưỡng tới ngắt thực tế | Sensing, chờ lấy mẫu, lọc, đánh giá, phân xử, GPIO và Switch |
| Ngắt theo thời gian | ≤10 ms từ khi hết thời gian cho phép trong khi điều kiện còn tồn tại tới ngắt thực tế | Sai lệch xác định thời điểm, xử lý bộ đếm, phân xử và thực thi ngắt |
| Hiển thị | Khoảng cách cập nhật ≤200 ms | Chuẩn bị dữ liệu, giao tiếp và hoàn thành cập nhật |
| Bảo vệ nhanh | Toàn đường phần cứng ≤100 µs | Phân bổ 20/70/10 µs trong HW Spec; firmware không được vô hiệu hóa đường này trong vận hành |

Chu kỳ đo 1 ms và chu kỳ hiển thị 200 ms phục vụ các yêu cầu khác nhau. Phải chứng minh ngắt phần mềm 10 ms trên toàn đường, gồm độ trễ của dữ liệu, cả khi nhiều công việc xảy ra đồng thời. Giới hạn ngắn hạn 1 s phải tính cả độ trễ ngắt vào cấu hình thời gian cho phép.

Cơ chế sơ bộ là tách công việc phản ứng bảo vệ khỏi công việc UI/chẩn đoán có độ trễ lớn, sử dụng cơ sở thời gian chung và trao đổi dữ liệu nhất quán. Bare-metal hoặc RTOS được chọn sau khi đánh giá tài nguyên và thời gian thực thi xấu nhất. Nếu có công việc đồng thời, cách đồng bộ phải tránh mất sự kiện lỗi, trộn cấu hình và trì hoãn đường ngắt.

Đo tải CPU, bộ nhớ và thời gian thực thi khi triển khai; ngân sách CPU/RAM/Flash, mức ưu tiên, chu kỳ từng công việc và timeout giao tiếp còn TBD. Mọi vòng chờ thiết bị hoặc tác vụ kéo dài cần có cách kết thúc/nhường xử lý phù hợp ngân sách timing. Watchdog và chính sách phục hồi lỗi treo được đánh giá trong Software Design, chưa là phương án đã chốt.

## 10. Chẩn đoán và kiểm chứng dự kiến

UART phục vụ debug/kiểm thử/logging theo Design Spec. Bản sơ bộ sử dụng UART để quan sát, không đặt yêu cầu điều khiển ON hoặc clear qua UART. Gói tin, tốc độ và mức log được xác định khi thiết kế. Quá tải kênh log không được làm mất xử lý bảo vệ hoặc trì hoãn ngắt.

Thông tin chẩn đoán nên cho phép đối chiếu thời điểm dữ liệu, trạng thái, cấu hình đang áp dụng, warning và các nguyên nhân fault mà MCU quan sát được. Lưu lỗi trong phiên vận hành phục vụ FR-10; lưu bền vững qua reset/mất nguồn không thuộc yêu cầu hiện tại.

| Nhóm kiểm chứng | Tình huống cần có | Căn cứ |
| --- | --- | --- |
| Đo và quy đổi | Điểm chuẩn, biên miền, dữ liệu thiếu/quá hạn, lệch thời điểm và sai số | FR-03 đến 05; PR-PERF-04 đến 07; HW Spec mục 5 |
| Cấu hình | Mặc định sau reset, hợp lệ/không hợp lệ, cập nhật trọn bộ, đổi ở từng trạng thái, ngân sách nguồn | FR-01; Design Spec mục 6.3 |
| Warning và ngắt | Từng ngưỡng/thời gian, bộ đếm độc lập, nhiều nguyên nhân, quá độ khởi động/runtime | FR-07 đến 10; PR-PERF-11 |
| State machine | Tất cả nhánh hợp lệ, sự kiện đồng thời, lệnh sai trạng thái và reset ở từng trạng thái | Design Spec mục 6.1–6.3 |
| Phục hồi | Lỗi MCU, HW_OCP và nhiều lỗi, clear thất bại, lỗi tái xuất hiện, thành công vẫn OFF | FR-11; Design Spec mục 6.5 |
| UI | Nhấn/giữ/dội phím, chỉnh/hủy cấu hình, menu khi có lỗi, tính nhất quán thông tin | FR-02, 06, 08; HW Spec mục 5.14–5.15 |
| Timing và tích hợp | Đo, bảo vệ, UI và UART đồng thời ở điều kiện tải xử lý lớn nhất | PR-PERF-08, 11; HW Spec mục 5.7 |

Kiểm chứng logic có thể dùng dữ liệu/sự kiện giả lập và quan sát quyết định. Kiểm chứng timing toàn đường, ADC và tác động Switch cần phối hợp phần cứng hoặc phương tiện thử phù hợp. Điều kiện thử, thiết bị, tiêu chí đạt và bằng chứng được lập trong Test Specification và lưu khi thực hiện kiểm chứng.

## 11. Những mục còn mở

| Mục | Nội dung cần hoàn thiện | Bước giải quyết |
| --- | --- | --- |
| Cấu hình mặc định và miền điều chỉnh | Giá trị, bước chỉnh, quan hệ ngưỡng tại biên, thời gian và profile; khả năng nguồn/dự phòng dòng điều khiển | Hoàn thiện bộ tham số phối hợp HW, trước khi chạy bảo vệ trên tải |
| Hysteresis và lọc | Tiêu chí vào/ra warning, độ trễ lọc, xử lý nhiễu và ảnh hưởng tới giới hạn thời gian | Software Design phối hợp đo lường/HW, kiểm thử quanh biên |
| Dữ liệu và tính sẵn sàng | Thời hạn dữ liệu, tiêu chí invalid, timeout khởi tạo, thời gian xác nhận nguồn/đường bảo vệ sẵn sàng | Phối hợp MCU/Sensing/HW_OCP trước hoàn tất khởi động và phục hồi |
| ADC và quy đổi | MCU/ADC, tham chiếu, hiệu chuẩn, hệ số, độ phân giải số và lệch thời điểm đo điện áp | Hardware/Software Design theo ngân sách sai số và timing |
| Recovery theo nguyên nhân | Điều kiện kiểm tra khi OFF, thời gian xác nhận ổn định, timing clear/đọc lại và timeout | Hoàn thiện cùng phần cứng bảo vệ và Test Specification |
| Các lỗi kỹ thuật bổ sung | Danh mục/tiêu chí lỗi ADC, driver, quá hạn dữ liệu, lỗi UI/UART; chính sách tương ứng và cách phục hồi | Rà theo khả năng phát hiện thực tế; hành vi ngăn ON hoặc OFF/FAULT khi dữ liệu mất hiệu lực tại mục 3 đã được xác nhận, tiêu chí và timing còn TBD |
| UI cụ thể | Loại màn hình, số nút, sơ đồ menu, thao tác chỉnh và hiển thị nhiều nguyên nhân | Phối hợp HW Spec; Software Design và kiểm thử thao tác |
| Tổ chức thực thi và tài nguyên | Bare-metal/RTOS, chu kỳ/ưu tiên, cơ chế đồng bộ, CPU/RAM/Flash và watchdog nếu dùng | Đánh giá sau khi chọn MCU, kiểm chứng thời gian xấu nhất |
| Debug/kiểm thử | Giao thức UART, mức log, tín hiệu đo timing, phương tiện thử và kịch bản chi tiết | Software Design và Test Specification |

Bản sơ bộ bao phủ các chức năng firmware trong kiến trúc hiện tại. Ba hành vi về dữ liệu giám sát mất hiệu lực, OFF ưu tiên ON đồng thời và nhả/nhấn lại nút ON/phục hồi đang giữ lúc khởi động đã được nhóm xác nhận. Các giá trị TBD phải được giải quyết ở bước liên quan trước khi dùng làm tiêu chí triển khai hoặc kiểm chứng cuối cùng. Các cơ chế tổ chức sơ bộ còn lại tiếp tục được rà khi duyệt nội dung tổng thể.
