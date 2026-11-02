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