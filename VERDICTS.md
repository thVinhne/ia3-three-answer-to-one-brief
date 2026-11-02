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

- Vị trí trong file gốc: public/versions/b.js, dòng 16.
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

## Kết quả sau sửa: Version A (2026-11-03)

Bản gốc vẫn có verdict `fix`, mục quyết định là mục 2. Kết quả bản gốc và dự đoán ở trên được giữ nguyên. Sau khi trợ lý sửa, tôi xác nhận đã chạy lại các mục cần kiểm tra và tất cả đều PASS. Bảng dưới ghi kết quả do tôi xác nhận; trợ lý không tự chạy lại trình duyệt hoặc TypeScript checker.

### Thay đổi trong a.js

Chỉ thay khối tính tổng tồn kho. Dòng 51 chờ `Promise.all(products.map(...))` lấy đủ stock; dòng 52 dùng `reduce(..., 0)` tính tổng theo chỉ số tương ứng của sản phẩm và stock; dòng 53 mới cập nhật giao diện. Các request được khởi chạy bởi `map` trước khi chờ kết quả, nên vẫn chạy song song.

```js
const stocks = await Promise.all(products.map((p) => getStock(p.id)));
const total = products.reduce((sum, p, i) => sum + p.price * stocks[i], 0);
stockValue.textContent = formatVnd(total);
```

### Kiểm tra lại sau sửa do tôi xác nhận

| Mục | Kết quả | Tiêu chí đã được tôi xác nhận đạt sau sửa | Nguồn |
|---|---|---|---|
| 1 | PASS | normal: tám thẻ đúng thứ tự API, giá đúng định dạng, trạng thái trống. | Xác nhận chạy lại của tôi trong chat. |
| 2 | PASS | normal: tổng tồn kho là `12.360.000 đ`. | Xác nhận chạy lại của tôi; kiểm tra tự động của trợ lý cũng đạt. Ảnh hiện có cần chụp lại đúng A, xem lưu ý bên dưới. |
| 3 | PASS | Tên cuối hiển thị nguyên văn `Scarf <i>limited</i>`, không in nghiêng. | Xác nhận chạy lại của tôi trong chat. |
| 4 | PASS | slow: Loading khoảng ba giây, sau đó đủ thẻ và xóa trạng thái. | Xác nhận chạy lại của tôi trong chat. |
| 5 | PASS | error: báo lỗi, có Retry, không có thẻ; chuyển normal rồi Retry khôi phục danh mục và ẩn thông báo/nút. | Xác nhận chạy lại của tôi trong chat. |
| 6 | PASS | hang: báo lỗi và có Retry sau khoảng năm giây. | Xác nhận chạy lại của tôi; kiểm tra tự động đo được khoảng 5.007 ms. |
| 7 | PASS | Không có lỗi Uncaught hoặc lỗi do code tự in trong các lượt 1–6. | Xác nhận chạy lại của tôi trong chat. |
| 8 | PASS | Kiểm tra lại bằng `@ts-check` không có lỗi cho A. | Xác nhận của tôi rằng các mục cần chạy lại đều PASS; trợ lý chưa chạy TypeScript checker. |
| empty | PASS | Không có thẻ, hiện `No products yet.` và `0 đ`. | Xác nhận chạy lại của tôi trong chat. |

### Kiểm tra tự động của trợ lý và yêu cầu song song

Trợ lý đã chạy a.js bằng Node.js với DOM mô phỏng tối thiểu, gọi HTTP thật tới một tiến trình starter `server.mjs` riêng dưới đủ năm mode. Trước sửa, kiểm tra mục 2 thất bại vì thực nhận `0 đ` thay vì `12.360.000 đ`. Sau sửa, cùng kiểm tra đạt tổng đúng; thứ tự tám sản phẩm, giá thẻ đầu, nội dung tên scarf, slow, HTTP 500, Retry, hang và empty đều đạt các assertion đã chạy. Không ghi nhận `unhandledRejection` hoặc `console.error`. Kiểm tra cú pháp `node --check` cũng đạt.

Kiểm tra song song đo thời điểm bắt đầu tám request stock trước thời điểm request đầu tiên hoàn thành. Ở normal, độ lệch giữa request bắt đầu sớm nhất và muộn nhất khoảng 1,6 ms; ở slow và sau Retry khoảng 0,4 ms. Đây là số đo tự động trong Node, không phải ảnh waterfall trình duyệt. Các kiểm tra tự động này không tự xác nhận hiển thị bằng trình duyệt hoặc mục 8; kết quả chạy lại của tôi bổ sung các phần đó.

### Bằng chứng mục 2 sau sửa

Ảnh được cung cấp tại [evidence/a-row2-fix.png](evidence/a-row2-fix.png) hiện hiển thị `localhost:5050/?v=c` và tiêu đề `Catalogue — version C`, dù tổng trong ảnh là `12.360.000 đ`. Vì vậy, ảnh này chưa chứng minh A sau sửa. Cần chụp lại trang `/?v=a` ở normal, với tổng đúng và Network hiển thị các request stock để có bằng chứng khớp phiên bản. Chưa đổi tên, chỉnh sửa hoặc thay thế ảnh hiện có.

