# Problem Scan — AI Product Scoping

## 1. Phạm vi và nguyên tắc sử dụng số liệu

Bài scan này được thực hiện cá nhân. Tôi chưa có dữ liệu vận hành, khảo sát thực địa hay phỏng vấn người dùng của Vinhomes/Vingroup. Vì vậy, mọi thời gian và ngưỡng định lượng dưới đây đều được gắn một trong ba nhãn: **Giả định thiết kế**, **Baseline cần được xác minh**, hoặc **Mục tiêu đề xuất cho prototype**. Các con số không được hiểu là kết quả vận hành thực tế.

## 2. Danh sách cơ hội

| # | Công ty thành viên | Lens | Mô tả bài toán |
|---:|---|---|---|
| 1 | Vinhomes | Repetitive | Phân loại nội dung phản ánh của cư dân, xác định mức ưu tiên và điều hướng bản nháp đến bộ phận điện, nước, thang máy hoặc vệ sinh. |
| 2 | Vinhomes | Time-consuming | Kiểm tra hồ sơ đăng ký thi công nội thất có đủ biểu mẫu, thông tin căn hộ, thời gian thi công và tài liệu bắt buộc hay chưa. |
| 3 | Vinpearl | AI-upgrade | Trích xuất yêu cầu từ email đặt phòng theo đoàn như ngày ở, số phòng, loại phòng, suất ăn và yêu cầu đặc biệt để tạo bản nháp yêu cầu đặt chỗ. |
| 4 | Xanh SM | Stakeholder Pain | Phân loại mô tả sự cố do tài xế gửi để nhân viên điều vận nhận biết nhóm sự cố và mức khẩn cấp trước khi xử lý. |
| 5 | VinFast | AI-upgrade | Phân loại sơ bộ mô tả lỗi xe bằng ngôn ngữ tự nhiên của khách hàng để kỹ thuật viên có thêm thông tin khi tiếp nhận; không dùng để chẩn đoán hoặc kết luận an toàn. |

Danh sách sử dụng đủ bốn lens của worksheet. Lens chỉ mô tả góc nhìn tìm kiếm cơ hội, không phải bằng chứng rằng pain point đã được xác nhận ngoài thực địa.

## 3. Ba Quick Problem Cards

### Card 1 — Vinhomes: Phân loại và điều hướng phản ánh cư dân

| Trường | Nội dung |
|---|---|
| **Bài toán một câu** | Hỗ trợ nhân viên CSKH đọc phản ánh tự do, trích xuất vị trí, đề xuất nhóm vấn đề và mức ưu tiên, rồi tạo ticket nháp để nhân viên kiểm tra trước khi chuyển. |
| **Công ty thành viên** | Vinhomes |
| **Actor** | Nhân viên CSKH/Ban quản lý tiếp nhận phản ánh; cư dân là bên chịu ảnh hưởng bởi thời gian xử lý. |
| **Quy trình thủ công hiện tại** | 1. Nhận phản ánh → 2. Đọc nội dung → 3. Xác định dự án/tòa/vị trí → 4. Phân loại và đánh giá ưu tiên → 5. Chuyển bộ phận chuyên môn. |
| **Bottleneck** | Nội dung tự do có thể thiếu vị trí, dùng từ không chuẩn hoặc mô tả nhiều vấn đề; nhân viên phải đọc và diễn giải. **Giả định thiết kế:** bước xác định vị trí và phân loại mất 8 phút/lượt. **Baseline cần được xác minh.** |
| **Thời gian xử lý** | **Giả định thiết kế:** 22 phút/lượt cho khâu tiếp nhận ban đầu; chưa bao gồm thời gian khắc phục. **Baseline cần được xác minh.** |
| **Bước AI tham gia** | LLM trích xuất thông tin, tóm tắt, đề xuất `category` và `priority`; rule-based router ánh xạ category sang bộ phận. AI chỉ tạo draft. |
| **Success metric** | **Mục tiêu đề xuất cho prototype:** phân loại đúng ≥ 85%; thời gian tạo draft ≤ 2 phút và tổng xử lý ban đầu ≤ 5 phút; route đúng ≥ 90%; tỷ lệ nhân viên phải sửa category/priority ≤ 20%. Các ngưỡng cần kiểm thử trên bộ dữ liệu ẩn danh. |
| **Quick Architecture** | **LLM Feature + Rule-based Router + Human-in-the-loop.** |

### Card 2 — Vinhomes: Kiểm tra tính đầy đủ của hồ sơ thi công nội thất

