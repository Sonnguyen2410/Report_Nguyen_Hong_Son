---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# [AWS Architecture] Giải Pháp Serverless Caching Với Amazon ElastiCache: Tối Ưu Chi Phí & Tăng Tốc Phản Hồi Cho AI Platform

Chào mọi người trong AWS Study Group,

Khi xây dựng ứng dụng tích hợp AI (như Groq API hay OpenAI), thách thức lớn nhất của các đội ngũ kỹ thuật khi mở rộng quy mô (scale) không nằm ở hạ tầng EC2, mà nằm ở chi phí gọi API bên ngoài và độ trễ (latency) khi chờ Model sinh ra câu trả lời.

Một request gọi sang Groq API có thể mất từ 800ms đến 3000ms tùy thuộc vào độ dài của prompt và token sinh ra. Nếu ứng dụng có hàng nghìn lượt truy cập mỗi ngày với nhiều câu hỏi mang tính lặp lại (như câu hỏi trắc nghiệm, giải thích thuật ngữ, hướng dẫn học tập), hệ thống sẽ tốn rất nhiều ngân sách và làm giảm đáng kể trải nghiệm người dùng.

Để giải quyết bài toán này, nhóm mình đã nghiên cứu phương án tối ưu kiến trúc bằng cách đưa lớp Serverless Caching vào hệ thống.

## 1. Phân Tích Luồng Dữ Liệu Hiện Tại & Điểm Đốt Ngân Sách

Trong sơ đồ kiến trúc hiện tại của dự án:
* **Traffic Flow:** User -> CloudFront -> S3 (Frontend) & EC2 Instance (Backend).
* **AI Execution:** EC2 Backend xử lý logic và gọi trực tiếp sang Groq API.

**Vấn đề gặp phải:**
* **Lãng phí tài nguyên (Redundant Calls):** Nếu 500 học viên cùng mở bài tập và yêu cầu AI giải thích một khái niệm, EC2 sẽ gửi 500 HTTP requests riêng biệt tới Groq API cho cùng một nội dung.
* **Nút thắt cổ chai về độ trễ (Latency Bottleneck):** Người dùng luôn phải chờ vài giây cho mỗi tương tác, dù hệ thống backend trên AWS chạy rất nhanh.
* **Rủi ro dính Rate Limit:** Các nhà cung cấp AI API luôn áp đặt giới hạn TPM (Tokens Per Minute) và RPM (Requests Per Minute). Việc gọi liên tục dễ khiến ứng dụng dính lỗi 429 Too Many Requests.

## 2. Giải Pháp Kiến Trúc: Tích Hợp Amazon ElastiCache for Redis (Serverless)

Phương án kiến trúc tối ưu là bổ sung Amazon ElastiCache for Redis đóng vai trò In-Memory Cache đứng trước lớp AI Service.

**Tại sao chọn cấu hình Serverless?**
* **Không cần quản lý cụm Cluster:** Tự động co giãn (auto-scaling) dung lượng RAM và throughput theo lưu lượng thực tế.
* **Tối ưu chi phí:** Chỉ trả tiền cho dung lượng lưu trữ (GB-hour) và dữ liệu truy xuất (ECU/read-write unit), phù hợp cho các dự án phát triển từ quy mô nhỏ lên lớn.
* **Low Latency:** Thời gian phản hồi nằm ở mức microsecond/millisecond do dữ liệu nằm hoàn toàn trên RAM.

## 3. Cơ Chế Xử Lý Luồng Dữ Liệu (Cache-Aside Strategy)

Ứng dụng mẫu chiến lược Cache-Aside (Lazy Loading) với luồng xử lý chi tiết từng bước:

**Luồng xử lý kỹ thuật:**
1. **Chuẩn hóa & Tạo Mã Nhận Diện (Key Generation):**
   Khi EC2 Backend nhận yêu cầu từ người dùng, hệ thống chuẩn hóa chuỗi văn bản (viết thường, loại bỏ khoảng trắng thừa) và tạo ra một chuỗi mã hóa định danh (Hash Key) duy nhất cho prompt đó.
2. **Kiểm Tra Trạng Thái Cache (Cache Hit / Miss):**
   * **Cache Hit (Đã có trong Cache):** Nếu tìm thấy kết quả tương ứng với Mã Nhận Diện trong ElastiCache, EC2 Backend lấy trực tiếp dữ liệu từ RAM và trả về cho người dùng ngay lập tức. Thời gian xử lý < 10ms.
   * **Cache Miss (Chưa có trong Cache):** Nếu không tìm thấy, EC2 Backend mới tiến hành khởi tạo kết nối gọi sang Groq API để xử lý.
3. **Lưu Trữ & Đặt Thời Gian Hết Hạn (TTL):**
   Sau khi nhận câu trả lời từ Groq API, EC2 Backend đồng thời lưu kết quả này vào ElastiCache kèm thời gian sống (TTL - Ví dụ: 24 giờ) trước khi phản hồi về phía người dùng cuối.

## 4. Đánh Giá Hiệu Quả Mang Lại

* **Tốc độ (Latency):** Giảm thời gian phản hồi cho các câu hỏi phổ biến từ ~1.5s - 2.5s xuống còn ~5ms - 15ms (tăng tốc gấp gần 100 lần).
* **Chi phí (Cost Efficiency):** Tiết kiệm đến 40% - 60% lượng token gọi sang Groq API trong các đợt cao điểm học tập.
* **Tính sẵn sàng (High Availability):** Nếu dịch vụ Groq API bị chập chờn hoặc chạm ngưỡng Rate Limit, hệ thống vẫn phục vụ tốt các nội dung đã được cache từ trước mà không ảnh hưởng đến trải nghiệm học viên.

## 5. Chiến Lược Nâng Cấp Tiếp Theo: Semantic Caching

Nếu Exact-match Caching (khớp chính xác chuỗi prompt) chỉ giải quyết được các câu hỏi giống hệt nhau, bước nâng cấp tiếp theo của nhóm là nghiên cứu Semantic Caching (Cache theo ngữ nghĩa):
* Sử dụng Vector Embeddings để đo độ tương đồng giữa câu hỏi mới và câu hỏi đã lưu trong cache.
* Nếu hai câu hỏi có độ tương đồng ngữ nghĩa cao (ví dụ: "AWS Lambda là gì?" và "Giải thích khái niệm AWS Lambda"), hệ thống sẽ trả về cùng một kết quả mà không cần gọi lại AI Model.

![Blog 3](/images/blog3.jpg)

---

 **Nguồn bài viết trên Facebook:** [AWS Study Group Post](https://www.facebook.com/groups/660548818043427)