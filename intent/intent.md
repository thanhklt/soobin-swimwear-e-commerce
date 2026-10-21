# intent.md — Website thương mại điện tử đồ bơi

| Mục | Nội dung |
|---|---|
| Phiên bản | 0.2 |
| Ngày | 21/10/2026 |
| Người lập | Business Analyst (Claude) cùng Originator |
| Nguồn | (1) `mo_ta_cnpm.docx` trong thư mục Source of Truth; (2) website tham chiếu sobie.vn; (3) buổi phỏng vấn Originator ngày 21/10/2026 |
| Mục đích | Chốt ý định, phạm vi và quy tắc nghiệp vụ làm đầu vào cho SRS, Use Case, ERD và Data Model |

**Lịch sử thay đổi**

| Phiên bản | Thay đổi |
|---|---|
| 0.1 | Bản nháp đầu tiên |
| 0.2 | Khuyến mãi chuyển sang cơ chế **không cộng dồn**: mỗi đơn chỉ áp 1 khuyến mãi mang lại số tiền giảm lớn nhất (mục 8). Bổ sung danh sách trường lưu vết khuyến mãi trên đơn hàng và chi tiết đơn (mục 8.5, 10.1) |

Ký hiệu nguồn dùng trong tài liệu: **[SoT]** lấy từ tài liệu Source of Truth, **[PV]** chốt qua phỏng vấn, **[SB]** bổ sung theo tham chiếu Sobie, **[GĐ]** giả định của BA cần Originator xác nhận.

---

## 1. Bối cảnh

Đây là đồ án môn Công nghệ phần mềm do nhóm 5 thành viên thực hiện **[PV]**. Nhóm đóng vai đơn vị xây dựng hệ thống cho một cửa hàng đồ bơi giả định đang chuyển đổi số. Cửa hàng có **1 kho duy nhất** và **1 kênh bán online là website đang xây dựng**, chưa có hệ thống cũ cần chuyển dữ liệu **[PV]**.

Website tham chiếu là sobie.vn. Dự án sao chép **cấu trúc, luồng mua sắm và nhóm tính năng** của Sobie, không sao chép thương hiệu, logo, hình ảnh sản phẩm hay nội dung chữ của Sobie. Dữ liệu mẫu (tên sản phẩm, ảnh, bài blog) do nhóm tự tạo.

## 2. Mục tiêu và đầu ra

### 2.1 Mục tiêu
1. Khách hàng có thể tìm, chọn biến thể (size, màu), đặt và thanh toán đồ bơi online với trải nghiệm tương đương Sobie.
2. Quản lý cửa hàng kiểm soát được sản phẩm, tồn kho, nhập hàng, đơn hàng, khuyến mãi và xem được thống kê doanh thu, lợi nhuận.
3. Admin quản trị tài khoản và phân quyền linh hoạt theo role.
4. Trạng thái giao hàng được cập nhật tự động qua webhook từ đơn vị vận chuyển (mô phỏng GHTK).

### 2.2 Đầu ra bắt buộc của môn học [PV]
| Đầu ra | Ghi chú |
|---|---|
| SRS | Dùng mã FR/BR/NFR trong tài liệu này để truy vết |
| Use Case | Danh sách sơ bộ ở mục 6 |
| ERD | Danh sách thực thể sơ bộ ở mục 12 |
| Data Model | Chi tiết hóa từ ERD (kiểu dữ liệu, khóa, ràng buộc) |

### 2.3 Tiêu chí hoàn thành [GĐ]
1. Chạy trọn luồng: khách đặt hàng → quản lý xác nhận → gửi đơn sang GHTK mock → webhook cập nhật "Đã giao" → hệ thống tự xuất hóa đơn → doanh thu xuất hiện trên dashboard.
2. Với mọi tổ hợp khuyến mãi, hệ thống chọn đúng phương án giảm nhiều nhất và tính đúng tổng tiền theo mục 8; đơn cũ không đổi khi giá hoặc CTKM thay đổi.
3. Tồn kho không bao giờ âm.
4. Một role mới do admin tạo chỉ truy cập được đúng các quyền được gán.

## 3. Phạm vi

### 3.1 Trong phạm vi MVP [PV]
Toàn bộ chức năng trong tài liệu SoT (trừ các mục ở 3.2) cộng toàn bộ nhóm tính năng storefront của Sobie, cụ thể: mega menu, trang chủ nhiều khối nội dung, thanh thông báo chạy, thêm nhanh vào giỏ, wishlist (không gửi email), trang Outlet, blog, đánh giá sản phẩm, trang cộng đồng, nút chat Zalo/hotline, trang chính sách/FAQ/giới thiệu/liên hệ, góp ý; dashboard thống kê.

### 3.2 Giai đoạn 2 (ngoài MVP) [PV]
| Chức năng | Ghi chú |
|---|---|
| Email nhắc giỏ hàng | Gửi **1 lần duy nhất** khi có sản phẩm nằm trong giỏ quá 24 giờ |
| Email báo giảm giá sản phẩm yêu thích | Wishlist vẫn có trong MVP, chỉ chưa có email |
| AI chatbox tư vấn sản phẩm và chọn size | Tạm hoãn |

