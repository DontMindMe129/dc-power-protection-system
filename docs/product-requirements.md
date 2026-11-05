# Yêu cầu sản phẩm

> Phiên bản: 2
>
> Cập nhật: 2026-11-05
>
> Trạng thái: Đồng bộ theo Requirement v2; các mục tiêu thiết kế chưa phải kết quả kiểm chứng trên sản phẩm.

Tài liệu mô tả yêu cầu cấp sản phẩm. Các quyết định thiết kế chi tiết được quản lý trong [Concept](concept.md) và [Design Specification](design-specification.md); hai tài liệu này cần được đồng bộ riêng theo bộ yêu cầu phiên bản này.

## 1. Tên sản phẩm

Hệ thống giám sát và bảo vệ đường nguồn DC (DC Power Path Monitoring and Protection System).

## 2. Mục đích và phạm vi sử dụng

Hệ thống được mắc giữa nguồn DC và tải để đo điện áp đầu vào, điện áp tại tải và dòng điện cấp cho tải. Từ các giá trị đo và cấu hình do người dùng lựa chọn, hệ thống cảnh báo khi có bất thường và tự động ngắt cấp điện khi đạt điều kiện bảo vệ. Các giá trị đo, trạng thái hoạt động và nguyên nhân ngắt được hiển thị để người dùng theo dõi và xử lý.

Thiết bị được sử dụng với nguồn DC trong khoảng 5–15 V và tải nằm trong giới hạn dòng điện cho phép của thiết bị. Thiết bị chỉ thực hiện chức năng giám sát và đóng/ngắt đường cấp điện, không chịu trách nhiệm điều chỉnh điện áp và dòng cấp cho tải.

## 3. Đầu vào và đầu ra

Mã nhóm: PR-IO-XX (Product Requirement – Input/Output)

| Mã | Tên | Nội dung |
| --- | --- | --- |
| PR-IO-01 | Đầu vào công suất | Nguồn DC cấp vào thiết bị |
| PR-IO-02 | Đầu vào thông tin | Lệnh bật/tắt đầu ra, yêu cầu phục hồi sau ngắt bảo vệ và các thông số cấu hình vận hành do người dùng thiết lập |
| PR-IO-03 | Đầu ra công suất | Đường cấp điện tới tải có thể đóng/ngắt |
| PR-IO-04 | Đầu ra thông tin | Giá trị đo, trạng thái, cảnh báo và nguyên nhân ngắt |

Các thông số cấu hình vận hành bao gồm:

| Nhóm | Thông số | Đơn vị | Ý nghĩa |
| --- | --- | --- | --- |
| Điện áp đầu vào | Ngưỡng cảnh báo điện áp thấp | V | Cảnh báo khi `V_in` giảm đến ngưỡng này. |
|  | Ngưỡng ngắt điện áp thấp | V | Ngắt khi `V_in` đạt điều kiện thấp áp. |
|  | Ngưỡng cảnh báo điện áp cao | V | Cảnh báo khi `V_in` tăng đến ngưỡng này. |
|  | Ngưỡng ngắt điện áp cao | V | Ngắt khi `V_in` đạt điều kiện quá áp. |
|  | Thời gian cho phép thấp áp trong vùng cảnh báo | ms hoặc s | Thời gian tối đa `V_in` được phép liên tục nằm trong vùng cảnh báo thấp áp trước khi ngắt. |
|  | Thời gian cho phép quá áp trong vùng cảnh báo | ms hoặc s | Thời gian tối đa `V_in` được phép liên tục nằm trong vùng cảnh báo quá áp trước khi ngắt. |
| Dòng tải | Ngưỡng cảnh báo dòng cao | A | Cảnh báo và bắt đầu tính thời gian cho phép khi `I_load` vượt ngưỡng này. |
|  | Ngưỡng ngắt quá dòng | A | Phần mềm yêu cầu ngắt khi phát hiện `I_load` vượt ngưỡng này, không chờ hết thời gian cho phép trong vùng cảnh báo. |
|  | Thời gian cho phép dòng cao trong vùng cảnh báo | ms hoặc s | Thời gian tối đa `I_load` được phép liên tục ở vùng cảnh báo dòng cao trước khi ngắt. |
| Sụt áp đường công suất | Ngưỡng cảnh báo sụt áp | V | Cảnh báo khi `V_drop` = `V_in` − `V_load` đạt ngưỡng này trong lúc đầu ra bật. |
|  | Ngưỡng ngắt do sụt áp | V | Ngắt khi sụt áp đường công suất đạt điều kiện bảo vệ trong lúc đầu ra bật. |

