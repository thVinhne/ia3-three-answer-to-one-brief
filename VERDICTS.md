# VERDICTS

## Dự đoán trước khi chạy thử

Các dự đoán dưới đây chỉ dựa trên việc đọc code và brief.md,
chưa dựa trên kết quả chạy chương trình.

### Version A — public/versions/a.js

| Mục | Nội dung kiểm tra | Dự đoán | Lý do từ code |
|---|---|---|---|
| 1 | 8 thẻ đúng thứ tự, giá đúng định dạng, trạng thái trống | PASS | 	products.map(...) tạo thẻ theo thứ tự mảng API; list.replaceChildren(...cards) đưa chúng lên trang. formatVnd() định dạng giá theo vi-VN. Với danh sách có sản phẩm, statusLine.textContent được đặt thành ''. |
| 2 | Tổng tồn kho là 12.360.000 đ | FAIL | products.forEach(async (...) => ...) khởi chạy các request nhưng không chờ chúng hoàn tất. Dòng hiển thị tổng chạy ngay khi total vẫn bằng 0, và không được cập nhật lại sau khi stock trả về. |
| 3 | Hiển thị nguyên văn Scarf '<i>limited</i>' | PASS | Trong renderCards(), tên sản phẩm được gán bằng textContent nên các ký tự <i> và </i> được hiển thị dưới dạng văn bản, không tạo chữ in nghiêng. |
| 4 | Loading khoảng 3 giây trong chế độ slow | PASS | load() đặt Loading products… trước await getProducts(). Khi server trả dữ liệu sau khoảng ba giây, code render thẻ và xóa dòng trạng thái. |
| 5 | Báo lỗi và Retry phục hồi đúng | PASS| getProducts() kiểm tra res.ok và ném lỗi khi nhận HTTP 500. catch xóa thẻ, đặt thông báo lỗi và hiện Retry. Nhấn Retry gọi lại load(), ẩn nút và tải lại; thành công thì hiện thẻ, xóa thông báo. |
| 6 | Dừng chờ sau khoảng 5 giây trong chế độ hang | PASS | Request sản phẩm dùng signal: AbortSignal.timeout(5000). Khi hết thời gian, fetch bị hủy; lỗi được xử lý trong catch để hiện thông báo và Retry. |
| 7 | Console không có lỗi do code gây ra | PASS| Lỗi request sản phẩm được xử lý bằng try/catch, không gọi console.error. Các endpoint stock của starter trả thành công cho ID hợp lệ, nên dự đoán không có lỗi Promise chưa được xử lý trong các lượt thử này. |
| 8 | Không có lỗi khi thêm // @ts-check | PASS | Các phần tử DOM có ép kiểu JSDoc phù hợp. Với cấu hình jsconfig.json của starter, chưa thấy thao tác gây lỗi kiểu; lỗi không chờ trong forEach(async ...) không nhất thiết bị bộ kiểm tra kiểu phát hiện. |
| empty | Hiện No products yet. và 0 đ | PASS | Mảng rỗng khiến renderCards() xóa hết thẻ; trạng thái thành No products yet. Không có vòng lặp stock, total giữ nguyên 0 và được hiển thị thành 0 đ. |

- Quyết định dự kiến: fix.
- Mục quyết định: mục 2.
- Hướng yêu cầu sửa dự kiến: Chờ tất cả request stock hoàn tất
  trước khi tính và hiển thị tổng, đồng thời giữ các request chạy song song.

### Version B — public/versions/b.js

