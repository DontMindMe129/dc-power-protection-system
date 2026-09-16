# Đặc tả kiểm thử

## 1. Mục đích

Đặc tả này xác định cách kiểm chứng các yêu cầu đề xuất trước khi thiết kế chi tiết được chấp nhận. Phạm vi bao gồm kiểm tra trực quan, phân tích, mô phỏng và thử nghiệm vật lý.

## 2. Các phương pháp kiểm chứng

| Phương pháp | Mục đích sử dụng |
| --- | --- |
| Kiểm tra trực quan | Kiểm tra sơ đồ nguyên lý, mã nguồn, định mức, cấu hình hoặc hành vi quan sát được |
| Phân tích | Tính toán dung sai, công suất, định thời hoặc biên nhiệt |
| Mô phỏng | Khảo sát hành vi đo lường/đóng cắt trước khi có phần cứng |
| Thử nghiệm | Áp dụng kích thích có kiểm soát và so sánh hành vi quan sát được với tiêu chí chấp nhận |

Mô phỏng giúp tăng độ tin cậy vào thiết kế nhưng không thay thế kiểm thử phần cứng tích hợp đối với độ chính xác đo, độ trễ đóng cắt hoặc hành vi nhiệt.

## 3. Các cấp kiểm thử

1. **T0 - Rà soát tài liệu:** chất lượng yêu cầu, truy vết và các cổng an toàn.
2. **T1 - Logic trên máy chủ/firmware:** dùng các phép đo đưa vào giả lập để kiểm tra ngưỡng, chống dội, trạng thái và quy tắc reset.
3. **T2 - Thử mạch năng lượng thấp:** hành vi đo lường và điều khiển công tắc với giới hạn dòng.
4. **T3 - Thử tích hợp danh định:** vận hành 12 V đến 1 A.
5. **T4 - Thử lỗi có kiểm soát:** UVP/OVP/OCP bằng thiết bị điều chỉnh được/có giới hạn dòng.
6. **T5 - Kiểm chứng định thời và nhiệt cuối cùng:** oscilloscope/logic analyzer và phép đo nhiệt độ.

## 4. Các cấp thiết bị

| Cấp | Thiết bị | Các tuyên bố có thể hỗ trợ |
| --- | --- | --- |
| Cơ bản | DMM, adapter cố định, điện trở/tải đã biết, terminal UART | Kiểm thử trực quan/giao diện người dùng, kiểm tra độ chính xác tĩnh hạn chế |
| Trung cấp | Nguồn ổn áp điều chỉnh được có giới hạn dòng, bộ tải, DMM | Quét ngưỡng, kiểm tra OCP tĩnh và đường công suất |
| Đầy đủ | Cấp trung cấp cộng với oscilloscope/logic analyzer và thiết bị đo nhiệt độ | Định thời trip, loại bỏ quá độ và bằng chứng nhiệt |

Dự án hiện chỉ có khả năng tiếp cận thiết bị cấp Cơ bản. Khả năng tiếp cận cấp Trung cấp là điều kiện tiên quyết trước khi kiểm thử lỗi tích hợp; cần tiếp cận cấp Đầy đủ ít nhất một lần trước khi chấp nhận cuối cùng.

## 5. Quy tắc chấp nhận chung

- Các giá trị thử phải được đo tại các đầu cực của trạm, không được suy ra từ nhãn adapter.
- Khi phù hợp, phép thử ngưỡng phải tiếp cận ngưỡng từ cả hai phía.
- Một yêu cầu tạm thời có thể đạt tạm thời, nhưng không thể trở thành giá trị cuối cùng cho đến khi được rà soát theo dung sai linh kiện và độ không đảm bảo của phép thử.
- Mọi hiện tượng reset bất ngờ, đóng cắt mất kiểm soát hoặc linh kiện quá nhiệt đều là điều kiện dừng ngay lập tức.
- Phép thử không đạt phải được ghi lại; kết quả không được xóa sau khi sửa. Lần chạy lại phải dẫn chiếu lần không đạt trước đó.
- Phép thử được đánh dấu `BỊ CHẶN` không tương đương với `ĐẠT`.

## 6. Siêu dữ liệu kết quả bắt buộc

Mọi bản ghi kết quả phải bao gồm:

- mã ca kiểm thử và mã yêu cầu;
- ngày và người thực hiện;
- phiên bản phần cứng và commit firmware;
- sơ đồ kết nối thử hoặc ảnh chụp rõ ràng;
- mã nhận dạng/dải đo của thiết bị;
- điều kiện ban đầu và kích thích đã áp dụng;
- kết quả mong đợi và thực tế;
- dữ liệu đo thô khi phù hợp;
- trạng thái đạt/không đạt/bị chặn;
- ghi chú bất thường và hành động tiếp theo.

## 7. Các cổng an toàn

- Không thực hiện phép thử T3-T5 nếu chưa có sơ đồ nguyên lý đường công suất đã được rà soát.
- Không thử trên 0,2 A qua các thanh nguồn của breadboard không hàn trừ khi đường dẫn và tiếp điểm đã được đánh giá rõ ràng.
- Không nối tắt trực tiếp đầu ra từ adapter cố định không được kiểm soát.
- Phép thử OVP dừng ở 15 V cho đến khi xác định điện áp cực đại tuyệt đối khi không hoạt động.
- Phép thử ngắn mạch cứng yêu cầu giới hạn dòng độc lập, bảo vệ nhanh bằng phần cứng và phương tiện ngắt nguồn khẩn cấp.