## 4. Trường hợp sử dụng

Mã nhóm: UC-XX (Use Case)

### UC-01 — Thiết lập hệ thống

Khi người dùng kết nối nguồn DC, tải tiêu thụ và thiết lập các thông số cấu hình vận hành, hệ thống kiểm tra cấu hình và giữ đầu ra ở trạng thái tắt. Nếu cấu hình hợp lệ, hệ thống cho phép người dùng bật đầu ra. Nếu cấu hình không hợp lệ, hệ thống không áp dụng cấu hình đó.

### UC-02 — Cấp điện và giám sát tải

Khi người dùng yêu cầu bật đầu ra, hệ thống kiểm tra điều kiện cấp điện. Nếu đủ điều kiện, hệ thống đóng đường công suất, đo và hiển thị điện áp đầu vào, điện áp tại tải và dòng tải. Tải được cấp điện và giám sát. Nếu không đáp ứng điều kiện cấp điện, đầu ra giữ tắt và hệ thống thông báo lý do.

### UC-03 — Tải có dòng tăng cao ngắn hạn

Khi dòng tải tăng cao trong thời gian ngắn lúc bật tải, hệ thống so sánh mức dòng và thời gian tồn tại với giới hạn của cấu hình đã chọn. Hệ thống tiếp tục cấp điện nếu dòng khởi động nằm trong giới hạn cho phép và ngắt bảo vệ nếu đạt điều kiện ngắt.

### UC-04 — Cảnh báo bất thường

Khi điện áp đầu vào, dòng tải hoặc sụt áp trên đường công suất đạt điều kiện cảnh báo nhưng chưa đạt điều kiện ngắt, hệ thống hiển thị cảnh báo và tiếp tục giám sát các giá trị đo. Người dùng nhận biết bất thường và tải tiếp tục được cấp điện khi chưa đạt điều kiện ngắt.

### UC-05 — Ngắt bảo vệ

Khi phát hiện điều kiện cần ngắt bảo vệ, hệ thống ngắt đường cấp điện từ nguồn đến tải, ghi nhận và hiển thị nguyên nhân ngắt. Đầu ra được giữ ở trạng thái tắt để chờ phục hồi.

### UC-06 — Phục hồi sau lỗi

Sau khi xử lý nguyên nhân lỗi, người dùng yêu cầu phục hồi. Hệ thống kiểm tra các điều kiện phục hồi. Nếu đủ điều kiện, hệ thống cho phép người dùng bật lại đầu ra. Nếu chưa đủ điều kiện, hệ thống tiếp tục giữ đầu ra tắt và thông báo lý do.

### UC-07 — Thay đổi nguồn hoặc tải

Khi người dùng tắt đầu ra, thay đổi nguồn hoặc tải và chọn lại cấu hình tương ứng, hệ thống kiểm tra cấu hình mới và các điều kiện cho phép cấp điện. Cấu hình hợp lệ được áp dụng. Đầu ra vẫn tắt và chờ lệnh bật của người dùng.

## 5. Yêu cầu chức năng

Mã nhóm: FR-XX (Functional Requirement)

