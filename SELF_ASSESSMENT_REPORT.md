# Self-assessment — IA#3

Submitted by: 24120157 — Lê Thái Vinh

Total I claim: 100 / 100

Tên ZIP dự kiến: `24120157_97.zip`.

| Criterion | Max | Claim | Evidence |
|---|---:|---:|---|
| Verdicts and evidence | 30 | 30 | `VERDICTS.md` có 27 dự đoán và 27 kết quả bản gốc; A lỗi mục 2, B lỗi mục 6 và 8, có dòng code và câu yêu cầu sửa. Ảnh FAIL: `evidence/a-row2.png`, `evidence/b-row6.png`, `evidence/b-row8.png`. Ảnh B bổ sung ngày 03/11/2026: `b-row6.png` cho thấy Loading và request Pending với đồng hồ 5,55 giây; `evidence/b-row6-30s.png` cho thấy HTTP 504 và thời gian request 30,01 giây trong Network. Đồng hồ là phép đo thủ công; số 30,01 giây là số đo Network. Khoản trừ 5 điểm vì thiếu bằng chứng thời gian đã được khắc phục. Commit dự đoán `9fe8b2486783` đứng trước commit kết quả `d98cf0f786e9`. |
| Every version passes every row | 25 | 25 | `public/versions/a.js`, `b.js`, `c.js`; kết quả tôi ghi trong `VERDICTS.md`. Lần kiểm tra trước của trợ lý bằng Chrome thật trên bản sao dùng server starter xác nhận cả ba đạt mục 1–8, empty và stock song song. TypeScript 6.0.3 cho 0 diagnostic ở mỗi bản; timeout A/B/C khoảng 5013,90 / 5011,90 / 5014,85 ms; tám stock request bắt đầu trong 0,282 / 0,450 / 0,278 ms. Đây là số đo của trợ lý, không phải lượt tôi tự chạy. Ảnh mới `evidence/a-row2-fix.png` hiển thị đúng version A, mode normal và tổng `12.360.000 đ`; `evidence/b-row6-fix.png` hiển thị đúng B, lỗi và Retry với đồng hồ 5,30 giây. Ảnh đồng hồ hỗ trợ ghi nhận thủ công, không thay số đo request; việc stock song song dựa trên kiểm tra trước của trợ lý, không suy từ tổng đúng trong ảnh. |
| The assistant's review, checked | 15 | 15 | `ASSISTANT-REVIEW.md` có công cụ/model, prompt, review Sol nguyên văn và 27 dòng đối chiếu với kết quả bản gốc. Sol đã tìm đủ A mục 2 và B mục 6/8; chưa có lỗi bỏ sót được xác nhận. Nhận định về rejection khi stock của A gốc lỗi được ghi rõ là rủi ro có điều kiện chưa thử, không bị coi là FAIL mục 7 trong các lượt chuẩn. Thử riêng stock lỗi là kiểm tra mở rộng ngoài bảng bắt buộc; không trừ điểm vì chưa thực hiện thử nghiệm này. |
| CSR vs SSR | 10 | 10 | `CSR-SSR.md` trả lời đủ năm câu; `evidence/csr-requests.png` và `ssr-requests.png` thể hiện 14/2 tổng request. Quan sát source, escape scarf và tắt JavaScript được ghi trong `CSR-SSR.md`. Lượt kiểm tra trước của trợ lý xác nhận các hành vi đó, 14/2 tổng request và 9/0 API lấy dữ liệu sản phẩm/tồn kho. |
| AI-LOG.md | 10 | 10 | `AI-LOG.md` có các mục riêng cho review, sửa A, sửa B, kiểm tra sau sửa, CSR–SSR và audit; ghi công cụ, yêu cầu, kết quả, Kept/Changed/Rejected và nguồn kiểm chứng. Có sai sót thật được ghi cụ thể: trợ lý từng ghi nhầm rằng tôi chưa cung cấp kết quả CSR và bộ checker tạm ban đầu không phát hiện lỗi B mục 8, sau đó đã sửa và tái hiện TS2353.|
| Scope and commit history | 10 | 10 | Commit `30154d2a0624` là starter, đủ 10 file khớp từng byte với ZIP. Dự đoán được commit trước kết quả. Ngoài A/B, các file starter giữ nguyên; C không đổi. |



## What I did not manage

None

## What I would do differently

Tôi sẽ chụp đúng URL, phiên bản và thông tin thời gian ngay khi thấy lỗi; kiểm tra nội dung ảnh trước khi dùng làm bằng chứng. Tôi sẽ lưu đầu ra kiểm thử, phân biệt tổng request với request API dữ liệu và ghi AI log ngay sau mỗi lần tương tác. Khi sửa, tôi sẽ commit riêng file code của từng phiên bản, rồi commit tài liệu và ảnh ở bước tiếp theo.
