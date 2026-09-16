# Đánh giá tính khả thi ban đầu

## Kết luận

**Khả thi có điều kiện đối với đồ án môn học.** Phạm vi 12 V / 1 A có độ khó kỹ thuật vừa phải và phù hợp với một dự án cân bằng giữa phần cứng và firmware. Trở ngại hiện tại không phải khối lượng xử lý của MCU, mà là việc tạo kích thích thử an toàn, có khả năng lặp lại và tiếp cận thiết bị đo thời gian/nhiệt trong ít nhất một buổi.

Dự án nên được tiếp tục nếu nhóm chấp nhận kế hoạch tài nguyên kiểm thử tối thiểu bên dưới. Nếu nhóm không thể tiếp cận nguồn điều chỉnh được/có giới hạn dòng hoặc thiết bị đo thời gian, dự án vẫn có thể được trình diễn, nhưng một số tuyên bố về bảo vệ không thể được kiểm chứng một cách trung thực.

## Tính khả thi theo lĩnh vực

| Lĩnh vực | Đánh giá | Lý do |
| --- | --- | --- |
| Mức công suất điện | Khả thi | 12 V tại 1 A tương ứng 12 W cấp cho tải bên ngoài; trạm chỉ nên tiêu tán một phần nhỏ nếu chọn MOSFET, shunt và kích thước đồng phù hợp. |
| Đo điện áp | Khả thi | Tín hiệu đã chia áp và bảo vệ tương thích với ADC của MCU; việc hiệu chuẩn theo DMM là khả thi. |
| Đo dòng điện | Khả thi | Mạch đầu vào shunt/cảm biến dòng cho 0-1,2 A nằm trong phạm vi thiết kế điện áp thấp thông dụng. |
| Firmware | Khả thi | Thu thập dữ liệu, lọc, ngưỡng, máy trạng thái, giao diện người dùng và UART phù hợp với năng lực của MCU lớp STM32F103. |
| Thử UVP/OVP | Có điều kiện | Cần nguồn 9-15 V điều chỉnh được an toàn; adapter cố định 12 V không thể tạo lặp lại cả hai ngưỡng. |
| Thử OCP tĩnh | Có điều kiện | Cần bộ tải điện trở/tải điện tử có khả năng tạo khoảng 0,1-1,2 A. |
| Kiểm chứng thời gian đáp ứng | Bị chặn với dụng cụ hiện có | Đồng hồ vạn năng không thể xác nhận tuyên bố ngắt trong <=100 ms; cần ít nhất một buổi dùng oscilloscope hoặc logic analyzer. |
| Xác nhận ngắn mạch cứng | Rủi ro cao nếu làm tùy tiện | Phải dùng thiết bị có giới hạn dòng sau khi rà soát bảo vệ phần cứng; đây không phải phép thử chức năng giai đoạn đầu. |
| Tích hợp PCB | Khả thi | Dòng 1 A có thể quản lý được, nhưng phải rà soát định mức đầu nối, chiều rộng đồng, nhiệt của shunt và SOA của MOSFET. |

## Khả năng tiếp cận kiểm thử bổ sung tối thiểu

Nhóm không cần sở hữu một phòng thí nghiệm đầy đủ, nhưng nên sắp xếp khả năng tiếp cận:

1. Nguồn DC điều chỉnh được trong dải 9-15 V với giới hạn dòng đã biết, hoặc thiết lập phòng thí nghiệm tương đương có giám sát.
2. Bộ tải điện trở an toàn hoặc tải điện tử đạt ít nhất 1,2 A tại 12 V.
3. DMM tham chiếu.
4. Oscilloscope hoặc logic analyzer, ít nhất cho phép thử định thời trip cuối cùng.
5. Phương tiện quan sát nhiệt độ trong phép thử độ bền 1 A; ngay cả nhiệt kế tiếp xúc đi mượn cũng tốt hơn đánh giá bằng tay.

