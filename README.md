# Hệ thống giám sát và bảo vệ đường nguồn DC

> Trạng thái: đang ở Giai đoạn 3 — Đặc tả thành phần. Đã có đủ bộ tài liệu spec sơ bộ, gồm Product Requirements, Design Spec, Hardware Spec v0.2, Software Spec v0.1 và Test Spec v0.1. Các thông số và điều kiện còn mở được theo dõi trong từng tài liệu để hoàn thiện khi thiết kế và chuẩn bị kiểm chứng.

Dự án nghiên cứu một thiết bị được mắc giữa nguồn DC bên ngoài và tải DC bên ngoài. Thiết bị giám sát trạng thái điện của đường công suất và có khả năng ngắt tải khi phát hiện điều kiện không an toàn.

Nhánh này được chủ ý giữ tối giản. Kiến trúc chức năng từ baseline v0.1 được giữ nguyên; cấu hình, quy tắc cảnh báo/ngắt và các mục tiêu hiệu năng được cập nhật theo Product Requirements v2. Miền điện áp 5–15 V đã được chọn; các chỉ tiêu hiệu năng còn lại vẫn là mục tiêu thiết kế, chưa phải kết quả đo. Phần cứng đang được khảo sát phương án; model linh kiện, schematic, PCB và firmware chưa được chốt. Tài liệu hiện tại là đầu vào cho đặc tả thành phần và thiết kế chi tiết, chưa chứng minh sản phẩm sẵn sàng chế tạo.

Tài liệu đang hoạt động:

- [`docs/concept.md`](docs/concept.md): phạm vi và cách hiểu hiện tại của dự án.
- [`docs/product-requirements.md`](docs/product-requirements.md): yêu cầu sản phẩm phiên bản 2, các tham số cấu hình, mục tiêu hiệu năng và phương pháp kiểm chứng dự kiến.
- [`docs/design-specification.md`](docs/design-specification.md): kiến trúc, trách nhiệm các khối, interface và hành vi giám sát/bảo vệ–phục hồi, được đồng bộ theo yêu cầu v2.
- [`docs/hardware-specification.md`](docs/hardware-specification.md): đặc tả phần cứng sơ bộ, các ngân sách đã chốt, hướng thiết kế và những mục cần hoàn thiện.
- [`docs/software-specification.md`](docs/software-specification.md): đặc tả firmware sơ bộ, tổ chức chức năng, đo lường, cấu hình, bảo vệ, trạng thái, UI, timing và kiểm chứng dự kiến.
- [`docs/test-specification.md`](docs/test-specification.md): đặc tả kiểm thử sơ bộ, bảng đối chiếu yêu cầu, phép thử từng khối/firmware/tích hợp, tiêu chí đạt và bản ghi bằng chứng.
- [`docs/diagrams/system-architecture.drawio`](docs/diagrams/system-architecture.drawio): bản vẽ gốc gồm bảy page (context, block diagram, bốn subsystem và state diagram); SVG tương ứng nằm trong `docs/diagrams/`.
- [`docs/development-process.md`](docs/development-process.md): quy trình phát triển và điều kiện chuyển giai đoạn theo môn học.

Phiên bản tài liệu được sinh trước đây vẫn được bảo tồn trên nhánh `main` và tag `archive/generated-v1`; chỉ dùng làm nguồn tham khảo, không phải thiết kế đã được chấp thuận.
