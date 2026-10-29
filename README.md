# soobin-swimwear-e-commerce

## Cấu trúc thư mục

Project hiện được tổ chức theo tầng kỹ thuật: router, service, repository và database. Các file ứng dụng và hạ tầng hiện vẫn là khung trống; mô tả bên dưới là trách nhiệm dự kiến của từng mục.

```text
soobin-swimwear-e-commerce/
├── .agents/                         # Cấu hình, tài nguyên dành cho coding agent
├── app/                             # Mã nguồn ứng dụng
│   ├── main.py                      # Điểm khởi tạo ứng dụng, đăng ký router
│   ├── config.py                    # Đọc và quản lý cấu hình ứng dụng
│   ├── routers/                     # Tiếp nhận HTTP request, định nghĩa endpoint
│   │   └── pages.py                 # Các route phục vụ trang HTML
│   ├── services/                    # Xử lý nghiệp vụ
│   │   └── product_services.py      # Nghiệp vụ liên quan đến sản phẩm
│   ├── repositories/                # Truy vấn và ghi dữ liệu
│   │   └── product_reporisity.py    # Truy cập dữ liệu sản phẩm
│   ├── schema/                      # Pydantic model cho dữ liệu đầu vào/đầu ra
│   ├── db/                          # Kết nối và định nghĩa cấu trúc database
│   │   ├── engine.py                # Khởi tạo kết nối, quản lý session/transaction
│   │   └── schema.py                # Định nghĩa bảng hoặc ORM model
│   ├── templates/                   # Template HTML để render trang
│   │   └── base.html                # Layout HTML dùng chung
│   └── static/                      # Tài nguyên tĩnh: CSS, JavaScript, hình ảnh
│       └── css/                     # Stylesheet của giao diện
│           └── New Text Document.txt  # File giữ chỗ, chưa phải stylesheet
├── infra/                           # Cấu hình hạ tầng và môi trường chạy
│   ├── docker-compose.yml           # Khai báo các service và cách kết nối chúng
│   └── postgres/                    # Script dành cho PostgreSQL
│       ├── init.sql                 # Khởi tạo cấu trúc database
│       └── seed.sql                 # Nạp dữ liệu ban đầu hoặc dữ liệu mẫu
├── .env.example                     # Mẫu biến môi trường cần cấu hình
├── AGENTS.md                        # Hướng dẫn làm việc cho coding agent
├── CLAUDE.md                        # Tham chiếu hướng dẫn cho Claude
├── Dockerfile                       # Hướng dẫn đóng gói ứng dụng thành Docker image
├── pyproject.toml                   # Metadata, phiên bản Python, dependency và build
└── README.md                        # Tài liệu tổng quan project
```

Thư mục `app/schema/` hiện chưa có file. Tên `product_reporisity.py` được giữ nguyên theo cấu trúc hiện tại; khi chuẩn hóa tên có thể đổi thành `product_repository.py`.

## Trách nhiệm của các tầng

| Thành phần | Chức năng |
| --- | --- |
| `routers/` | Nhận request, lấy dữ liệu từ URL/query/body, gọi service và trả response hoặc render template. |
| `schema/` | Chứa Pydantic model để kiểm tra dữ liệu và mô tả cấu trúc đầu vào/đầu ra, ví dụ `ProductCreate`, `ProductUpdate`, `ProductResponse`. |
| `services/` | Thực hiện quy tắc nghiệp vụ và điều phối các thao tác qua repository. |
| `repositories/` | Đóng gói thao tác đọc/ghi database, ví dụ tìm sản phẩm, thêm sản phẩm hoặc cập nhật giá. |
| `db/` | Cung cấp kết nối database và định nghĩa bảng/ORM model để lưu trữ dữ liệu. |
| `templates/` | Định nghĩa nội dung HTML; các trang có thể kế thừa layout trong `base.html`. |
| `static/` | Chứa tài nguyên được trình duyệt tải trực tiếp như CSS, JavaScript và hình ảnh. |
| `infra/` | Chứa cấu hình chạy các thành phần hạ tầng và script khởi tạo dữ liệu. |

### Phân biệt Pydantic schema và database schema

- `app/schema/`: cấu trúc dữ liệu trao đổi với ứng dụng. Ví dụ, `ProductCreate` kiểm tra tên và giá khi tạo sản phẩm; `ProductResponse` xác định các trường trả về cho client.
- `app/db/schema.py`: cấu trúc dữ liệu lưu trong database, bao gồm bảng, cột, khóa và quan hệ hoặc các ORM model tương ứng.
- `app/config.py`: cấu hình ứng dụng. Nếu sử dụng `pydantic-settings`, lớp `Settings` có thể đặt tại đây.

### Luồng xử lý dự kiến

```text
HTTP request
    → Router (kiểm tra đầu vào bằng Pydantic schema khi cần)
    → Service (xử lý nghiệp vụ)
    → Repository (truy cập dữ liệu)
    → Database
```

Kết quả đi ngược về router để trả response hoặc render template. Khi trả JSON, router có thể dùng Pydantic schema để kiểm tra và định dạng dữ liệu đầu ra.

## Định hướng modular monolith

Cấu trúc hiện tại chia theo tầng kỹ thuật. Nếu chuyển sang modular monolith, có thể gom từng nghiệp vụ vào `app/modules/`, ví dụ `catalog`, `cart`, `orders`, `customers`. Mỗi module sở hữu router, service, repository, Pydantic schema và database model của mình.

Các module giao tiếp qua interface/service công khai hoặc event, tránh truy cập trực tiếp repository và sửa dữ liệu thuộc module khác. Toàn bộ ứng dụng vẫn có thể chạy trong cùng một tiến trình, triển khai chung và sử dụng chung một database.