Kết luận sau sửa: A đạt đủ mục 1–8 và empty theo xác nhận chạy lại của tôi; yêu cầu stock song song đạt kiểm tra tự động. Cần bổ sung đúng ảnh A mục 2. Chưa ghi nhận commit cho lần sửa này trong chat; cập nhật báo cáo được thực hiện riêng với thay đổi code.


## Kết quả sau sửa: Version B (2026-11-03)

Bản gốc vẫn có verdict `fix` vì mục 6 và 8 FAIL. Tôi đã chạy lại toàn bộ mục 1–8 và empty sau sửa, xác nhận tất cả PASS. Dự đoán và kết quả bản gốc ở trên được giữ để đối chiếu trước/sau.

Chỉ sửa dòng 16 trong b.js: thay `timeout: 5000` bằng `signal: AbortSignal.timeout(5000)`. Khi hết năm giây, request bị hủy và catch hiện thông báo lỗi cùng Retry. Luồng Retry và `Promise.all(products.map(...))` gửi stock song song được giữ nguyên.

| Mục | Sau sửa | Kết quả được tôi xác nhận | Bằng chứng hoặc nguồn |
|---|---|---|---|
| 1 | PASS | normal: tám thẻ đúng thứ tự API, giá đúng định dạng, trạng thái trống. | Xác nhận chạy lại toàn bộ của tôi trong chat. |
| 2 | PASS | Tổng tồn kho là `12.360.000 đ` sau khi nhận đủ stock. | Xác nhận của tôi; kiểm tra tự động của trợ lý cũng đạt. |
| 3 | PASS | Tên cuối hiển thị nguyên văn `Scarf <i>limited</i>`, không in nghiêng. | Xác nhận chạy lại của tôi. |
| 4 | PASS | slow: Loading khoảng ba giây, rồi đủ tám thẻ và xóa trạng thái. | Xác nhận của tôi; kiểm tra tự động cũng đạt. |
| 5 | PASS | error: báo lỗi, có Retry, không có thẻ; chuyển normal rồi Retry khôi phục danh mục, xóa thông báo và ẩn nút. | Xác nhận của tôi; trợ lý cũng kiểm tra Retry sau error và hang. |
| 6 | PASS | hang: sau khoảng năm giây hiện `Could not load products.` và Retry. | [b-row6-fix.png](evidence/b-row6-fix.png); xác nhận của tôi; kiểm tra tự động khoảng 5004 ms. |
| 7 | PASS | Không có lỗi Uncaught hoặc lỗi do code tự in trong các lượt 1–6. | Xác nhận chạy lại trên trình duyệt của tôi. |
| 8 | PASS | Bật `@ts-check` cho b.js, không có lỗi kiểu. | [b-row8-fix.png](evidence/b-row8-fix.png); TypeScript checker của trợ lý cũng nhận 0 diagnostic cho B. |
| empty | PASS | Không có thẻ; hiện `No products yet.` và `0 đ`. | Xác nhận của tôi; kiểm tra tự động cũng đạt. |

### Kiểm tra của trợ lý trong lần sửa B

Trợ lý chạy b.js bằng Node.js với DOM giả lập, gọi HTTP tới server starter thật trên cổng riêng. Trước sửa, khoảng 5506 ms vẫn Loading và Retry ẩn. Sau sửa, hang hiện lỗi và Retry sau khoảng 5004 ms. Các assertion đã chạy ở normal, slow, error, hang, empty và Retry đều đạt, gồm thứ tự tám thẻ, giá thẻ đầu, tên scarf nguyên văn và tổng đúng.

Tám request stock bắt đầu trong khoảng 0,5–1,5 ms ở các lần đo, xác nhận vẫn gửi song song trong kiểm tra tự động. Đây là số đo Node, không phải ảnh waterfall trình duyệt.

Kiểm tra cú pháp `node --check` đạt. TypeScript 6.0.3 với jsconfig.json của starter và `@ts-check` thêm trong bộ nhớ tái hiện TS2353 của tùy chọn cũ; bản sửa có 0 diagnostic cho B. Các kiểm tra tự động này được thực hiện trong lần sửa trước. Lần cập nhật báo cáo chỉ đọc file và ảnh; các kết quả trình duyệt ở bảng là từ xác nhận chạy lại của tôi.

### Đối chiếu ảnh sau sửa

- [b-row6-fix.png](evidence/b-row6-fix.png) hiển thị đúng URL `/?v=b`, version B, mode hang, thông báo lỗi và Retry. Ảnh không có thời gian chờ hoặc Network, nên mốc năm giây dựa vào xác nhận của tôi và số đo tự động.
- [b-row8-fix.png](evidence/b-row8-fix.png) hiển thị b.js với `@ts-check` ở dòng đầu, signal hợp lệ và Problems báo không phát hiện vấn đề. Lệnh fetch ở dòng 17 trong ảnh do thêm dòng kiểm tra; trong file hiện tại, vị trí là dòng 16.

Kết luận sau sửa: B đạt đủ mục 1–8 và empty theo xác nhận chạy lại của tôi; yêu cầu stock song song đạt kiểm tra tự động. Chưa phát hiện lỗi mới qua các lượt này. Lần cập nhật báo cáo không tạo commit.
