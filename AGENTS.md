# AGENTS.md

## Lệnh

## Quy tắc

1. Đầu mỗi phiên làm việc, kiểm tra danh mục tài liệu trong [nguồn thông tin chuẩn (source of truth)](https://drive.google.com/drive/folders/1BuL3ev8ow3pxkLeJFljE3wbwOz4VO-Aa?usp=sharing) và đọc các tài liệu liên quan đến nhiệm vụ trước khi thực hiện công việc. Nếu không truy cập được nguồn này, báo cho dev và yêu cầu cung cấp quyền truy cập hoặc nội dung cần thiết; không tự suy đoán.
2. Nếu phát hiện code hiện tại khác với nguồn thông tin chuẩn, phải báo ngay cho dev, nêu rõ điểm khác biệt trước khi tiếp tục xử lý phần liên quan.
3. Không tự ý commit hoặc push. Chỉ thực hiện khi dev yêu cầu rõ ràng.
4. Khi không đủ ngữ cảnh hoặc yêu cầu chưa rõ, phải hỏi lại dev thay vì cố gắng dự đoán.
5. Kiểm soát phạm vi: Chỉ thay đổi những phần cần thiết cho yêu cầu. Nếu cần refactor lớn, thêm dependency hoặc thay đổi kiến trúc, phải trình bày lý do và hỏi dev trước.
6. Xử lý mâu thuẫn: Khi code, tài liệu hoặc yêu cầu có mâu thuẫn, nêu rõ các điểm khác nhau và hỏi dev xác nhận hướng xử lý trước khi sửa phần liên quan.
7. Kiểm chứng trước khi báo hoàn thành: Chạy các kiểm tra phù hợp với thay đổi. Khi bàn giao, nêu rõ đã sửa gì, đã kiểm tra gì và còn vấn đề nào chưa giải quyết. Nếu chưa chạy được kiểm tra, phải nói rõ.

## Một số lỗi cần tránh

1. Khi xảy ra lỗi hoặc test thất bại, không sửa test chỉ để làm cho test pass. Phải tìm và xử lý nguyên nhân gốc rễ của lỗi.
2. Không che giấu lỗi bằng cách bọc `try-catch`, nuốt exception hoặc bỏ qua lỗi. Nếu cần bắt lỗi, phải xử lý rõ ràng và bảo đảm lỗi vẫn được ghi nhận hoặc truyền tiếp phù hợp.