### 3.3 Ngoài phạm vi [GĐ]
Nhiều kho/chi nhánh, bán trên sàn TMĐT, POS tại cửa hàng, hóa đơn điện tử VAT theo quy định thuế, hoàn tiền tự động, đổi/trả hàng, quà tặng theo bậc giá trị đơn và freeship theo ngưỡng (xem câu hỏi mở Q3), tích điểm thành viên, đa ngôn ngữ.

### 3.4 Ưu tiên trong MVP (MoSCoW) [GĐ]
Phạm vi MVP khá rộng so với nhóm 5 người, nên đề xuất gắn nhãn ưu tiên để cắt giảm nếu thiếu thời gian:

| Must | Should | Could |
|---|---|---|
| Đăng ký/đăng nhập, RBAC, sản phẩm & biến thể, danh mục, giỏ hàng, đặt hàng COD + VietQR, xử lý đơn, webhook GHTK mock, hóa đơn, phiếu nhập, khuyến mãi 3 loại, dashboard cơ bản | Mega menu, bộ lọc nâng cao, quick add, wishlist, trang Outlet, đánh giá sản phẩm, sổ địa chỉ | Blog, trang cộng đồng, góp ý, thanh thông báo chạy, banner cấu hình được |

## 4. Tác nhân và phân quyền

### 4.1 Tác nhân
| Tác nhân | Mô tả |
|---|---|
| Khách vãng lai | Xem, tìm kiếm sản phẩm, đọc blog. Muốn thêm giỏ, wishlist hoặc đặt hàng thì phải đăng nhập **[GĐ]** |
| Khách hàng | Người dùng đã đăng ký, role mặc định "Khách hàng" |
| Quản lý cửa hàng | Vận hành bán hàng, kho, khuyến mãi, nội dung, thống kê. Thực tế chỉ có **1 quản lý** **[PV]** |
| Admin | **Chỉ** quản lý tài khoản và quản lý role **[PV]** |
| GHTK mock | Hệ thống ngoài, gọi webhook báo trạng thái vận chuyển |
| Cổng VietQR (mock xác nhận) | Hệ thống ngoài, báo đã nhận tiền chuyển khoản |

Lưu ý thay đổi so với SoT: tài liệu SoT đặt "Quản lý tài khoản người dùng" trong nhóm chức năng của quản lý cửa hàng; theo phỏng vấn, chức năng này chuyển sang **Admin** **[PV]**. Mục "Quản lý nhân viên" trong SoT được hiểu là admin tạo tài khoản nội bộ và gán role **[GĐ]**.

### 4.2 Cổng đăng nhập [PV]
1. Storefront: khách hàng.
2. Portal quản trị: admin và quản lý dùng **chung một portal**; menu và API hiển thị theo quyền của role.

### 4.3 Mô hình phân quyền (RBAC) [PV]
- **FR-RBAC-01** Admin tạo, sửa, vô hiệu hóa role.
- **FR-RBAC-02** Admin gán linh hoạt danh sách quyền (permission) cho từng role. Quyền ở mức chức năng + hành động, ví dụ `product.create`, `order.confirm`, `report.view`.
- **FR-RBAC-03** Mỗi tài khoản có đúng 1 role **[GĐ]**.
- **BR-RBAC-01** Ba role hệ thống mặc định (Admin, Quản lý, Khách hàng) không được xóa **[GĐ]**.
- **BR-RBAC-02** Phân quyền được kiểm tra ở backend cho mọi API, không chỉ ẩn nút trên giao diện.

## 5. Yêu cầu chức năng

