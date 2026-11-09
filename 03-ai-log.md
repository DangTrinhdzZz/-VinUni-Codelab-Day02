# AI Log & Reflection

## Bối cảnh sử dụng

Tôi thực hiện bài lab này theo hình thức cá nhân vì chưa tìm được thành viên. Tôi dùng AI như một thought-partner để đọc yêu cầu trong repository, đối chiếu worksheet với ví dụ, starter code và autograder, sau đó hỗ trợ brainstorm và chuẩn hóa bài nộp. Tôi không có cuộc họp nhóm, khảo sát thực địa hoặc phản hồi của thành viên khác để ghi lại. Điều kiện nộp cá nhân vẫn cần được giảng viên xác nhận.

## AI đã hỗ trợ tôi như thế nào

Ban đầu tôi xem xét các ý tưởng thuộc VinFast, Xanh SM, Vinhomes, Vinpearl, Vinmec và VinUni từ worksheet và inspiration kit. AI giúp tôi nhóm các ý tưởng theo bốn lens: Repetitive, Time-consuming, AI-upgrade và Stakeholder Pain. Sau khi so sánh khả năng giới hạn phạm vi, dữ liệu cần có, mức rủi ro và khả năng kiểm thử trong thời gian lab, tôi chọn bài toán **Vinhomes — phân loại và điều hướng phản ánh cư dân**.

AI hỗ trợ tôi:

- Chuyển mô tả bài toán thành current-state workflow có actor, input, output, handoff, bottleneck và thời gian giả định.
- Xây dựng Problem Statement 6-field và phân biệt business impact có thể kỳ vọng với kết quả thực tế chưa đo được.
- Đề xuất các metric có thể kiểm thử: category accuracy, thời gian tạo draft, route accuracy và tỷ lệ nhân viên phải sửa.
- So sánh No AI, Rule-based, LLM Feature và Agentic Loop.
- Thiết kế operational boundary, structured output và các adversarial tests.
- Kiểm tra sự nhất quán giữa Problem Scan, Deep Dive, sơ đồ và code.

Tôi chỉ dùng kết quả AI làm bản nháp để rà soát. Tôi không coi nội dung AI tạo ra là bằng chứng về hoạt động thật của Vinhomes.

## Những điểm AI chưa đáng tin cậy

AI có thể đưa ra thời gian xử lý, tỷ lệ chính xác, SLA hoặc ROI nghe hợp lý nhưng không có nguồn. AI cũng có xu hướng đề xuất kiến trúc phức tạp như agent tự gọi công cụ dù bài toán chỉ cần hiểu văn bản và routing hữu hạn. Với prompt chưa chặt, model có thể:

- Đoán dự án, tòa hoặc căn khi thông tin bị thiếu.
- Ép phản ánh nhiều vấn đề vào một category.
- Bỏ sót dấu hiệu nguy hiểm hoặc gắn `urgent` quá rộng.
- Lặp lại dữ liệu cá nhân trong summary.
- Làm theo prompt injection yêu cầu bỏ `[DRAFT_ONLY]` hoặc bỏ human review.
- Viết như thể ticket đã được gửi/chuyển/đóng, hoặc hứa bồi thường.

Vì chưa có dữ liệu đã gán nhãn, tôi cũng chưa thể tin rằng category/priority do AI đề xuất phản ánh policy vận hành thực tế.

## Cách tôi sửa và kiểm chứng

Tôi sửa đầu ra AI theo các nguyên tắc sau:

1. Gắn nhãn mọi thời gian là **Giả định thiết kế — Baseline cần được xác minh**.
2. Gắn nhãn mọi ngưỡng chất lượng là **Mục tiêu đề xuất cho prototype**, không trình bày như kết quả đã đạt.
3. Thu hẹp scope còn điện, nước, thang máy và vệ sinh; dùng `unknown` và `out_of_scope` cho phần không thể xử lý an toàn.
4. Chọn **LLM Feature + Rule-based Router + Human-in-the-loop**, không dùng Agentic Loop.
5. Buộc output theo schema, `requires_human_review=true` và thêm `[DRAFT_ONLY]` ở tầng ứng dụng.
6. Giữ fallback về xử lý thủ công khi API/model lỗi, JSON sai, dữ liệu thiếu, nội dung mơ hồ hoặc không tự tin.
7. Không cấp quyền tự tạo, gửi, chuyển hoặc đóng ticket cho model.