| Mục | Nội dung kiểm tra | Dự đoán | Lý do từ code |
|---|---|---|---|
| 1 | 8 thẻ đúng thứ tự, giá đúng định dạng, trạng thái trống | PASS | products.map(createCard) giữ thứ tự API, rồi catalogue.append(...) thêm các thẻ theo thứ tự đó. toVnd() định dạng giá theo vi-VN; danh sách không rỗng thì trạng thái được đặt thành ''. |
| 2 | Tổng tồn kho là 12.360.000 đ | PASS | await Promise.all(products.map(...)) chờ đủ kết quả stock và vẫn gửi request song song. reduce() cộng price × stocks[i], rồi mới hiển thị tổng. |
| 3 | Hiển thị nguyên văn Scarf '<i>limited</i>' | PASS | card.querySelector('.name').textContent = product.name hiển thị tên dưới dạng văn bản, không diễn giải <i> thành HTML. |
| 4 | Loading khoảng 3 giây trong chế độ slow | PASS | 	Code đặt Loading products… trước khi chờ loadProducts(). Sau khi nhận sản phẩm, code thêm thẻ và xóa trạng thái. |
| 5 | Báo lỗi và Retry phục hồi đúng | PASS | loadProducts() ném lỗi khi response.ok là false. catch hiện thông báo và Retry. Mỗi lần showCatalogue() chạy, code ẩn Retry và xóa danh mục cũ; lần tải thành công sẽ hiện lại thẻ và xóa thông báo. |
| 6 | Dừng chờ sau khoảng 5 giây trong chế độ hang | FAIL | fetch('/api/products', { timeout: 5000 }) không thiết lập timeout hợp lệ cho Fetch API của trình duyệt. Vì vậy, dự đoán trang vẫn Loading khi đã qua năm giây, cho đến khi server trả HTTP 504 sau khoảng 30 giây. |
| 7 | Console không có lỗi do code gây ra | PASS | Luồng tải được bao trong try/catch, không gọi console.error; các Promise tải stock được chờ bằng Promise.all. Tùy chọn timeout không hợp lệ thường bị trình duyệt bỏ qua, không tự gây lỗi JavaScript |
| 8 | Không có lỗi khi thêm // @ts-check | FAIL | 	Đối tượng tùy chọn truyền vào fetch có thuộc tính timeout, nhưng kiểu RequestInit không có thuộc tính này. Dự đoán VS Code sẽ báo lỗi tại { timeout: 5000 } |
| empty | Hiện No products yet. và 0 đ | PASS | Danh mục được xóa, trạng thái thành No products yet. Promise.all([]) hoàn tất với mảng rỗng; reduce(..., 0) trả về 0, được định dạng thành 0 đ. |

- Quyết định dự kiến: fix.
- Mục quyết định: mục 6 và mục 8.
- Hướng yêu cầu sửa dự kiến: Thay tùy chọn timeout không được
  Fetch API hỗ trợ bằng cơ chế hủy request sau năm giây hợp lệ,
  giữ nguyên khả năng Retry và việc tải stock song song.

### Version C — public/versions/c.js

| Mục | Nội dung kiểm tra | Dự đoán | Lý do từ code |
|---|---|---|---|
| 1 | 8 thẻ đúng thứ tự, giá đúng định dạng, trạng thái trống | PASS | Hàm render() duyệt products bằng for...of, thêm thẻ vào fragment theo thứ tự API rồi đưa lên trang. money() định dạng giá theo vi-VN; danh sách có sản phẩm thì trạng thái được đặt thành ''. |
| 2 | Tổng tồn kho là 12.360.000 đ | PASS | await Promise.all(products.map(...)) chờ đủ kết quả stock. Sau đó code cộng p.price * stock[i] và mới hiển thị tổng. Các request stock được khởi chạy song song. |
| 3 | Hiển thị nguyên văn Scarf '<i>limited</i>' | PASS | card.querySelector('.name').textContent = p.name đưa tên lên trang dưới dạng văn bản, nên các dấu < > được giữ nguyên, không tạo chữ in nghiêng. |
| 4 | Loading khoảng 3 giây trong chế độ slow | PASS | 	start() đặt Loading products… trước await fetchProducts(). Sau khi nhận dữ liệu, code render thẻ và xóa trạng thái nếu danh sách không rỗng. |
| 5 | Báo lỗi và Retry phục hồi đúng | PASS | fetchProducts() ném lỗi khi res.ok là false. catch xóa thẻ, hiện thông báo và Retry. Nhấn Retry gọi lại start(), ẩn nút; khi tải thành công, code hiện thẻ và xóa thông báo. |
| 6 | Dừng chờ sau khoảng 5 giây trong chế độ hang | PASS | Request sản phẩm sử dụng signal: AbortSignal.timeout(5000). Khi hết thời gian, request bị hủy và catch xử lý để hiện thông báo lỗi cùng nút Retry. |
| 7 | Console không có lỗi do code gây ra | PASS | 	Luồng tải sản phẩm và stock nằm trong try/catch; các Promise stock được chờ bằng Promise.all. Code không gọi console.error cho lỗi dự kiến. |
| 8 | Không có lỗi khi thêm // @ts-check | PASS | Các phần tử DOM được khai báo kiểu bằng JSDoc; tùy chọn signal truyền vào fetch hợp lệ. Với jsconfig.json của starter, chưa thấy thao tác gây lỗi kiểu. |
| empty | Hiện No products yet. và 0 đ | PASS | render([]) thay danh mục bằng fragment rỗng; trạng thái thành No products yet. Promise.all([]) hoàn tất, total giữ nguyên 0 và được hiển thị thành 0 đ. |

