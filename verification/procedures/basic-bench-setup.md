# Thiết lập bàn thử cơ bản

## Mục đích

Xác định thiết lập tối thiểu có khả năng lặp lại cho phép đo tĩnh và kiểm thử tải danh định. Đây không phải thiết lập thử ngắn mạch cứng.

## Kết nối

```text
nguồn DC điều chỉnh được/có giới hạn dòng
              |
              v
            DC IN của trạm
            DC OUT của trạm
              |
              v
       điện trở/bộ tải đủ định mức
```

Kết nối DMM tham chiếu tại đúng các đầu cực liên quan đến phép thử. Không suy ra điện áp đầu cực từ nhãn adapter hoặc giá trị đặt khi không tải.

## Ví dụ lựa chọn tải

Với tải chủ yếu là điện trở:

`R = V / I` và `P = V x I`.

| Mục tiêu tại 12 V | Tải xấp xỉ | Công suất tiêu tán |
| ---: | ---: | ---: |
| 0.25 A | 48 ohm | 3 W |
| 0.50 A | 24 ohm | 6 W |
| 0.90 A | 13.3 ohm | 10.8 W |
| 1.00 A | 12 ohm | 12 W |
| 1.20 A | 10 ohm | 14.4 W |

Chọn định mức công suất điện trở thực tế cao hơn đáng kể so với công suất tiêu tán đã tính, lắp đặt an toàn và dự kiến điện trở sẽ nóng lên. Phải đo dòng thực tế vì dung sai và sự phát nhiệt của điện trở làm thay đổi giá trị.

## Hạn chế của bộ đổi nguồn 12 V cố định

Adapter cố định có thể hỗ trợ trình diễn danh định nhưng không thể tạo lặp lại các ngưỡng UVP 10,5 V và OVP 14,2 V. Không được tùy tiện tạo quá áp bằng nguồn không ổn áp hoặc không rõ đặc tính. Hãy sắp xếp nguồn ổn áp điều chỉnh được cho các trường hợp này.