| Mã | Chức năng | Nội dung yêu cầu |
| --- | --- | --- |
| FR-01 | Thiết lập cấu hình | Cho phép người dùng thiết lập các thông số cấu hình vận hành khi đầu ra đang tắt. Chỉ áp dụng khi các thông số nằm trong giới hạn chung của thiết bị và không mâu thuẫn với nhau. Nếu không hợp lệ, giữ nguyên cấu hình đang dùng và thông báo lý do. |
| FR-02 | Bật/tắt đầu ra | Cho phép bật/tắt đầu ra theo lệnh người dùng. Chỉ bật khi đã áp dụng cấu hình hợp lệ, `V_in` nằm trong khoảng cho phép của cấu hình đó và không còn lỗi đang được giữ sau ngắt bảo vệ. Nếu chưa đáp ứng, giữ đầu ra tắt và thông báo lý do. Cho phép người dùng yêu cầu tắt trong quá trình hoạt động. |
| FR-03 | Đo điện áp đầu vào | Đo điện áp `V_in` tại đầu nối nguồn vào để theo dõi nguồn DC cấp cho thiết bị. |
| FR-04 | Đo điện áp tại tải | Đo điện áp `V_load` tại đầu nối tải ra khi đầu ra đang bật và khi đã tắt. |
| FR-05 | Đo dòng tải | Đo dòng điện `I_load` cấp cho tải. Giá trị đo không bao gồm dòng cấp cho nhánh điều khiển. |
| FR-06 | Hiển thị thông tin vận hành | Cập nhật điện áp đầu vào, điện áp tại tải, dòng tải, cảnh báo và trạng thái bật/tắt đầu ra trên giao diện để người dùng theo dõi. |
| FR-07 | Đánh giá điều kiện vận hành | Đánh giá `V_in`, `I_load`, sụt áp `V_drop` = `V_in` − `V_load` và thời gian tồn tại của các điều kiện bất thường theo cấu hình đang áp dụng để xác định miền vận hành hiện tại của hệ thống. Chỉ đánh giá sụt áp đường công suất khi đầu ra bật. |
| FR-08 | Cảnh báo | Hiển thị đại lượng đang có giá trị trong miền cảnh báo theo kết quả đánh giá ở FR-07. Tiếp tục cấp điện và giám sát khi chưa đạt điều kiện ngắt. Xóa cảnh báo tương ứng khi điều kiện cảnh báo đó không còn. |
| FR-09 | Ngắt bảo vệ | Ngắt cấp điện cho tải khi một trong các đại lượng đạt điều kiện ngắt về mức hoặc thời gian. |
| FR-10 | Giữ trạng thái sau lỗi | Sau ngắt bảo vệ, giữ đầu ra tắt, ghi nhận nguyên nhân và chờ người dùng yêu cầu phục hồi. Đồng thời hiển thị trạng thái và nguyên nhân ngắt khi khối điều khiển còn được cấp nguồn. |
| FR-11 | Phục hồi sau lỗi | Khi nhận yêu cầu phục hồi, kiểm tra các điều kiện tương ứng với nguyên nhân ngắt theo cấu hình đang áp dụng. Nếu đạt, xóa trạng thái lỗi và giữ đầu ra tắt, chờ lệnh bật riêng. Nếu chưa đạt, tiếp tục giữ lỗi, giữ đầu ra tắt và thông báo lý do. |

### 5.1. Các miền vận hành

**Bình thường:** Chưa có điều kiện cảnh báo hoặc ngắt về điện áp đầu vào, dòng tải và sụt áp theo cấu hình đang áp dụng. Sụt áp đường công suất chỉ được xét khi đầu ra bật.

**Cảnh báo:** `V_in` nằm trong vùng cảnh báo thấp áp/quá áp nhưng chưa hết thời gian cho phép tương ứng; hoặc sụt áp đạt ngưỡng cảnh báo nhưng chưa đạt ngưỡng ngắt; hoặc `I_load` vượt ngưỡng cảnh báo dòng cao nhưng chưa vượt ngưỡng ngắt quá dòng và chưa hết thời gian cho phép. Chỉ xác định miền cảnh báo khi chưa có điều kiện ngắt.

**Ngắt:** Chỉ cần có một trong các điều kiện sau: `V_in` đạt ngưỡng ngắt thấp áp/quá áp; `V_in` liên tục ở vùng cảnh báo thấp áp/quá áp đến hết thời gian cho phép; sụt áp đạt ngưỡng ngắt khi đầu ra bật; `I_load` vượt ngưỡng ngắt quá dòng hoặc liên tục ở vùng cảnh báo dòng cao đến hết thời gian cho phép; hoặc bảo vệ quá dòng nhanh bằng phần cứng tác động. Miền ngắt được ưu tiên khi đồng thời có cảnh báo.

Thời gian cho phép được tính riêng cho thấp áp, quá áp và dòng cao theo từng khoảng liên tục; đặt lại khi điều kiện tương ứng không còn. Đạt điều kiện ngắt theo mức thì không chờ thời gian này. Dòng khởi động và dòng tăng cao khi vận hành dùng chung các ngưỡng và thời gian cấu hình; bảo vệ quá dòng nhanh bằng phần cứng tác động độc lập.

## 6. Yêu cầu hiệu năng

