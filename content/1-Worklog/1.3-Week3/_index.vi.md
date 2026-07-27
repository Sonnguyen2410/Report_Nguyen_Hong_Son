---
title: "Worklog Tuần 3"
date: 2026-07-27
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

## Chủ đề: Phát triển Module Quiz, Trợ lý AI Assistant (Amazon Bedrock) & S3 Media Streaming

### 1. Mục tiêu tuần 3 (15/06/2026 – 19/06/2026)
* Phát triển module Thi Quiz trực tuyến (Tạo ngân hàng câu hỏi, chấm điểm tự động, lưu lịch sử bài thi).
* Tích hợp dịch vụ **Amazon Bedrock / Claude 3** xây dựng Trợ lý AI Tutor Assistant giải đáp thắc mắc cho học viên.
* Xây dựng cơ chế tải lên và phát video bài giảng qua **Amazon S3 Short-lived Presigned URLs** (PUT & GET).
* Đóng gói bộ dữ liệu mẫu (Seed Data) chuẩn bị cho công đoạn kết nối Frontend.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (15/06/2026)** | • Xây dựng Quiz Controller & Attempt Routes (`/api/quizzes`): Tạo đề thi trắc nghiệm, cấu hình thời gian làm bài.<br>• Viết thuật toán tự động chấm điểm bài thi (`QuizAttempt.service.js`) cho các dạng câu hỏi: Trắc nghiệm, Đúng/Sai, Điền từ. | • Module Thi Quiz & Chấm điểm tự động.<br>• Lưu lịch sử điểm số học viên. |
| **Thứ 3 (16/06/2026)** | • Cài đặt thư viện `@aws-sdk/client-bedrock-runtime`, nghiên cứu mô hình AI Anthropic Claude 3.<br>• Viết AI Controller (`/api/ai/chat`): Tiếp nhận câu hỏi học viên, gửi prompt chuẩn hóa tới Amazon Bedrock và nhận câu trả lời.<br>• Lưu lịch sử hội thoại vào Mongoose Collection `AIMessage`. | • Trợ lý AI Assistant phản hồi thông minh.<br>• Lưu lịch sử chat AI theo từng bài học. |
| **Thứ 4 (17/06/2026)** | • Nghiên cứu cơ chế bảo mật tệp truyền thông trên Amazon S3 không mở Public.<br>• Cài đặt `@aws-sdk/client-s3` & `@aws-sdk/s3-request-presigner` trên Backend.<br>• Viết API sinh Presigned PUT URL (up video/PDF 5 phút) và Presigned GET URL (xem video 15 phút). | • APIs Presigned PUT & GET URLs hoàn tất.<br>• Bảo mật dữ liệu video S3 tuyệt đối. |
| **Thứ 5 (18/06/2026)** | • Phát triển các APIs Thảo luận bài học (`CourseDiscussion`) và Thông báo hệ thống (`Notification`).<br>• Viết kịch bản `seedData.js` tự động tạo dữ liệu tài khoản mẫu (Student, Tutor, Admin) và 5 khóa học mẫu. | • Bộ APIs Thảo luận & Thông báo.<br>• Script Seed Data cơ sở dữ liệu mẫu. |
| **Thứ 6 (19/06/2026)** | • Kiểm thử toàn bộ chuỗi APIs bằng Postman Collection kết hợp sinh Presigned URLs.<br>• Đo đạc thời gian phản hồi của Amazon Bedrock AI và tối ưu hóa Prompt Template.<br>• Tham gia họp Review tuần 3 với Mentor. | • Hệ thống Backend 100% hoàn thiện APIs.<br>• Báo cáo tiến độ tuần 3 thành công. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **Amazon Bedrock (Generative AI on AWS):**
  * Mô hình trí tuệ nhân tạo tạo sinh Serverless và cách truy cập Foundation Models (Anthropic Claude 3).
  * Cách gọi Bedrock Runtime API với `@aws-sdk/client-bedrock-runtime` và tối ưu hóa Prompt Engineering.
* **Amazon S3 Presigned URLs Architecture:**
  * Nguyên lý sinh đường dẫn ủy quyền có thời hạn ngắn từ AWS SDK.
  * Ưu điểm của Presigned URL: Trình duyệt tải tệp trực tiếp lên S3 mà không đi qua máy chủ Backend, giúp tiết kiệm băng thông và CPU của EC2.

#### B. Phát triển Backend Nâng cao
* **Tự động hóa chấm điểm & Xử lý bất đồng bộ:**
  * Thuật toán so sánh đáp án Quiz và tính phần trăm điểm số đạt/không đạt.
  * Quản lý trạng thái bất đồng bộ `async/await` khi tương tác với các dịch vụ đám mây AWS.

---

### 4. Kết quả đạt được (Deliverables)
* Module Thi Quiz trực tuyến và hệ thống lưu vết điểm thi `QuizAttempt`.
* Trợ lý AI Assistant tích hợp Amazon Bedrock phản hồi chính xác.
* Bộ APIs sinh Presigned URLs an toàn cho Upload và Video Streaming từ Amazon S3.
* Kịch bản `seedData.js` sẵn sàng đổ dữ liệu thử nghiệm.