### 5.1 Storefront (khách) — theo mẫu Sobie [SB]
| Mã | Chức năng | Mô tả |
|---|---|---|
| FR-SF-01 | Trang chủ | Banner chính; khối "New Collection", "New Arrivals", khối theo bộ sưu tập (ví dụ Big size), khối tab Phụ kiện, khối tab Rashguard theo đối tượng, khối Swim theo đối tượng, banner đôi, khối cộng đồng (lưới ảnh), khối blog mới nhất |
| FR-SF-02 | Thanh thông báo chạy | Dòng chữ chạy đầu trang, nội dung do quản lý cấu hình |
| FR-SF-03 | Mega menu | Menu nhiều cấp: Dòng sản phẩm → Đối tượng → Bộ sưu tập/loại, kèm ảnh minh họa mỗi nhóm |
| FR-SF-04 | Trang danh mục | Lọc theo loại, đối tượng/giới tính, size, màu, chất liệu, khoảng giá, đang giảm giá; sắp xếp theo mới nhất, giá, bán chạy; phân trang |
| FR-SF-05 | Thẻ sản phẩm | Ảnh, tên, giá gốc và giá sau giảm, nhãn chất liệu, nhãn "Sale x%", nhãn "Hết hàng"; hover hiện danh sách size để thêm nhanh vào giỏ |
| FR-SF-06 | Chi tiết sản phẩm | Album ảnh theo màu, chọn màu và size, hiển thị tồn kho còn/hết theo biến thể, bảng size, mô tả, chất liệu, tính năng, hướng dẫn bảo quản, đánh giá, sản phẩm liên quan |
| FR-SF-07 | Tìm kiếm | Theo tên/từ khóa; gợi ý "từ khóa hot" do quản lý cấu hình **[SoT]** cho phép tìm theo loại, giới tính, số đo |
| FR-SF-08 | Trang Outlet | Tự động liệt kê sản phẩm đang thuộc CTKM theo sản phẩm còn hiệu lực, lọc theo mức giảm |
| FR-SF-09 | Blog | Danh sách và chi tiết bài viết |
| FR-SF-10 | Trang tĩnh | Giới thiệu, liên hệ, FAQ, chất liệu, các chính sách (bảo mật, thanh toán, vận chuyển, đổi trả, kiểm hàng) |
| FR-SF-11 | Trang cộng đồng | Lưới ảnh khách hàng/feedback do quản lý đăng |
| FR-SF-12 | Liên hệ nhanh | Nút nổi Zalo và gọi hotline (chỉ là liên kết) |
| FR-SF-13 | Góp ý | Form gửi góp ý, quản lý xem được |
| FR-SF-14 | Responsive | Giao diện ưu tiên mobile, có thanh điều hướng dưới trên mobile như Sobie |

### 5.2 Khách hàng
| Mã | Chức năng | Mô tả |
|---|---|---|
| FR-KH-01 | Đăng ký | Bằng email hoặc số điện thoại + mật khẩu **[SoT]** |
| FR-KH-02 | Đăng nhập/đăng xuất | Bằng email hoặc số điện thoại + mật khẩu **[SoT]** |
| FR-KH-03 | Quên mật khẩu | Đặt lại qua email **[GĐ]** |
| FR-KH-04 | Hồ sơ & sổ địa chỉ | Sửa thông tin cá nhân; lưu nhiều địa chỉ nhận hàng, chọn mặc định **[GĐ]** |
| FR-KH-05 | Giỏ hàng | Thêm, xóa, đổi số lượng **[SoT]**; số lượng không vượt tồn kho của biến thể |
| FR-KH-06 | Đặt hàng | Chọn địa chỉ, nhập voucher, chọn COD hoặc VietQR. Màn hình thanh toán hiển thị tạm tính, **khuyến mãi được hệ thống chọn** (tên, loại, số tiền giảm) và tổng thanh toán theo mục 8. Nếu voucher khách nhập không phải lựa chọn tốt nhất, hiển thị thông báo giải thích |
| FR-KH-07 | Thanh toán VietQR | Hiển thị mã QR kèm số tiền và nội dung chuyển khoản là mã đơn **[PV]** |
| FR-KH-08 | Theo dõi đơn | Danh sách đơn, chi tiết, lịch sử trạng thái |
| FR-KH-09 | Hủy đơn | Chỉ khi đơn ở trạng thái "Chưa xử lý" **[PV]** |
| FR-KH-10 | Xem hóa đơn | Với đơn đã giao thành công |
| FR-KH-11 | Wishlist | Thêm/xóa sản phẩm yêu thích **[SoT]**, không có email trong MVP **[PV]** |
| FR-KH-12 | Đánh giá sản phẩm | Chấm sao + nhận xét, chỉ cho sản phẩm thuộc đơn đã giao **[GĐ]** |

### 5.3 Quản lý cửa hàng
| Mã | Chức năng | Mô tả |
|---|---|---|
| FR-QL-01 | Quản lý sản phẩm | Thêm/sửa/xóa mềm **[SoT]**; thuộc tính theo mục 7 |
| FR-QL-02 | Quản lý biến thể | Biến thể theo **size × màu** **[PV]**, mỗi biến thể có SKU và tồn kho |
| FR-QL-03 | Quản lý danh mục, dòng sản phẩm, bộ sưu tập, chất liệu, màu, size | Danh mục dùng để dựng mega menu và bộ lọc |
| FR-QL-04 | Quản lý nhà cung cấp | Thêm/sửa/ngừng hợp tác **[PV]** |
| FR-QL-05 | Quản lý phiếu nhập | Theo mục 9 |
| FR-QL-06 | Xử lý đơn hàng | Xác nhận đơn, gửi đơn sang GHTK mock, xem lịch sử trạng thái |
| FR-QL-07 | Xem hóa đơn | Chỉ xem, không sửa **[PV]** |
| FR-QL-08 | Quản lý khuyến mãi | Theo mục 8 |
| FR-QL-09 | Thống kê (dashboard) | Theo mục 11 **[PV]** |
| FR-QL-10 | Quản lý nội dung | Banner, thanh thông báo chạy, bài blog, ảnh cộng đồng, trang tĩnh, từ khóa hot |
| FR-QL-11 | Kiểm duyệt đánh giá | Ẩn/hiện đánh giá **[GĐ]** |
| FR-QL-12 | Xem góp ý | Danh sách góp ý từ khách |