Nếu dùng điện trở làm tải danh định 1 A, giá trị xấp xỉ là 12 ohm và công suất tiêu tán khoảng 12 W. Cần linh kiện có định mức cao hơn đáng kể so với mức tiêu tán này, chẳng hạn 25 W, và linh kiện sẽ nóng lên. Ở 1,2 A và 12 V, tải 10 ohm tiêu tán khoảng 14,4 W.

## Công việc có thể thực hiện ngay với dụng cụ cơ bản

- Rà soát yêu cầu và kiến trúc.
- Tính toán mạch chia áp/shunt và phân tích dung sai.
- Mô phỏng mạch đo lường và đóng cắt.
- Kiểm thử máy trạng thái firmware bằng các giá trị ADC được đưa vào giả lập.
- Kiểm thử hành vi giao diện người dùng và UART.
- Kiểm tra tĩnh tại một hoặc vài điểm vận hành an toàn bằng DMM và tải phù hợp.

## Những điều chưa thể tuyên bố

- Thời gian ngắt vật lý <=100 ms đã được kiểm chứng.
- Khả năng ngắt ngắn mạch cứng an toàn.
- Độ chính xác đo cuối cùng trên toàn bộ dải.
- Hiệu năng nhiệt ở dòng 1 A liên tục.
- Hoạt động an toàn tại mức đầu vào ngoài dải 9-15 V.

## Rủi ro chính và biện pháp giảm thiểu

| Rủi ro | Hệ quả | Biện pháp giảm thiểu / tiêu chí thoát |
| --- | --- | --- |
| Không có nguồn điều chỉnh được có giới hạn dòng | Phép thử UVP/OVP/OCP mất an toàn hoặc không thể lặp lại | Đặt lịch sử dụng phòng thí nghiệm có giám sát trước khi phê duyệt sơ đồ nguyên lý |
| Điện trở tải thiếu công suất | Nguy cơ bỏng hoặc điểm dòng không hợp lệ | Tính công suất tiêu tán; dùng biên công suất thực tế >=2 lần và bố trí có che chắn |
| Coi firmware là bảo vệ ngắn mạch | MOSFET/shunt hỏng trước khi MCU phản ứng | Dùng cầu chì/giới hạn dòng/đường comparator độc lập; rà soát trước khi thử lỗi |
| Offset/dung sai cảm biến | Trip sai hoặc hiển thị không chính xác | Các điểm hiệu chuẩn, ngân sách dung sai và ngưỡng có thể cấu hình |
| Chọn MOSFET chỉ theo định mức dòng | Quá nóng hoặc hành vi mất an toàn khi có lỗi | Kiểm tra `RDS(on)`, điều khiển gate, SOA, năng lượng lỗi và lớp đồng PCB |
| Thay đổi yêu cầu mà không cập nhật phép thử | Báo cáo cuối không thể kiểm chứng | Duy trì mã ổn định và ma trận truy vết trong mỗi lần rà soát |

## Điểm kiểm tra tiếp tục/dừng

Chuyển sang kiến trúc chi tiết nếu tất cả câu trả lời đều là **có**:

- [ ] Nhóm có thể có được nguồn điều chỉnh được/có giới hạn dòng trước khi kiểm thử tích hợp không?
- [ ] Nhóm có thể có được hoặc chế tạo bộ tải có định mức an toàn đến 1,2 A không?
- [ ] Nhóm có thể mượn oscilloscope hoặc logic analyzer cho một buổi đo thời gian không?
- [ ] Phần cứng có bao gồm cơ chế bảo vệ độc lập với hoạt động bình thường của firmware không?
- [ ] Nhóm có tránh thử ngắn mạch trực tiếp cho đến khi vượt qua cổng an toàn không?

Nếu ba câu đầu vẫn là **không**, hãy giảm phạm vi tuyên bố xuống còn giám sát và trình diễn ngắt tải có kiểm soát, hoặc chuyển sang dự án có kích thích thử đơn giản hơn.
