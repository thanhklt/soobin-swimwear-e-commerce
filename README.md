# Sobie — thương mại điện tử đồ bơi

Dự án được tổ chức theo **modular monolith**: một ứng dụng chính gồm 12 module nghiệp vụ. GHTK mock là ứng dụng mô phỏng bên ngoài, có thư mục và Dockerfile riêng.

Hiện tại repo **chỉ có khung thư mục/file**, chưa có ứng dụng chạy được, API, kết nối database, tích hợp GHTK hay logic nghiệp vụ. Các file dependency, Docker và SQL chưa có cấu hình thực thi.

## Cấu trúc thư mục

```text
soobin-swimwear-e-commerce/          # Repo của ứng dụng Sobie
├── app/
│   ├── __init__.py
│   ├── main.py                     # Khởi tạo ứng dụng, đăng ký router
│   ├── shared/                     # Thành phần kỹ thuật dùng chung
│   │   ├── config/
│   │   │   ├── config.yml
│   │   │   └── settings.py
│   │   ├── database/
│   │   │   └── engine.py            # Engine, session, transaction
│   │   ├── security/               # Tiện ích bảo mật
│   │   ├── events/                 # Hạ tầng event nội bộ
│   │   └── web/
│   │       ├── templates/
│   │       │   └── base.html
│   │       └── static/
│   │           └── css/
│   └── modules/
│       ├── iam/
│       ├── customer/
│       ├── catalog/
│       ├── inventory/
│       ├── promotion/
│       ├── cart/
│       ├── ordering/
│       ├── payment/
│       ├── shipping/
│       ├── invoice/
│       ├── content/
│       └── reporting/
├── db/
│   └── init/
│       ├── 01_schema.sql           # Chờ bổ sung từ sobie.sql đã sửa
│       └── 02_seed.sql             # Chờ dữ liệu mẫu, mật khẩu băm sẵn
├── ghtk_mock/
│   ├── __init__.py
│   ├── main.py
│   ├── templates/
│   ├── requirements.txt
│   └── Dockerfile
├── infra/                          # Giữ lại cho hạ tầng PostgreSQL, Redis
│   ├── docker-compose.yml
│   └── postgres/
│       ├── init.sql
│       └── seed.sql
├── tests/
├── Dockerfile                      # Khung image ứng dụng chính
├── docker-compose.yml              # Khung Compose tổng thể
├── requirements.txt                # Khung dependency ứng dụng chính
├── pyproject.toml                  # Metadata Python hiện có, giữ nguyên
├── .env.example
├── .dockerignore
├── .gitignore
├── AGENTS.md
├── CLAUDE.md
└── README.md
```

Các package Python có `__init__.py`. Những thư mục chưa có nội dung dùng `.gitkeep` để được giữ trong Git.

## Cấu trúc mỗi module

```text
app/modules/<module>/
├── __init__.py
├── router.py
├── service.py
├── repository.py
├── schemas.py
└── models.py
```

| File | Trách nhiệm |
| --- | --- |
| `router.py` | Nhận request, kiểm tra dữ liệu và gọi service. |
| `service.py` | Chứa nghiệp vụ, điều phối transaction và cung cấp service công khai cho module khác. |
| `repository.py` | Đóng gói truy vấn dữ liệu do module sở hữu. |
| `schemas.py` | Schema đầu vào/đầu ra, tách biệt với model lưu trữ. |
| `models.py` | Model dữ liệu của module; với reporting là model kết quả chỉ đọc. |

Luồng xử lý bắt buộc: `Route → Service → Repository → Database`. Route không gọi trực tiếp repository hoặc database.

## Phạm vi 12 module