### 5.4 Admin
| Mã | Chức năng | Mô tả |
|---|---|---|
| FR-AD-01 | Quản lý tài khoản | Thêm/sửa/khóa tài khoản khách hàng và tài khoản nội bộ **[SoT][PV]** |
| FR-AD-02 | Quản lý role | Theo mục 4.3 **[PV]** |

## 6. Danh sách Use Case sơ bộ

| Mã | Use case | Tác nhân chính |
|---|---|---|
| UC01 | Đăng ký tài khoản | Khách vãng lai |
| UC02 | Đăng nhập / đăng xuất | Khách hàng, Quản lý, Admin |
| UC03 | Đặt lại mật khẩu | Khách hàng |
| UC04 | Duyệt và lọc sản phẩm | Khách vãng lai, Khách hàng |
| UC05 | Tìm kiếm sản phẩm | Khách vãng lai, Khách hàng |
| UC06 | Xem chi tiết sản phẩm | Khách vãng lai, Khách hàng |
| UC07 | Quản lý giỏ hàng | Khách hàng |
| UC08 | Đặt hàng | Khách hàng |
| UC09 | Thanh toán VietQR | Khách hàng, Cổng VietQR |
| UC10 | Theo dõi và hủy đơn hàng | Khách hàng |
| UC11 | Quản lý danh sách yêu thích | Khách hàng |
| UC12 | Đánh giá sản phẩm | Khách hàng |
| UC13 | Quản lý hồ sơ và sổ địa chỉ | Khách hàng |
| UC14 | Xem hóa đơn | Khách hàng, Quản lý |
| UC15 | Gửi góp ý / đọc blog | Khách vãng lai, Khách hàng |
| UC20 | Quản lý sản phẩm và biến thể | Quản lý |
| UC21 | Quản lý danh mục, bộ sưu tập, thuộc tính | Quản lý |
| UC22 | Quản lý nhà cung cấp | Quản lý |
| UC23 | Quản lý phiếu nhập | Quản lý |
| UC24 | Xử lý đơn hàng | Quản lý |
| UC25 | Quản lý chương trình khuyến mãi | Quản lý |
| UC26 | Xem thống kê | Quản lý |
| UC27 | Quản lý nội dung website | Quản lý |
| UC28 | Kiểm duyệt đánh giá | Quản lý |
| UC30 | Quản lý tài khoản | Admin |
| UC31 | Quản lý role và quyền | Admin |
| UC40 | Cập nhật trạng thái vận chuyển (webhook) | GHTK mock |
| UC41 | Xác nhận thanh toán (webhook) | Cổng VietQR mock |
| UC50–52 | Email nhắc giỏ hàng, email báo giảm giá wishlist, AI chatbox | Giai đoạn 2 |

## 7. Sản phẩm và biến thể

### 7.1 Thuộc tính sản phẩm (bổ sung theo Sobie) [SB][PV]
| Thuộc tính | Ví dụ / ghi chú |
|---|---|
| Mã sản phẩm, tên, slug | Slug dùng cho URL thân thiện SEO |
| Mô tả, hướng dẫn bảo quản | Văn bản giàu định dạng |
| Danh mục (loại) | Áo bơi, đồ bơi liền thân, quần bơi, bikini, chân váy, rashguard, wetsuit, kính bơi, nón bơi, chân vịt, khăn/poncho, giày đi biển, túi/balo… |
| Dòng sản phẩm | Swim Pro, Water sport, Chic, Wetsuit, Accessory |
| Bộ sưu tập | Nhiều-nhiều, ví dụ "Seaside Holidays", "Big size" |
| Đối tượng / giới tính | Nữ, Nam, Teen, Trẻ em, Baby, Unisex |
| Cấp độ | Cơ bản / Nâng cao (cho dòng Swim Pro) |
| Chất liệu | SOWAVE, NYLON, NEOPRENE, POLYCARBONATE, SILICONE, TERRY, TPR… (danh mục cấu hình được, hiện thành nhãn trên thẻ sản phẩm) |
| Thông số kỹ thuật | Độ dày wetsuit (1.5 mm, 2.5 mm), chỉ số chống UV (UPF50+), chống sương… |
| Kiểu dáng (tag) | Tay dài, tay ngắn, croptop, khóa kéo, kín đáo… |
| Giá bán | **Quản lý nhập tay**, không có quy tắc tự tính **[PV]**. Đặt ở cấp sản phẩm, mọi biến thể cùng giá **[GĐ]** |
| Hình ảnh | Nhiều ảnh, có thể gắn với màu |
| Bảng size | Số đo theo từng size (dài, rộng, vòng ngực, vòng eo, chiều cao/cân nặng gợi ý) **[SoT]** nhắc số đo dài, rộng |
| Trạng thái | Đang bán / Ngừng bán; cờ xóa mềm **[SoT]** |
| Ngày tạo, ngày cập nhật | Dùng cho khối "New Arrivals" |