| Trường | Nội dung |
|---|---|
| **Bài toán một câu** | Hỗ trợ nhân viên kiểm tra một bộ hồ sơ đăng ký thi công nội thất đã có đủ trường và tài liệu bắt buộc để chuyển sang bước thẩm định hay chưa. |
| **Công ty thành viên** | Vinhomes |
| **Actor** | Nhân viên Ban quản lý kiểm tra hồ sơ; cư dân/nhà thầu chờ phản hồi bổ sung. |
| **Quy trình thủ công hiện tại** | 1. Nhận hồ sơ → 2. Mở từng biểu mẫu/tệp → 3. Đối chiếu checklist → 4. Ghi hạng mục thiếu → 5. Soạn yêu cầu bổ sung. |
| **Bottleneck** | Đối chiếu nhiều tệp có cách trình bày khác nhau. **Giả định thiết kế:** bước 2–4 mất 18 phút/hồ sơ. **Baseline cần được xác minh.** |
| **Thời gian xử lý** | **Giả định thiết kế:** 25 phút/hồ sơ. **Baseline cần được xác minh.** |
| **Bước AI tham gia** | OCR/LLM trích xuất trường từ tài liệu; rules đối chiếu checklist phiên bản được Ban quản lý phê duyệt; nhân viên xác nhận kết quả. |
| **Success metric** | **Mục tiêu đề xuất cho prototype:** phát hiện đúng ≥ 90% trường/tài liệu bị thiếu; giảm thời gian tạo checklist nháp còn ≤ 6 phút; 100% hồ sơ vẫn được nhân viên duyệt. |
| **Quick Architecture** | **Rule + LLM Feature + Human-in-the-loop.** |

### Card 3 — Vinpearl: Trích xuất yêu cầu từ email đặt phòng theo đoàn

| Trường | Nội dung |
|---|---|
| **Bài toán một câu** | Hỗ trợ nhân viên kinh doanh trích xuất chi tiết từ email đặt phòng theo đoàn và tạo bản nháp yêu cầu để kiểm tra quỹ phòng. |
| **Công ty thành viên** | Vinpearl |
| **Actor** | Nhân viên kinh doanh/đặt phòng; công ty lữ hành gửi yêu cầu. |
| **Quy trình thủ công hiện tại** | 1. Đọc email và tệp đính kèm → 2. Ghi ngày ở/số lượng/loại phòng → 3. Ghi dịch vụ và yêu cầu đặc biệt → 4. Nhập yêu cầu nháp → 5. Kiểm tra lại với người gửi. |
| **Bottleneck** | Email có cấu trúc không đồng nhất và có thể thay đổi nhiều lần. **Giả định thiết kế:** bước đọc và nhập lại mất 20 phút/yêu cầu. **Baseline cần được xác minh.** |
| **Thời gian xử lý** | **Giả định thiết kế:** 30 phút/yêu cầu trước khi kiểm tra quỹ phòng. **Baseline cần được xác minh.** |
| **Bước AI tham gia** | LLM trích xuất thành structured data và đánh dấu trường mơ hồ/thiếu; nhân viên xác nhận trước khi nhập hệ thống. |
| **Success metric** | **Mục tiêu đề xuất cho prototype:** độ chính xác theo trường ≥ 90%; tạo draft ≤ 5 phút; 100% trường thiếu được hiển thị để con người review; không tự xác nhận đặt phòng. |
| **Quick Architecture** | **LLM Feature + Human-in-the-loop.** |

## 4. Quyết định lựa chọn

### Bài toán được chọn

Tôi chọn **Vinhomes — Phân loại và điều hướng phản ánh cư dân** vì đầu vào ngôn ngữ tự nhiên phù hợp với khả năng trích xuất và phân loại của LLM, đầu ra có thể giới hạn bằng schema rõ ràng, và phạm vi prototype có thể thu hẹp thành bốn category: điện, nước, thang máy và vệ sinh. Giá trị cần kiểm chứng là giảm thao tác đọc–nhập lặp lại, trong khi nhân viên vẫn giữ quyền quyết định.

### Vì sao chưa chọn hai card còn lại

- **Hồ sơ thi công nội thất:** cần có checklist chính thức, phiên bản biểu mẫu, chính sách theo từng dự án và tập tài liệu mẫu đủ đại diện. Nếu chưa có các đầu vào đó, prototype dễ đánh đồng “không trích xuất được” với “hồ sơ bị thiếu”.
- **Email đặt phòng theo đoàn:** cần tích hợp quy tắc tồn phòng, giá, điều kiện hợp đồng và luồng thay đổi booking. Phần trích xuất có thể làm prototype, nhưng kiểm chứng end-to-end phụ thuộc nhiều hệ thống và dữ liệu hơn phạm vi lab.

### Lựa chọn kiến trúc

**LLM Feature + Rule-based Router** phù hợp hơn Agentic Loop:

1. LLM xử lý phần không cấu trúc: hiểu tiếng Việt, trích xuất vị trí, tóm tắt và đề xuất category/priority.
2. Router dùng mapping xác định, dễ kiểm thử và audit, để đề xuất bộ phận theo category.
3. Human-in-the-loop duyệt hoặc sửa trước khi hệ thống chuyển ticket.
4. Không cần vòng lặp tự chủ, gọi nhiều công cụ hay tự quyết định hành động ngoài hệ thống. Agentic Loop làm tăng bề mặt lỗi và chi phí nhưng không tạo giá trị cần thiết cho scope này.

Quyết định này là quyết định scoping cho prototype, chưa phải phê duyệt triển khai production.