Các chỉ tiêu dưới đây xác định miền vận hành và mức hiệu năng mà thiết bị cần đáp ứng. Giới hạn chung của thiết bị không thay đổi theo cấu hình người dùng. Các ngưỡng cảnh báo, ngưỡng ngắt và thời gian cho phép phải được cấu hình phù hợp với những giới hạn này.

Miền điện áp 5–15 V đã được lựa chọn cho đề tài. Các giá trị còn lại là mục tiêu thiết kế đang được xây dựng và đánh giá tính khả thi, chưa phải kết quả đo trên sản phẩm.

### 6.1. Các chỉ tiêu hiệu năng

| Mã | Thông số | Giá trị mục tiêu | Ý nghĩa và điều kiện áp dụng |
| --- | --- | --- | --- |
| PR-PERF-01 | Miền điện áp hoạt động | 5–15 V DC | Miền điện áp tại đầu nối nguồn vào mà thiết bị phải hoạt động đúng. Đây là giới hạn chung của thiết bị, không phải khoảng điện áp phù hợp với mọi tải. |
| PR-PERF-02 | Dòng tải liên tục cho phép | 0–1 A | Miền dòng tải được phép đi qua liên tục, trong đó thiết bị phải đáp ứng các yêu cầu chức năng và đo lường. Đây cũng là giới hạn đặt ra cho cả hệ thống, không phải ngưỡng cảnh báo dòng cao do người dùng cấu hình. |
| PR-PERF-03 | Dòng tải ngắn hạn cho phép | Trên 1 A đến 2 A, tối đa 1 s | Giới hạn dòng và thời gian vận hành ngắn hạn chung của thiết bị. Các ngưỡng và thời gian cấu hình phải phối hợp để không cho phép vận hành vượt giới hạn này. |
| PR-PERF-04 | Sai số đo điện áp đầu vào | Không quá ±0,05 V | Sai số của toàn bộ kênh đo `V_in` trong phạm vi 5–15 V. |
| PR-PERF-05 | Sai số đo điện áp tại tải | Không quá ±0,05 V | Sai số của toàn bộ kênh đo `V_load` trong phạm vi 0–15 V, phục vụ hiển thị và xác định sụt áp trên thiết bị. |
| PR-PERF-06 | Sai số đo dòng tải | Không quá ±0,05 A | Sai số của toàn bộ kênh đo `I_load` trong phạm vi 0–2 A. |
| PR-PERF-07 | Sai số xác định sụt áp | Không quá ±0,1 V | Sai số của `V_drop` = `V_in` − `V_load`, có xét sai số của cả hai kênh điện áp. Chỉ áp dụng khi đầu ra bật và các phép đo nằm trong phạm vi quy định. |
| PR-PERF-08 | Chu kỳ cập nhật hiển thị | Không quá 200 ms | Khoảng thời gian giữa hai lần cập nhật giá trị đo và trạng thái trên giao diện. |
| PR-PERF-09 | Ngưỡng tác động bảo vệ quá dòng nhanh | Chọn 2,5 A để kiểm thử ngắt phần cứng | Mức dòng làm bảo vệ phần cứng tác động độc lập với phần mềm, là thông số chung của thiết bị. |
| PR-PERF-10 | Thời gian ngắt quá dòng bằng phần cứng | Không quá 100 µs | Tính từ khi dòng tải vượt ngưỡng tác động bảo vệ quá dòng nhanh đến khi đường cấp điện bị ngắt. |
| PR-PERF-11 | Thời gian ngắt bảo vệ bằng phần mềm | Không quá 10 ms | Tính từ khi `V_in`, `I_load` hoặc `V_drop` đạt điều kiện ngắt (cả theo mức lẫn thời gian) đến khi đường cấp điện thật sự bị ngắt. |
| PR-PERF-12 | Sụt áp ở dòng tải liên tục | Không quá 0,2 V tại 1 A | Điện áp bị mất trên đường công suất khi đầu ra bật, đo giữa hai đầu nối của thiết bị, không gồm dây dẫn bên ngoài. |
| PR-PERF-13 | Sụt áp ở dòng tải ngắn hạn | Không quá 0,4 V tại 2 A | Điện áp bị mất trên đường công suất trong thời gian dòng ngắn hạn, đo giữa hai đầu nối của thiết bị, không gồm dây dẫn bên ngoài. |

### 6.2. Cơ sở lựa chọn các chỉ tiêu

**a. Phạm vi nguồn, tải**