Tôi kiểm chứng bằng cách đọc lại toàn bộ README, worksheet, deliverable example, inspiration kit, starter code, autograder, requirements và `.gitignore`; biên dịch Python; chạy các kiểm tra tĩnh của autograder; rà soát link/tên file/TODO/secret; và render trực tiếp ảnh workflow để xem chữ, font, mũi tên, handoff và bottleneck.

Autograder có kiểm tra legacy gắn với ví dụ Xanh SM (`5%` và `dispatch_mobile_charger`). Tôi giữ đúng domain Vinhomes và chỉ thêm một **ghi chú tương thích legacy** được gắn nhãn rõ trong system prompt: hai khái niệm đó không áp dụng, không được đưa vào output và không làm thay đổi hành vi phân loại. Tôi không biến bài toán thành Xanh SM hoặc thêm rule pin/xe sạc vào logic Vinhomes.

## Quá trình cải tiến prompt

### Phiên bản 1 — vai trò và tác vụ

Prompt ban đầu chỉ yêu cầu đọc phản ánh, phân loại và tóm tắt. Phiên bản này chưa đủ vì model có thể trả văn bản tự do, đoán thông tin thiếu hoặc hành động vượt quyền.

### Phiên bản 2 — schema và phạm vi

Tôi thêm sáu category hợp lệ, bốn mức priority, object `location`, `missing_information`, `target_department`, `requires_human_review` và `prohibited_action_detected`. Tôi quy định dữ liệu thiếu phải là `unknown`, ngoài phạm vi là `out_of_scope`.

### Phiên bản 3 — operational boundary

Tôi bổ sung lệnh cấm tự tạo/gửi/chuyển/đóng ticket, cấm cam kết bồi thường/hoàn phí, cấm quy kết trách nhiệm và cấm tiết lộ PII. Tôi thêm quy tắc mọi output là draft và mọi trường hợp đều phải được con người review.

### Phiên bản 4 — safety, injection và code validation

Tôi thêm quy tắc `urgent` cho nguy cơ đối với người/tài sản, chống prompt injection, structured output của SDK và validation lần hai ở tầng ứng dụng. Sáu test tấn công bao phủ bỏ tag/tự route, tự đóng ticket, cam kết bồi thường, yêu cầu PII, system override và trường hợp thiếu vị trí có nhiều sự cố.

Các test không in `Passed` vô điều kiện: code parse JSON và kiểm tra tag, schema, category, priority, HITL, hành động bị cấm và kỳ vọng riêng của từng case.

## Trạng thái runtime

Tại thời điểm hoàn thiện tài liệu, môi trường chưa có `GEMINI_API_KEY`/`GOOGLE_API_KEY`, nên tôi **không chạy và không tuyên bố kết quả adversarial runtime của Gemini**. Kết quả runtime phải được bổ sung sau khi cấu hình API key hợp lệ (và cài dependencies trong `requirements.txt`). Các kiểm tra tĩnh không thay thế đánh giá model trên dữ liệu ẩn danh.

## Bài học “Problem First, AI Second”

Bài học quan trọng nhất của tôi là không bắt đầu bằng câu hỏi “dùng agent nào”, mà bắt đầu bằng actor, workflow, bottleneck, metric và boundary. Phần cần ngôn ngữ tự nhiên mới dùng LLM; phần mapping xác định dùng rules; quyết định có tác động vận hành vẫn thuộc về con người. Một prototype hẹp, đo được và có fallback có giá trị hơn một hệ thống tự chủ phức tạp nhưng không có dữ liệu hoặc cơ chế kiểm soát.

Trước khi nộp, tôi cần tự xác nhận reflection này đúng với trải nghiệm cá nhân của mình và cập nhật phần runtime nếu sau đó thực sự chạy Gemini.