### 7.2 Biến thể [PV]
- **BR-SP-01** Một biến thể = 1 sản phẩm × 1 màu × 1 size, có SKU duy nhất và số lượng tồn riêng.
- **BR-SP-02** Danh sách size cấu hình được vì mỗi loại hàng dùng hệ size khác nhau (XS–XL, 2XL–4XL, 2Y–5Y, 36–44, 55–60 cm, freesize).
- **BR-SP-03** Xóa sản phẩm là xóa mềm; sản phẩm đã xóa không hiện ở storefront nhưng vẫn giữ trong đơn hàng, hóa đơn, phiếu nhập cũ.
- **BR-SP-04** Biến thể tồn kho = 0 hiển thị "Hết hàng" và không thêm được vào giỏ.

## 8. Khuyến mãi [PV]

### 8.1 Cấu trúc dữ liệu
| Bảng | Khóa | Thuộc tính chính |
|---|---|---|
| CHUONG_TRINH_KM | PK mã CTKM | Tên, mô tả, loại (sản phẩm / hóa đơn / voucher), ngày bắt đầu, ngày kết thúc, trạng thái |
| KM_SAN_PHAM | PK + FK mã CTKM, FK mã sản phẩm | % giảm |
| KM_HOA_DON | PK + FK mã CTKM | Giá trị đơn tối thiểu **[GĐ]**, % giảm |
| KM_VOUCHER | PK + FK mã CTKM, mã voucher (duy nhất) | % giảm, số lượt dùng tối đa **[GĐ]**, giá trị đơn tối thiểu **[GĐ]** |

### 8.2 Nguyên tắc: không cộng dồn, chỉ chọn khuyến mãi tốt nhất [PV]
Mỗi đơn hàng chỉ được áp **đúng một** trong ba phương án khuyến mãi: theo sản phẩm, theo hóa đơn, hoặc voucher. Hệ thống tự tính cả ba và chọn phương án có **số tiền giảm lớn nhất**. Lý do: tránh việc kết hợp nhiều loại khuyến mãi làm kết quả phụ thuộc thứ tự áp mã, đồng thời luật đơn giản, dễ giải thích với khách và dễ kiểm thử.

### 8.3 Công thức tính tiền
```
Tạm tính gốc           G     = Σ SLᵢ × ĐGᵢ

Phương án 1 — Theo SP  D_sp  = Σ SLᵢ × ĐGᵢ × p_spᵢ
Phương án 2 — Hóa đơn  D_hđ  = G × p_hđ
Phương án 3 — Voucher  D_vc  = G × p_vc

Số tiền giảm           D     = max(D_sp, D_hđ, D_vc)
Tổng tiền hàng               = G − D
Tổng thanh toán              = G − D + Phí vận chuyển      (phí ship: xem Q3)
```
Trong đó:
- p_spᵢ = % giảm cao nhất trong các CTKM theo sản phẩm đang hiệu lực của sản phẩm dòng i; bằng 0 nếu không có.
- p_hđ = % giảm cao nhất trong các CTKM theo hóa đơn đang hiệu lực mà G đạt ngưỡng tối thiểu; bằng 0 nếu không có.
- p_vc = % giảm của voucher khách nhập, nếu voucher hợp lệ và G đạt ngưỡng tối thiểu; bằng 0 nếu không nhập hoặc không hợp lệ.
- Mọi % nằm trong khoảng [0, 100).

**Ví dụ.** Giỏ gồm áo 500.000₫ đang giảm 30% và quần 300.000₫ không giảm. G = 800.000₫. D_sp = 150.000₫. Có KM hóa đơn 10% cho đơn từ 700.000₫ → D_hđ = 80.000₫. Khách nhập voucher 15% → D_vc = 120.000₫. Hệ thống chọn phương án theo sản phẩm, tổng tiền hàng = 650.000₫; voucher không được dùng và không bị trừ lượt.