Mức 5–15 V là khoảng điện áp nhóm đặt ra để thiết kế. Còn các giá trị, thông số liên quan đến điện áp đến từ các loại tải dự định sẽ được kiểm thử sau:

- Tải ổn định có dòng liên tục đến 1 A.
- Tải có dòng khởi động cao có dòng vận hành đến 1 A. Dòng khởi động có thể lên đến 2 A, với thời gian trên 1 A không quá 1 s.
- Tải có dòng biến thiên khi hoạt động có dòng thay đổi trong miền đến 1 A. Các đợt vượt 1 A phải nằm trong giới hạn 2 A, tối đa 1 s và đáp ứng điều kiện lặp lại.

**b. Mức sụt áp chấp nhận được**

Thiết bị được đặt nối tiếp trên đường công suất nối giữa nguồn và tải nên gây một lượng sụt áp nhất định. Lấy mức nguồn thấp nhất mà hệ thống chấp nhận là 5 V, mục tiêu là sụt áp không quá 4% cho ngưỡng dòng tải liên tục thiết bị cho phép, tương đương sụt không quá 0,2 V tại 1 A. Và sụt không quá 8% cho ngưỡng dòng ngắn hạn cho phép, tương đương sụt không quá 0,4 V tại 2 A.

## 7. Chi phí sản xuất

Mã nhóm: PR-COST-XX (Product Requirement – Cost)

Các mức chi phí dưới đây là mục tiêu dự kiến cho một bo mạch và một mẫu thử hoàn chỉnh.

| Mã | Hạng mục | Chi phí dự kiến |
| --- | --- | --- |
| PR-COST-01 | Linh kiện cho một bo mạch theo danh sách BOM | Không quá 600.000 đồng |
| PR-COST-02 | Chế tạo một mẫu thử hoàn chỉnh | Không quá 1.000.000 đồng |

Chi phí mẫu thử bao gồm linh kiện, chế tạo PCB, lắp ráp và vỏ nếu có. Chi phí này không bao gồm nguồn DC bên ngoài, tải kiểm thử, mạch nạp, thiết bị đo, chi phí nghiên cứu và linh kiện mua dư.

## 8. Nguồn nuôi hệ thống

Mã nhóm: PR-PWR-XX (Product Requirement – Power)

| Mã | Hạng mục | Yêu cầu |
| --- | --- | --- |
| PR-PWR-01 | Nguồn cấp cho thiết bị | Lấy điện từ nguồn DC đầu vào để nuôi khối điều khiển, đo lường và giao diện. Nhánh nguồn nội bộ được bảo vệ và điều chỉnh điện áp, lấy trước công tắc công suất của tải để việc ngắt tải không đồng thời cắt nguồn điều khiển. Khả năng tiếp tục hoạt động phụ thuộc nguồn nuôi nội bộ còn hợp lệ. |
| PR-PWR-02 | Trạng thái khi mất nguồn điều khiển | Đường cấp điện cho tải phải mặc định tắt khi nguồn nuôi khối điều khiển bị mất hoặc không ổn định. |
| PR-PWR-03 | Nguồn phụ | Không cần nguồn phụ trong vận hành bình thường. |
| PR-PWR-04 | Công suất tự tiêu thụ | Mục tiêu dự kiến không quá 1 W trong miền điện áp đầu vào 5–15 V khi không nối tải và thiết bị đã hoạt động ổn định. Áp dụng ở cả trạng thái bật và tắt đầu ra. |

Công suất tự tiêu thụ được đo tại đầu nối nguồn DC khi các khối điều khiển, đo lường và giao diện hoạt động bình thường. Phép đo không nối tải loại trừ công suất cấp cho tải và tổn hao do dòng tải.

## 9. Kích thước và khối lượng

Mã nhóm: PR-PHY-XX (Product Requirement – Physical Size)

Các giới hạn dưới đây là mục tiêu thiết kế dự kiến cho mẫu thử đầu tiên.

| Mã | Thông số | Giới hạn dự kiến |
| --- | --- | --- |
| PR-PHY-01 | Kích thước PCB | Không quá 100 × 100 mm |
| PR-PHY-02 | Chiều cao linh kiện | Không quá 30 mm |
| PR-PHY-03 | Khối lượng PCB đã lắp ráp | Không quá 150 g |
| PR-PHY-04 | Vỏ thiết bị | Không bắt buộc đối với mẫu thử đầu tiên |