- Quyết định dự kiến: keep.
- Cơ sở: Chưa thấy vi phạm các mục 1–8 và empty khi đọc code;
  request stock chạy song song và được chờ trước khi tính tổng.
- Điều cần kiểm chứng: Chạy đủ bảng, đặc biệt timeout trong
  chế độ hang, thứ tự request trong Network và kiểm tra @ts-check.

  ## Kết quả kiểm thử bản gốc

### Version A

| Mục | Kết quả | Quan sát thực tế | Dòng gây lỗi | Bằng chứng |
|---|---|---|---|---|
| 1 | PASS | Chế độ normal hiển thị tám thẻ đúng thứ tự API, giá đúng định dạng Việt Nam và dòng trạng thái trống. | — | Kiểm tra trực tiếp trên trang và đối chiếu API. |
| 2 | FAIL | Chế độ normal hiển thị Stock value là 0 đ, thay vì 12.360.000 đ. | public/versions/a.js, dòng 52–56. | evidence/a-row2.png |
| 3 | PASS | Thẻ cuối hiển thị nguyên văn Scarf <i>limited</i>, nhìn thấy các dấu ngoặc và không có chữ in nghiêng. | — | Kiểm tra thẻ cuối trên trang. |
| 4 | PASS | Chế độ slow hiển thị Loading products… khoảng ba giây, sau đó xuất hiện tám thẻ và dòng trạng thái được xóa. | — | Quan sát trong lần chạy slow. |
| 5 | PASS | Chế độ error hiện Could not load products., có Retry và không có thẻ; chuyển normal rồi nhấn Retry thì hiện tám thẻ, thông báo và nút Retry biến mất. | — | Thử error → normal → Retry. |
| 6 | PASS | Chế độ hang hiện thông báo lỗi và Retry sau khoảng năm giây, không tiếp tục Loading đến giây thứ bảy. | — | Quan sát thời gian trong lần chạy hang. |
| 7 | PASS | Trong các lượt thử 1–6, không thấy lỗi Uncaught hoặc lỗi do code tự in; dòng GET … 500 do trình duyệt báo xuất hiện khi thử error. | — | Quan sát Console. |
| 8 | PASS | Thêm tạm // @ts-check vào dòng đầu a.js khi mở đúng thư mục dự án; không có lỗi được báo cho file này. Đã bỏ dòng kiểm tra sau đó. | — | Kiểm tra trong VS Code. |
| empty | PASS | Chế độ empty không có thẻ, hiển thị No products yet. và tổng 0 đ. | — | Quan sát trong lần chạy empty. |

#### Nguyên nhân mục 2

Ở dòng 52–55, products.forEach(async ...) khởi chạy các thao tác
lấy tồn kho nhưng không chờ chúng hoàn tất. Dòng 56 hiển thị total
khi giá trị vẫn bằng 0. Khi các callback cập nhật total sau đó,
code không cập nhật lại nội dung trên giao diện.

- Verdict: fix.
- Mục quyết định: mục 2.
- Đối chiếu dự đoán: Dự đoán mục 2 FAIL được kết quả thực tế xác nhận.
- Một câu gửi assistant:
  “Ở bản A, mục 2 trong chế độ normal hiển thị 0 đ thay vì
  12.360.000 đ vì dòng 52–56 không chờ các request stock hoàn tất;
  chỉ sửa a.js để chờ đủ kết quả rồi tính và hiển thị tổng,
  đồng thời giữ các request stock chạy song song và các mục
  đang PASS tiếp tục PASS.”

  ### Version B — Kết quả bản gốc

