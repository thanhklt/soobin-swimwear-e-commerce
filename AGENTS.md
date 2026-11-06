# Agent Rules

## 1. Nguyên tắc cốt lõi (Core Rules)
- **Không tự ý git commit hoặc git push**: Tuyệt đối không tự ý push hay commit code trừ khi có yêu cầu rõ ràng từ developer.
- **Source of Truth**: Luôn đọc từ `source_of_true` mỗi khi bắt đầu phiên mới: https://drive.google.com/drive/folders/1BuL3ev8ow3pxkLeJFljE3wbwOz4VO-Aa?usp=sharing
- **Xử lý lệch thông tin**: Khi quyết định hiện tại lệch với thông tin từ `source_of_true`, hãy báo cho developer biết để quyết định.
- **Xử lý vấn đề mơ hồ**: Khi gặp vấn đề mơ hồ, không tự ý giả định mà hãy báo cho developer biết để chờ developer quyết định.
- **Quy định gắn tag giả định [GD]**: Mọi thông tin, luồng xử lý hoặc quyết định mà agent giả định bắt buộc phải được gắn tag `[GD]` và tổng hợp đưa vào danh sách **câu hỏi mở** để chờ developer quyết định.

## 2. Kiến trúc & Phân tầng (Architecture & Boundaries)
- **Tuân thủ nghiêm ngặt 3 tầng (3-Layer Pattern)**: Luôn đi theo luồng `Route` → `Service` → `Repository` → `Database`. Tuyệt đối không gọi tắt (Route không gọi trực tiếp Repository hoặc DB).
- **Ranh giới Modular Monolith**: Các module nghiệp vụ (catalog, cart, orders,...) giao tiếp với nhau qua Service/Interface công khai; không truy cập trực tiếp Repository hoặc database table của module khác.

## 3. Quy ước Response API Envelope & Dữ liệu
- **Response Wrapper Envelope**: Mọi API response phải được đóng gói theo định dạng Envelope thống nhất gồm 4 trường: `success`, `data`, `error`, `meta`.
  
  ### 3.1. Phản hồi thành công có phân trang (Danh sách)
  Khi trả về danh sách bản ghi, dữ liệu nằm trong mảng `data`, và thông tin phân trang nằm trong `meta.pagination`:
  ```json
  {
    "success": true,
    "data": [
      {
        "ma_mat_hang": "MH003",
        "ten_mat_hang": "Khung xe đạp"
      },
      {
        "ma_mat_hang": "MH004",
        "ten_mat_hang": "Bánh xe đạp"
      }
    ],
    "error": null,
    "meta": {
      "timestamp": "2026-04-04T10:00:00Z",
      "request_id": "req_abc123",
      "pagination": {
        "page": 2,
        "page_size": 2,
        "total_items": 9,
        "total_pages": 5,
        "has_next": true,
        "has_previous": true
      }
    }
  }
  ```

  **Chi tiết các trường trong `meta.pagination`:**

  | Trường | Ý nghĩa |
  |---|---|
  | `page` | Trang hiện tại, bắt đầu từ 1 |
  | `page_size` | Số bản ghi tối đa mỗi trang |
  | `total_items` | Tổng số bản ghi thỏa điều kiện lọc |
  | `total_pages` | Tổng số trang: làm tròn lên `total_items / page_size` |
  | `has_next` | Có trang tiếp theo hay không |
  | `has_previous` | Có trang trước hay không |

  ### 3.2. Phản hồi thành công không phân trang (Bản ghi đơn lẻ)
  Đối với API trả về chi tiết đối tượng đơn lẻ hoặc các thao tác không phân trang, `meta.pagination` là `null`:
  ```json
  {
    "success": true,
    "data": {
      "ma_mat_hang": "MH001",
      "ten_mat_hang": "Xe đạp XBike",
      "so_luong_ton": 25
    },
    "error": null,
    "meta": {
      "timestamp": "2026-04-04T10:00:00Z",
      "request_id": "req_abc123",
      "pagination": null
    }
  }
  ```

  ### 3.3. Phản hồi thất bại
  ```json
  {
    "success": false,
    "data": null,
    "error": {
      "code": "ITEM_NOT_FOUND",
      "message": "Không tìm thấy mặt hàng.",
      "details": {
        "ma_mat_hang": "MH999"
      }
    },
    "meta": {
      "timestamp": "2026-04-04T10:00:00Z",
      "request_id": "req_def456"
    }
  }
  ```

- **Tiền tệ & Số học tài chính**: Tiền tệ luôn biểu diễn bằng số nguyên VND hoặc kiểu `Decimal` trong Python, làm tròn theo `ROUND_HALF_UP`. Tuyệt đối không dùng `float` để tránh sai số tính toán đơn hàng / khuyến mãi.
- **Thời gian & Múi giờ**: Lưu trữ và xử lý thời gian trong DB luôn ở dạng UTC (`timestamptz`). Chỉ định dạng sang múi giờ `Asia/Ho_Chi_Minh` khi xuất báo cáo hoặc hiển thị lên giao diện.

## 4. Kiểm thử & Toàn vẹn dữ liệu (Testing & Data Integrity)
- **Kiểm thử trước khi nghiệm thu**: Mọi thay đổi logic nghiệp vụ mới phải đi kèm test (`pytest`). Trước khi báo hoàn thành task, agent phải chạy test và linter liên quan để xác nhận không phát sinh lỗi.
- **Toàn vẹn Transaction**: Các thao tác nhạy cảm như trừ tồn kho, kiểm tra/áp mã giảm giá và tạo đơn hàng phải luôn chạy trong cùng một Database Transaction để chống race condition.

## 5. Bảo mật & Quản lý Source Code
- **Bảo vệ Secret & Cấu hình**: Không bao giờ hardcode credentials, tokens, secrets vào mã nguồn. Luôn nạp qua biến môi trường (`.env`, `pydantic-settings`).
- **Phạm vi tác động tối thiểu**: Chỉ chỉnh sửa đúng phạm vi task được yêu cầu; bảo toàn comments, docstrings và cấu trúc code có sẵn không liên quan.