Kích thước và khối lượng không bao gồm nguồn DC bên ngoài, tải, dây kết nối rời hoặc mạch nạp. Bố trí linh kiện cần thuận tiện cho lắp ráp, đo kiểm và tản nhiệt.

## 10. Lắp đặt

Mã nhóm: PR-INST-XX (Product Requirement – Installation)

| Mã | Yêu cầu |
| --- | --- |
| PR-INST-01 | Sử dụng thiết bị trên bàn thí nghiệm, trong nhà, tại nơi khô ráo và thông thoáng. |
| PR-INST-02 | Cố định PCB bằng lỗ bắt vít và chân đỡ cách điện hoặc đặt trong vỏ phù hợp. Không đặt PCB trực tiếp lên bề mặt dẫn điện. |
| PR-INST-03 | Đầu nối nguồn vào và tải ra phải dễ tiếp cận, có nhãn phân biệt và ký hiệu cực tính. |
| PR-INST-04 | Chỉ kết nối với nguồn DC trong miền điện áp cho phép; không nối trực tiếp với điện lưới AC. |
| PR-INST-05 | Việc đấu nối chỉ cần dụng cụ cơ bản phù hợp với loại đầu nối được chọn. |

Loại đầu nối, số lượng và vị trí lỗ bắt vít sẽ được xác định trong thiết kế phần cứng.

## 11. Phương pháp kiểm chứng dự kiến

Các yêu cầu được kiểm tra bằng quan sát, đo lường và thử nghiệm trên mẫu thử. Bảng dưới đây nêu phương pháp dự kiến; điều kiện thử và tiêu chí đạt cụ thể sẽ được lập trong tài liệu kiểm thử.

| Nhóm yêu cầu | Phương pháp kiểm tra | Cơ sở đánh giá |
| --- | --- | --- |
| Đầu vào, đầu ra và chức năng | Thực hiện các tình huống sử dụng ở mục 4; kiểm tra thao tác bật/tắt, chọn cấu hình, cảnh báo, ngắt và phục hồi. | Đối chiếu kết quả với mục 3 và FR-01 đến FR-11. |
| Đo điện áp, dòng tải và sụt áp | So sánh giá trị đo và `V_drop` tính từ hai kênh điện áp với thiết bị đo tham chiếu tại nhiều điểm trong miền đo. | Sai số theo PR-PERF-04 đến PR-PERF-07. |
| Dòng liên tục, dòng ngắn hạn và sụt áp | Dùng tải có kiểm soát. Đo dòng tải, thời gian dòng ngắn hạn và sụt áp giữa hai đầu nối của thiết bị. | PR-PERF-01 đến PR-PERF-03, PR-PERF-12 và PR-PERF-13. |
| Ngưỡng và thời gian ngắt bảo vệ | Đo ngưỡng tác động và thời gian ngắt bằng phần cứng. Với phần mềm, thử riêng khi đại lượng đạt ngưỡng ngắt và khi hết thời gian cho phép mà cảnh báo vẫn còn. Đo từ mốc tương ứng đến khi đường cấp điện bị ngắt. | PR-PERF-09 đến PR-PERF-11. |
| Cập nhật giao diện | Đo khoảng thời gian giữa các lần cập nhật giá trị và trạng thái trên giao diện. | PR-PERF-08. |
| Chi phí | Tổng hợp BOM, hóa đơn PCB, linh kiện và các chi phí chế tạo liên quan. | PR-COST-01 và PR-COST-02. |
| Nguồn nuôi và trạng thái mất nguồn | Thử đóng/ngắt nguồn, sụt nguồn điều khiển, reset bộ điều khiển và ngắt đầu ra tải. Quan sát đường công suất và khối điều khiển. Đối với PR-PWR-04, đo công suất tại đầu nối nguồn khi không nối tải, ở cả hai trạng thái đầu ra bật và tắt sau khi thiết bị ổn định. Kiểm tra mức lớn nhất ghi nhận trong miền 5–15 V. | PR-PWR-01 đến PR-PWR-04. |
| Kích thước, khối lượng và lắp đặt | Đo kích thước, cân PCB đã lắp ráp; quan sát đầu nối, nhãn cực tính và cách cố định bo mạch. | PR-PHY-01 đến PR-PHY-04 và PR-INST-01 đến PR-INST-05. |
