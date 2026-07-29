---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# [AWS Troubleshooting] Xử Lý Triệt Để Lỗi CORS Và Routing Khi Đặt CloudFront Đứng Trước EC2 Backend

Chào mọi người trong AWS Study Group,

Khi xây dựng ứng dụng web với kiến trúc tách biệt: Frontend hosted trên S3 + CloudFront và Backend REST API chạy trên EC2 Instance, một trong những "cơn ác mộng" phổ biến nhất mà các dev hay gặp phải chính là Lỗi CORS (Cross-Origin Resource Sharing) và mất Query Parameters/Headers khi request đi qua CloudFront.

Nhiều bạn thường chọn giải pháp "nhanh gọn" là bật `Access-Control-Allow-Origin: *` ở phía backend. Tuy nhiên, cách này tạo ra lỗ hổng bảo mật nghiêm trọng và chưa chắc đã giải quyết tận gốc vấn đề nếu cấu hình CloudFront Behavior bị sai.

Bài viết này chia sẻ cách nhóm mình phân tích nguyên nhân cốt lõi và cấu hình chuẩn mực trên AWS để xử lý triệt để bài toán này.

## 1. Bản Chất Nguyên Nhân Phát Sinh Lỗi

Khi Client (trình duyệt) gọi API, luồng dữ liệu diễn ra như sau:
* **Client Origin:** [https://app.learnsphere.com](https://app.learnsphere.com) (Phục vụ từ CloudFront + S3)
* **Backend Origin:** [https://api.learnsphere.com](https://api.learnsphere.com) hoặc IP của EC2 Instance.

Trình duyệt sẽ tự động gửi một Preflight Request (HTTP OPTIONS) để hỏi server backend xem domain frontend có được phép truy cập tài nguyên hay không.

**3 "bẫy" cấu hình thường gặp trên AWS:**
* **CloudFront nuốt mất OPTIONS Request:** Mặc định, CloudFront Behavior chỉ cho phép các HTTP Method cơ bản (GET, HEAD). Các request OPTIONS bị chặn ngay tại Edge Location và không bao giờ tới được EC2.
* **CloudFront không Forward Origin Header:** CloudFront mặc định không chuyển header `Origin`, `Access-Control-Request-Method` từ Client về cho EC2 Backend. Kết quả là Backend không biết client đến từ đâu để trả về CORS header tương ứng.
* **Cache nhầm CORS Response:** CloudFront cache lại phản hồi của API. Nếu request đầu tiên từ Client A không có CORS header và bị cache lại, tất cả Client B sau đó cũng sẽ dính lỗi CORS do nhận lại bản cache lỗi này.

## 2. Giải Pháp Cấu Hình Chuẩn Trên AWS CloudFront

Để xử lý tận gốc mà không cần hạ thấp tiêu chuẩn bảo mật, cấu hình CloudFront Behavior dành riêng cho path `/api/*` (trỏ về EC2 Origin) cần được thiết lập đúng các thông số sau:

**Bước 1: Cho phép đầy đủ HTTP Methods**
Trong mục Allowed HTTP Methods, chuyển từ `GET, HEAD` sang `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`.
Điều này đảm bảo CloudFront chuyển tiếp toàn bộ Preflight request về cho EC2 xử lý.

**Bước 2: Bật Cấu Hình Forward Headers (Origin & Authorization)**
Tại mục Cache Key and Origin Requests, chọn cấu hình Custom / Cache Policy:
Thêm các Header bắt buộc phải Forward gồm: `Origin`, `Access-Control-Request-Method`, `Access-Control-Request-Headers`, và `Authorization` (nếu dùng Bearer Token).
Việc này giúp EC2 Backend nhận diện chính xác domain nguồn và phản hồi đúng chuỗi `Access-Control-Allow-Origin`.

**Bước 3: Sử dụng Response Headers Policy (Khuyên Dùng)**
Thay vì xử lý CORS phức tạp ở từng đoạn code trong ứng dụng trên EC2, AWS cung cấp tính năng Response Headers Policy ngay tại CloudFront:
* Bạn tạo một CORS Response Headers Policy trong CloudFront Console.
* Định nghĩa rõ ràng:
  * `Allow-Origin`: Chỉ điền domain chính thức của Frontend.
  * `Allow-Credentials`: `true` (nếu dùng Cookie/Session).
  * `Allow-Headers` & `Allow-Methods`: Tương ứng với nhu cầu hệ thống.
* Gán Policy này vào Behavior của CloudFront. CloudFront sẽ tự động đính kèm các header CORS chuẩn mực vào mọi phản hồi trả về cho Client.

## 3. Cấu Hình Tối Ưu Security Group Cho EC2

Sau khi CloudFront đã xử lý chuẩn luồng traffic, bước tiếp theo là bảo mật cho EC2 Backend:
* **Không mở Public 0.0.0.0/0 tràn lan:** Chỉ mở port HTTP/HTTPS cho các dải IP đại diện của CloudFront (sử dụng AWS Managed Prefix List cho CloudFront) hoặc chỉ nhận traffic thông qua Application Load Balancer (ALB).
* **Đảm bảo Health Check:** Cấu hình đường dẫn `/health` hoặc `/ping` trên EC2 để CloudFront/ALB kiểm tra trạng thái hoạt động của container.

## 4. Kết Quả Mang Lại

* **Xử lý dứt điểm 100% lỗi CORS:** Hệ thống chạy mượt mà trên mọi trình duyệt mà không cần tắt các chế độ bảo mật khắt khe.
* **Tối ưu khả năng Caching:** Chỉ cache các dữ liệu cần thiết, tránh tình trạng cache sai response header của API.
* **Tăng cường Security:** Loại bỏ việc dùng wildcard `*` cho CORS Origin, bảo vệ API khỏi các truy cập trái phép từ các domain lạ.

![Blog 2](/images/blog2.png)

---

🔗 **Nguồn bài viết trên Facebook:** [AWS Study Group Post](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2227706841327609/#)