### 8.4 Quy tắc nghiệp vụ
- **BR-PROMO-01** Khuyến mãi chỉ áp dụng khi thời điểm đặt hàng nằm trong [ngày bắt đầu, ngày kết thúc] và CTKM đang bật.
- **BR-PROMO-02** Mỗi đơn chỉ áp **một** phương án khuyến mãi theo mục 8.3; không cộng dồn giữa các loại.
- **BR-PROMO-03** Trong phương án theo sản phẩm, mỗi sản phẩm lấy CTKM có % cao nhất nếu thuộc nhiều CTKM cùng lúc. Các dòng khác nhau có thể thuộc các CTKM sản phẩm khác nhau; cả nhóm này tính là một phương án.
- **BR-PROMO-04** Trong phương án theo hóa đơn, hệ thống tự chọn CTKM có % cao nhất mà đơn đạt ngưỡng. Khách không cần nhập mã (xem Q7).
- **BR-PROMO-05** Ngưỡng giá trị tối thiểu của KM hóa đơn và voucher xét trên **tạm tính gốc G**.
- **BR-PROMO-06** Mỗi đơn nhập tối đa 1 voucher. Voucher hết hạn, hết lượt hoặc chưa đạt ngưỡng bị từ chối kèm lý do.
- **BR-PROMO-07** Khi nhiều phương án cho cùng số tiền giảm, thứ tự ưu tiên là: theo sản phẩm → theo hóa đơn → voucher, để không tiêu tốn lượt voucher của khách **[GĐ]**.
- **BR-PROMO-08** Voucher chỉ bị trừ lượt khi phương án voucher **được chọn** và đơn được tạo. Đơn bị hủy khi còn "Chưa xử lý" được hoàn lại lượt **[GĐ]**.
- **BR-PROMO-09** Giá "Sale" hiển thị trên thẻ sản phẩm và trang Outlet là giá sau KM theo sản phẩm. Vì hệ thống luôn chọn phương án giảm nhiều nhất, khách không bao giờ trả nhiều hơn tổng giá Sale đã thấy.
- **BR-PROMO-10** Số tiền giảm D làm tròn đến đơn vị đồng **[GĐ]**.
- **BR-PROMO-11** Phương án được chọn tính lại mỗi khi giỏ hàng, voucher hoặc thời điểm thanh toán thay đổi, và chốt tại thời điểm tạo đơn.

### 8.5 Lưu vết khuyến mãi tại thời điểm đặt [PV]
Đơn hàng và chi tiết đơn lưu **ảnh chụp** mọi giá trị dùng để tính tiền. Sau đó thay đổi giá bán, sửa hoặc tắt CTKM đều không làm thay đổi đơn cũ, hóa đơn và số liệu thống kê.

| Nơi lưu | Trường |
|---|---|
| CT_DON_HANG | Mã biến thể, số lượng, đơn giá tại thời điểm đặt, mã CTKM sản phẩm đủ điều kiện, % giảm sản phẩm tại thời điểm đặt, cờ "đã áp dụng", thành tiền sau giảm của dòng |
| DON_HANG | Tạm tính gốc G; loại khuyến mãi được áp (Không / Sản phẩm / Hóa đơn / Voucher); mã CTKM được áp; mã voucher (nếu có); % giảm được áp (với hóa đơn/voucher); số tiền giảm D; phí vận chuyển; tổng thanh toán |
| HOA_DON / CT_HOA_DON | Sao chép nguyên các trường trên khi xuất hóa đơn |

Lưu cả % giảm sản phẩm của từng dòng ngay cả khi phương án sản phẩm không được chọn, để phục vụ đối soát và báo cáo hiệu quả khuyến mãi.

## 9. Kho và phiếu nhập [PV]

- **BR-KHO-01** Hệ thống có 1 kho; tồn kho quản lý theo biến thể.
- **BR-KHO-02** Phiếu nhập gồm: mã phiếu, ngày lập, nhà cung cấp, người lập, trạng thái (Nháp / Đã xác nhận), chi tiết (biến thể, số lượng, giá nhập).
- **BR-KHO-03** Phiếu ở trạng thái Nháp được thêm/sửa/xóa tự do và chưa ảnh hưởng tồn kho. Khi xác nhận, số lượng được cộng vào tồn kho.
- **BR-KHO-04** **Bỏ phiếu điều chỉnh** so với SoT vì cửa hàng chỉ có 1 quản lý. Quản lý được sửa số lượng trên phiếu đã xác nhận bất kỳ lúc nào, với điều kiện tồn kho sau khi sửa không âm. Diễn giải BA: số lượng mới hợp lệ khi `0 ≤ SL mới` và `SL cũ − SL mới ≤ tồn kho hiện tại` (xem Q5).
- **BR-KHO-05** Tồn kho bị trừ **ngay khi khách đặt hàng** **[PV]**. Việc kiểm tra và trừ kho phải nằm trong cùng một giao dịch để hai khách không cùng mua được món cuối cùng.
- **BR-KHO-06** Đơn bị khách hủy ở trạng thái "Chưa xử lý": cộng lại tồn kho **[GĐ]** (xem Q1).
- **BR-KHO-07** Đơn "Giao không thành công": **không** tự cộng lại tồn kho **[PV]**.
- **Khuyến nghị:** lưu nhật ký mỗi lần sửa phiếu nhập (ai sửa, lúc nào, giá trị cũ/mới) để truy vết khi tính lợi nhuận; không bắt buộc.

## 10. Đơn hàng, thanh toán, vận chuyển, hóa đơn

### 10.1 Thông tin đơn hàng [SoT][PV]
Mã đơn, khách hàng, người nhận, địa chỉ, SĐT người nhận; thông tin người gửi (cửa hàng, địa chỉ, SĐT); phương thức thanh toán, trạng thái thanh toán; mã vận đơn GHTK; các trường khuyến mãi và tiền theo mục 8.5; lịch sử trạng thái.

