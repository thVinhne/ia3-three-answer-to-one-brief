## 2026-11-03 — Tìm hiểu yêu cầu bài IA3
Tool Codex.
Asked for Tóm tắt đề, chức năng starter, trình tự thực hiện và các file cần nộp.
Kept Hướng dẫn phân biệt VERDICTS.md, ASSISTANT-REVIEW.md, CSR-SSR.md, AI-LOG.md và báo cáo tự đánh giá.
Changed Chưa có chỉnh sửa được ghi nhận.
Rejected Chưa có; phần này là hướng dẫn quy trình, chưa chạy kiểm thử.

## 2026-11-03 — Lập dự đoán cho ba phiên bản
Tool Codex.
Asked for Cung cấp mẫu VERDICTS.md và giải thích cách đọc code để dự đoán từng mục cho A, B và C.
Kept Mẫu bảng và các dự đoán kèm lý do do AI cung cấp: A lỗi mục 2; B lỗi mục 6 và 8; C dự kiến đạt tất cả, bao gồm empty.
Changed Chưa có điều chỉnh dự đoán dựa trên kết quả chạy.
Rejected Chưa có; các dự đoán hiện chỉ dựa trên đọc code, chưa được xác nhận bằng kiểm thử.

## 2026-11-03 — Kiểm tra file dự đoán đã soạn
Tool Codex.
Asked for Đọc VERDICTS.md của tôi và đối chiếu với ba file JS để tìm nội dung còn thiếu hoặc chưa chính xác.
Kept Ghi nhận các góp ý: B mục 5 còn để PASS/FAIL; cần bọc chuỗi chứa <i> bằng backtick, thống nhất FAIL và sửa dấu chấm thừa.
Changed Chưa xác nhận đã áp dụng các góp ý vào file.
Rejected Chưa có; AI chưa chạy kiểm thử và không xác minh được lịch sử commit vì lệnh Git bị từ chối truy cập.

## 2026-11-03 — Ghi nhận kiểm thử bản gốc A

Tool: Codex.

Asked for: Tôi báo rằng bản A đạt mọi mục trừ mục 2 và cung cấp
đường dẫn ảnh evidence; nhờ hướng dẫn đặt tên ảnh, ghi VERDICTS.md
và AI-LOG.md.

Kept: AI đọc ảnh và xác nhận ảnh thể hiện bản A ở chế độ normal
với Stock value là 0 đ. AI đối chiếu a.js, xác định dòng 52–56
không chờ các callback async hoàn tất trước khi hiển thị tổng.
Tôi dùng hướng dẫn để ghi kết quả kiểm thử và câu yêu cầu sửa.

Changed: AI chỉ ra ảnh hiện tại chưa có DevTools hoặc terminal
theo rubric; đề xuất chụp lại giao diện cùng Network và đặt tên
a-row2.png. Chưa sửa code trong lần tương tác này.

Rejected: Chưa có nhận định sai của AI được kiểm thử bác bỏ
trong lần tương tác này.

Verification: Các kết quả PASS/FAIL là từ lần tôi chạy kiểm thử.
AI chỉ đọc ảnh, code và tài liệu hiện có, không tự chạy lại
toàn bộ bảng.