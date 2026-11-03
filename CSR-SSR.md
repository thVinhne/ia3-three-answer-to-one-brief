1. Tên sản phẩm và tổng tồn kho nằm ở đâu trong View Source?

Tôi kiểm tra trang CSR /?v=c và trang SSR /ssr. Trong source của CSR, danh mục chưa có thẻ sản phẩm; template chỉ chứa các phần tử trống và stock-value chứa ký hiệu —. Tên sản phẩm và tổng tiền chỉ xuất hiện trên giao diện sau khi JavaScript tải dữ liệu API và cập nhật DOM.
Trong source của SSR, tôi tìm thấy sẵn tên sản phẩm, các thẻ sản phẩm và tổng tồn kho 12.360.000 đ. Server đã tạo các nội dung này trước khi gửi HTML về trình duyệt.

2. Vì sao source SSR chứa &lt;i&gt;, và điều này giống textContent thế nào?

Trong source SSR, tên khăn được viết là Scarf &lt;i&gt;limited&lt;/i&gt;. Trên giao diện, tôi thấy nguyên văn Scarf <i>limited</i>, không có chữ in nghiêng.
Server đã mã hóa các dấu ngoặc để trình duyệt hiển thị chúng như văn bản. Ở CSR, textContent cũng coi tên sản phẩm là văn bản, nên chuỗi <i> không bị diễn giải thành thẻ HTML.

3. Khi tắt JavaScript và reload, mỗi trang còn gì?

Sau khi tắt JavaScript, trang CSR vẫn có khung giao diện, nhưng không xuất hiện thẻ sản phẩm; tổng tồn kho vẫn là —. JavaScript không chạy nên trang không gọi API và không tạo danh mục.
Trang SSR vẫn hiển thị sản phẩm và tổng tồn kho 12.360.000 đ, vì chúng đã có trong HTML server trả về.

4. Mỗi trang cần bao nhiêu request trước khi hiện tổng tồn kho? Trang nào cung cấp nội dung sớm hơn?

Khi bật lại JavaScript, xóa danh sách Network và reload, tôi đếm được 14 request ở CSR và 2 request ở SSR.
CSR cần tải JavaScript, gọi /api/products, rồi gửi tám request /api/stock/:id để tính tổng. SSR nhận sẵn danh mục và tổng tiền trong response HTML, không cần trình duyệt gọi các API này.
Trong ví dụ này, SSR cung cấp nội dung ngay trong HTML ban đầu, thuận lợi cho công cụ tìm kiếm đọc nội dung và giảm công việc JavaScript trên điện thoại chậm. CSR phải chờ JavaScript chạy và các request dữ liệu hoàn tất mới hiện đủ nội dung.

5. Khi nào nên render ở server, khi nào ở trình duyệt?

Tôi chọn SSR cho trang cần nội dung có sẵn trong HTML và vẫn đọc được khi tắt JavaScript; chọn CSR cho các phần giao diện cần tương tác hoặc cập nhật dữ liệu thường xuyên mà không tải lại toàn bộ trang.