### 10.2 Vòng đời đơn hàng
| Từ trạng thái | Sự kiện | Sang trạng thái | Tác nhân | Ảnh hưởng |
|---|---|---|---|---|
| (mới) | Khách đặt hàng | Chưa xử lý | Khách hàng | Trừ tồn kho |
| Chưa xử lý | Khách hủy | Đã hủy | Khách hàng | Cộng lại tồn kho **[GĐ]** |
| Chưa xử lý | Quản lý xác nhận | Đã xử lý | Quản lý | Tạo vận đơn trên GHTK mock |
| Đã xử lý | Webhook: đã lấy hàng | Đang vận chuyển | GHTK mock | — |
| Đang vận chuyển | Webhook: giao thành công | Đã giao | GHTK mock | Tự động xuất hóa đơn |
| Đang vận chuyển | Webhook: giao thất bại | Giao không thành công | GHTK mock | Không cộng lại kho |

- **BR-DH-01** Sau khi quản lý xác nhận, khách không thể hủy; muốn hủy chỉ có cách từ chối nhận hàng **[PV]**.
- **BR-DH-02** Mọi chuyển trạng thái ngoài bảng trên đều bị từ chối.
- **BR-DH-03** Đơn VietQR chỉ được quản lý xác nhận khi trạng thái thanh toán là "Đã thanh toán" **[GĐ]**.

### 10.3 Thanh toán [PV]
- COD: thanh toán khi nhận hàng.
- VietQR: hệ thống sinh mã QR theo chuẩn VietQR với số tiền và nội dung là mã đơn. Do không có tài khoản ngân hàng thật, việc báo "đã nhận tiền" được mô phỏng bằng một webhook mock **[GĐ]**.
- Hoàn tiền cho đơn VietQR bị hủy hoặc giao thất bại: xử lý thủ công ngoài hệ thống **[GĐ]** (xem Q2).

### 10.4 Tích hợp GHTK mock [PV]
- Nhóm xây một website đơn giản đóng vai GHTK: nhận yêu cầu tạo vận đơn, hiển thị danh sách vận đơn, có nút để người dùng đổi trạng thái.
- Khi trạng thái đổi, GHTK mock **gọi webhook** về hệ thống chính (cơ chế hook, không poll) **[PV]**.
- **BR-HOOK-01** Webhook có xác thực bằng khóa bí mật/chữ ký chung **[GĐ]**.
- **BR-HOOK-02** Xử lý idempotent: nhận lại cùng một sự kiện không làm đổi trạng thái hai lần.
- **BR-HOOK-03** Ghi log mọi webhook nhận được, kể cả sự kiện bị từ chối.

### 10.5 Hóa đơn [PV]
- **BR-HD-01** Hóa đơn chỉ được xuất khi đơn chuyển sang "Đã giao".
- **BR-HD-02** Hóa đơn là chứng từ chỉ đọc, sao chép thông tin đơn tại thời điểm xuất; cả khách và quản lý chỉ được xem.

## 11. Thống kê (dashboard) [PV]
| Chỉ số | Nguồn dữ liệu |
|---|---|
| Doanh thu theo ngày/tuần/tháng/khoảng tùy chọn | Hóa đơn |
| Lợi nhuận = Doanh thu − Giá vốn hàng bán | Hóa đơn + giá nhập (phương pháp giá vốn: xem Q4) |
| Số đơn theo trạng thái, tỉ lệ giao không thành công, tỉ lệ hủy | Đơn hàng |
| Top sản phẩm/biến thể bán chạy | Chi tiết hóa đơn |
| Biến thể tồn kho thấp (dưới ngưỡng cấu hình) | Biến thể |
| Hiệu quả khuyến mãi (số đơn và tổng tiền giảm theo CTKM) | Đơn hàng |
| Tổng nhập hàng theo nhà cung cấp | Phiếu nhập |

## 12. Thực thể dữ liệu sơ bộ (đầu vào ERD)

**Tài khoản & phân quyền:** TAI_KHOAN, ROLE, QUYEN, ROLE_QUYEN, DIA_CHI.
**Danh mục & sản phẩm:** DANH_MUC, DONG_SAN_PHAM, BO_SUU_TAP, SAN_PHAM_BO_SUU_TAP, CHAT_LIEU, MAU_SAC, KICH_THUOC, SAN_PHAM, BIEN_THE, HINH_ANH, BANG_SIZE, TAG.
**Kho:** NHA_CUNG_CAP, PHIEU_NHAP, CT_PHIEU_NHAP, (tùy chọn) NHAT_KY_SUA_PHIEU.
**Mua hàng:** GIO_HANG, CT_GIO_HANG, YEU_THICH, DON_HANG, CT_DON_HANG, LICH_SU_TRANG_THAI, THANH_TOAN, VAN_DON, NHAT_KY_WEBHOOK, HOA_DON, CT_HOA_DON.
**Khuyến mãi:** CHUONG_TRINH_KM, KM_SAN_PHAM, KM_HOA_DON, KM_VOUCHER.
**Nội dung:** DANH_GIA, BAI_VIET, BANNER, TRANG_TINH, ANH_CONG_DONG, GOP_Y, CAU_HINH (thanh thông báo, từ khóa hot, hotline, link Zalo, ngưỡng tồn kho thấp).