| Module | Phạm vi |
| --- | --- |
| `iam` | Tài khoản, role và quyền. |
| `customer` | Hồ sơ khách hàng, sổ địa chỉ và wishlist. |
| `catalog` | Sản phẩm, biến thể, danh mục và thuộc tính. |
| `inventory` | Nhà cung cấp, phiếu nhập và tồn kho. |
| `promotion` | Chương trình khuyến mãi và lựa chọn khuyến mãi tốt nhất. |
| `cart` | Giỏ hàng. |
| `ordering` | Đặt hàng và trạng thái đơn. |
| `payment` | VietQR và webhook thanh toán. |
| `shipping` | Vận đơn, tích hợp GHTK và webhook vận chuyển. |
| `invoice` | Hóa đơn. |
| `content` | Đánh giá, blog, banner, trang tĩnh, cộng đồng, góp ý và cấu hình nội dung. |
| `reporting` | Dashboard thống kê, chỉ đọc. |

Các module chỉ giao tiếp qua service/interface công khai; không import repository, model lưu trữ hoặc truy cập bảng của module khác. Không đưa nghiệp vụ vào `shared/`. Module reporting lấy dữ liệu qua service/interface chỉ đọc do module sở hữu công khai, không ghi dữ liệu nghiệp vụ và không truy vấn trực tiếp bảng của module khác.

Cấu hình kỹ thuật trong `shared/config/` khác với cấu hình nội dung website do module `content` quản lý. Tiện ích bảo mật trong `shared/security/` khác với nghiệp vụ tài khoản, role và quyền của `iam`.

## Database và hạ tầng

- `db/init/01_schema.sql` dành cho schema từ `sobie.sql` đã sửa; `02_seed.sql` dành cho dữ liệu mẫu với mật khẩu đã băm. Hai file hiện chỉ có chú thích, chưa tạo bảng hoặc tài khoản.
- Khi triển khai PostgreSQL, mount `db/init/` vào `/docker-entrypoint-initdb.d/` để chạy script theo thứ tự tên file trên volume dữ liệu mới. Cấu hình mount chưa được triển khai.
- Script khởi tạo không tự chạy lại khi volume đã có database; không xóa volume có dữ liệu để áp dụng thay đổi schema.
- Giữ nguyên `infra/` theo yêu cầu để cấu hình PostgreSQL và Redis về sau. Các file cũ trong `infra/` hiện vẫn rỗng. Không duy trì bản sao schema/seed tại hai nơi; `db/init/` là vị trí của bộ SQL trong cấu trúc mới.
- Compose ở gốc dành cho hệ thống tổng thể; Compose trong `infra/` dành cho hạ tầng. Cả hai hiện chưa khai báo service, chưa thể dùng để khởi động hệ thống.

Khi triển khai nghiệp vụ, mọi API dùng envelope `success`, `data`, `error`, `meta`; tiền dùng số nguyên VND hoặc `Decimal` với `ROUND_HALF_UP`; thời gian DB dùng UTC. Kiểm tra/trừ tồn, áp khuyến mãi và tạo đơn phải dùng cùng transaction, kể cả khi đi qua nhiều service. Secret được nạp từ biến môi trường.

## Trạng thái và phần còn chờ

Đã có khung module và vị trí dành cho hạ tầng, mock, SQL, template và kiểm thử. Chưa khai báo dependency, chưa có lệnh chạy ứng dụng hoặc bộ test nghiệp vụ. `pyproject.toml` được giữ nguyên; các file `requirements.txt` chưa khai báo thư viện.

Câu hỏi mở cần bổ sung trước khi triển khai:

- Cung cấp bản `sobie.sql` đã sửa để điền `01_schema.sql`; file này chưa có trong repo.
- Xác định bộ dữ liệu mẫu và cơ chế băm mật khẩu để điền `02_seed.sql`.

Nguồn yêu cầu: [source_of_true](https://drive.google.com/drive/folders/1BuL3ev8ow3pxkLeJFljE3wbwOz4VO-Aa). Cấu trúc 12 module và việc giữ `infra/` theo xác nhận của developer trong phiên làm việc này. Những khác biệt nghiệp vụ giữa SRS và PRD cần được chốt khi triển khai từng tính năng.