| Mục | Kết quả | Quan sát thực tế | Bằng chứng |
|---|---|---|---|
| 1 | PASS | normal hiển thị tám thẻ đúng thứ tự API, giá đúng định dạng và dòng trạng thái trống. | Kiểm tra giao diện và đối chiếu Response của /api/products. |
| 2 | PASS | Sau khi các request tồn kho hoàn tất, tổng hiển thị 12.360.000 đ. | Quan sát giao diện và Network. |
| 3 | PASS | Tên cuối hiển thị nguyên văn Scarf <i>limited</i>, không in nghiêng. | Quan sát thẻ cuối. |
| 4 | PASS | slow hiện Loading products… khoảng ba giây, sau đó hiện tám thẻ và xóa trạng thái. | Quan sát lần chạy slow. |
| 5 | PASS | error hiện thông báo lỗi, Retry và không có thẻ; chuyển normal rồi nhấn Retry thì hiện tám thẻ, xóa thông báo và ẩn Retry. | Thử error → normal → Retry. |
| 6 | FAIL | Sau hơn năm giây ở chế độ hang, trang vẫn Loading và chưa có Retry. Khoảng 30 giây sau, server trả HTTP 504, trang mới hiện thông báo lỗi và Retry. | [Ảnh sau hơn năm giây](evidence/b-row6.png) |
| 7 | PASS | Trong các lượt thử 1–6, không thấy lỗi Uncaught hoặc lỗi do code tự in. | Quan sát Console; phân biệt các dòng HTTP lỗi do trình duyệt tự báo. |
| 8 | FAIL | Bật // @ts-check, VS Code báo ts(2353): thuộc tính timeout không tồn tại trong kiểu RequestInit. | [Ảnh lỗi kiểm tra kiểu](evidence/b-row8.png) |
| empty | PASS | Không có thẻ; trạng thái là No products yet. và tổng là 0 đ. | Quan sát lần chạy empty. |

#### Dòng code gây lỗi

Mục 6 và mục 8 cùng liên quan đến lệnh:

fetch('/api/products', { timeout: 5000 })

- Vị trí trong file gốc: public/versions/b.js, dòng [điền số dòng].
- Mục 6: thuộc tính timeout không thiết lập thời hạn chờ cho
  Fetch API của trình duyệt, nên request không bị hủy sau năm giây.
- Mục 8: timeout không phải thuộc tính hợp lệ của RequestInit,
  nên bộ kiểm tra kiểu báo lỗi.

#### Verdict

- Quyết định: fix.
- Mục quyết định: 6 và 8.
- Đối chiếu dự đoán: Kết quả kiểm thử xác nhận dự đoán
  FAIL ở mục 6 và mục 8.
- Một câu gửi assistant:
  “Bản B vẫn Loading sau hơn năm giây trong chế độ hang và
  @ts-check báo timeout không thuộc RequestInit; chỉ sửa b.js
  để thay tùy chọn này bằng cơ chế hủy request sau năm giây
  hợp lệ, giữ Retry hoạt động và các request stock chạy song song.”

  ### Version C — Kết quả bản gốc

| Mục | Kết quả | Quan sát thực tế | Bằng chứng |
|---|---|---|---|
| 1 | PASS | normal hiển thị tám thẻ đúng thứ tự API, giá đúng định dạng và dòng trạng thái trống. | Kiểm tra giao diện và đối chiếu Response của /api/products. |
| 2 | PASS | Sau khi các request tồn kho hoàn tất, tổng hiển thị 12.360.000 đ. | Quan sát giao diện và Network. |
| 3 | PASS | Tên cuối hiển thị nguyên văn Scarf <i>limited</i>, không in nghiêng. | Quan sát thẻ cuối. |
| 4 | PASS | slow hiện Loading products… khoảng ba giây, sau đó hiện tám thẻ và xóa trạng thái. | Quan sát lần chạy slow. |
| 5 | PASS | error hiện thông báo lỗi, Retry và không có thẻ; chuyển normal rồi nhấn Retry thì hiện tám thẻ, xóa thông báo và ẩn Retry. | Thử error → normal → Retry. |
| 6 | PASS | hang hiện Could not load products. và Retry sau khoảng năm giây; đến giây thứ bảy không còn Loading. | Quan sát thời gian, giao diện và Network. |
| 7 | PASS | Trong các lượt thử 1–6, không thấy lỗi Uncaught hoặc lỗi do code tự in. | Quan sát Console. |
| 8 | PASS | Thêm // @ts-check ở dòng đầu c.js, không thấy lỗi kiểm tra kiểu cho file này; đã bỏ dòng thêm tạm sau kiểm tra. | Kiểm tra trong VS Code. |
| empty | PASS | Không có thẻ; trạng thái là No products yet. và tổng là 0 đ. | Quan sát lần chạy empty. |

#### Verdict

- Quyết định: keep.
- Mục quyết định: Không có mục FAIL trong các mục 1–8 và empty.
- Dòng code gây lỗi: Không phát hiện qua các lượt kiểm thử này.
- Đối chiếu dự đoán: Kết quả kiểm thử xác nhận dự đoán PASS
  của cả chín mục.
- Một câu gửi assistant:
  “Giữ nguyên c.js vì các lượt chạy đã đạt đủ mục 1–8 và empty;
  khi sửa các bản khác, cần giữ nguyên hành vi hiển thị,
  timeout, Retry và việc tải stock song song như yêu cầu.”