## 13. Yêu cầu phi chức năng
| Mã | Yêu cầu |
|---|---|
| NFR-01 | Backend Python (đề xuất FastAPI hoặc Django REST Framework), frontend React **[PV]**; CSDL quan hệ (đề xuất PostgreSQL hoặc MySQL) **[GĐ]** |
| NFR-02 | Giao diện tiếng Việt, tiền tệ VND định dạng `450,000₫` |
| NFR-03 | Responsive, ưu tiên mobile |
| NFR-04 | Mật khẩu băm (bcrypt/argon2); xác thực bằng JWT hoặc session; HTTPS khi triển khai |
| NFR-05 | Mọi API kiểm tra quyền theo RBAC |
| NFR-06 | Thao tác trừ kho và tạo đơn chạy trong một transaction |
| NFR-07 | Trang danh mục và chi tiết sản phẩm tải dưới 3 giây với dữ liệu mẫu ~500 biến thể **[GĐ]** |
| NFR-08 | Có dữ liệu mẫu (seed) đủ để demo mọi luồng |

## 14. Rủi ro
| Rủi ro | Ảnh hưởng | Giảm thiểu |
|---|---|---|
| Phạm vi MVP rộng so với 5 người và thời lượng môn học | Trễ hạn, chất lượng tài liệu giảm | Áp dụng MoSCoW ở 3.4, làm Must trước |
| Không cộng lại kho khi giao thất bại | Tồn kho trên hệ thống thấp hơn thực tế | Quản lý nhập lại hàng hoàn qua phiếu nhập (Q6) |
| Sửa phiếu nhập đã xác nhận không có lịch sử | Khó giải thích lợi nhuận, khó chấm điểm phần truy vết | Thêm nhật ký sửa phiếu |
| Sao chép quá sát Sobie | Vấn đề bản quyền | Chỉ sao chép cấu trúc, tự tạo thương hiệu và nội dung |

## 15. Câu hỏi mở cần Originator xác nhận
| Mã | Câu hỏi | Đề xuất của BA |
|---|---|---|
| Q1 | Khách hủy đơn khi còn "Chưa xử lý" có cộng lại tồn kho không? | Có, vì kho đã bị trừ lúc đặt |
| Q2 | Đơn VietQR đã thanh toán nhưng bị hủy/giao thất bại thì hoàn tiền thế nào? Đơn VietQR chưa thanh toán bao lâu thì tự hủy? | Hoàn tiền thủ công ngoài hệ thống; tự hủy sau 24 giờ và cộng lại kho |
| Q3 | Có tính phí vận chuyển không? Có làm freeship theo ngưỡng và quà tặng theo bậc đơn như Sobie không (mô hình KM hiện chỉ có %)? | MVP: phí ship cố định cấu hình được, cộng sau khuyến mãi; thanh thông báo chỉ hiển thị chữ; freeship và quà tặng để giai đoạn 2 |
| Q4 | Giá vốn tính theo phương pháp nào khi một biến thể có nhiều lần nhập giá khác nhau? | Bình quân gia quyền |
| Q5 | Diễn giải "0 ≤ số lượng ≤ tồn kho" ở BR-KHO-04 có đúng ý không? | Như BR-KHO-04 |
| Q6 | Hàng hoàn về sau giao thất bại có được nhập lại kho không, bằng cách nào? | Tạo phiếu nhập với nhà cung cấp đặc biệt "Hàng hoàn" |
| Q7 | "Khuyến mãi theo hóa đơn (có mã hóa)" nghĩa là KM hóa đơn tự áp theo giá trị đơn, hay khách phải nhập mã? | Tự áp theo ngưỡng giá trị; chỉ voucher mới cần nhập mã |
| Q8 | Khách vãng lai có được đặt hàng không cần tài khoản không? | Không, bắt buộc đăng nhập (đúng với SoT) |
| Q9 | Quản lý có được từ chối đơn ở trạng thái "Chưa xử lý" không (ví dụ thông tin giả)? | Có, chuyển sang "Đã hủy" kèm lý do và cộng lại kho |

## 16. Thuật ngữ
| Thuật ngữ | Giải thích |
|---|---|
| Biến thể | Một tổ hợp size × màu của sản phẩm, đơn vị quản lý tồn kho |
| CTKM | Chương trình khuyến mãi |
| RBAC | Phân quyền dựa trên vai trò |
| Webhook | Hệ thống ngoài chủ động gọi API của hệ thống chính khi có sự kiện |
| GHTK mock | Website mô phỏng đơn vị vận chuyển Giao Hàng Tiết Kiệm |
| Xóa mềm | Đánh dấu đã xóa thay vì xóa khỏi CSDL |
