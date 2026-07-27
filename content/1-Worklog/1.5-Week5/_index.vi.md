---
title: "Worklog Tuần 5"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

## Chủ đề: Tích hợp OpenAI API, xây dựng module trích xuất tài liệu PDF/Word và OCR Tiếng Việt

### 1. Mục tiêu tuần 5
* Tích hợp thành công OpenAI API (sử dụng SDK chính thức) vào Backend Node.js phục vụ các tính năng Trí tuệ Nhân tạo.
* Xây dựng pipeline xử lý tài liệu đa định dạng: Đọc và trích xuất nội dung văn bản thuần (plain text) từ file PDF, file Word (`.docx`) và tài liệu dạng ảnh scan bằng công nghệ OCR (Optical Character Recognition) hỗ trợ tiếng Việt.
* Thiết lập hệ thống lưu trữ chỉ mục tài liệu bài học (AI Document Indexing) vào cơ sở dữ liệu MongoDB Atlas làm ngữ cảnh nền tảng cho Trợ lý AI ở Tuần 6.
* Đảm bảo tính bảo mật và tối ưu hóa chi phí gọi API OpenAI (Rate Limiting, Timeout Handling & Prompt Structuring).

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Tích hợp OpenAI API & Kỹ thuật Prompt Engineering
* **Tổng quan về OpenAI API SDK:**
  * Tìm hiểu cách khởi tạo OpenAI Client trong Node.js bằng thư viện `openai`.
  * Lựa chọn Model tối ưu cho bài toán E-Learning: Sử dụng `gpt-4o` / `gpt-4o-mini` cho tốc độ xử lý nhanh, khả năng hiểu tiếng Việt tốt và chi phí hợp lý.
  * Phân biệt các tham số cấu hình Model: `temperature` (độ sáng tạo), `max_tokens` (giới hạn độ dài văn bản trả về), `top_p` và `response_format` (bắt buộc trả về định dạng `json_object` chuẩn xác).
* **Bảo mật và Quản lý Chi phí API:**
  * Đảm bảo API Key của OpenAI được bảo mật tuyệt đối trong biến môi trường `OPENAI_API_KEY` trên EC2 server, tuyệt đối không để lộ ra ngoài client React.
  * Xây dựng cơ chế **Timeout Handling:** Cấu hình thời gian chờ tối đa (Timeout 120 giây) phòng trường hợp mạng trễ hoặc file quá lớn làm treo request.
  * Thiết lập cơ chế bóc tách đoạn văn (Text Chunking/Truncation) để tránh vượt quá giới hạn Context Window của Model và giảm thiểu chi phí Token.

#### B. Các Công nghệ Trích xuất Văn bản & OCR
* **Trích xuất File Văn bản Tĩnh (PDF & Word):**
  * Sử dụng thư viện `pdf-parse`: Đọc luồng dữ liệu nhị phân (Buffer) của file PDF và trích xuất toàn bộ chuỗi ký tự text.
  * Sử dụng thư viện `mammoth`: Đọc file Word (`.docx`), chuyển đổi cấu trúc XML của Word thành văn bản thuần sạch ký tự rác.
* **Nhận dạng Ký tự Quang học OCR với Tesseract.js (Tiếng Việt):**
  * Khái niệm OCR: Kỹ thuật nhận diện chữ viết trong ảnh số thành dữ liệu văn bản có thể chỉnh sửa được.
  * Sử dụng thư viện `tesseract.js`: Cấu hình nhận diện với bộ dữ liệu ngôn ngữ tiếng Việt (`vie`) và tiếng Anh (`eng`).
  * Xử lý đa luồng với Tesseract Worker để không gây nghẽn sự kiện (Event Loop) trên Node.js server khi xử lý file ảnh dung lượng lớn.

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Cài đặt Thư viện và Cấu hình Môi trường Backend (`LearnSphere_BE`):**
  * Cài đặt các gói phụ thuộc xử lý file và AI:
    ```bash
    npm install openai pdf-parse mammoth tesseract.js @tesseract.js-data/vie
    ```
  * Cấu hình biến môi trường `OPENAI_API_KEY` và `AI_PROVIDER_TIMEOUT_MS=120000` trong file `.env`.

* **Xây dựng Pipeline Trích xuất Dữ liệu Tài liệu (`file-parser.service.js`):**
  * Phát triển module phát hiện định dạng file dựa trên MIME Type hoặc file extension:
    * Định dạng `application/pdf`: Gọi module `pdf-parse` để lấy `data.text`.
    * Định dạng `application/vnd.openxmlformats-officedocument.wordprocessingml.document` (`.docx`): Gọi `mammoth.extractRawText({ buffer })`.
    * Định dạng hình ảnh (`image/png`, `image/jpeg`): Tạo Tesseract Worker với ngôn ngữ `vie+eng`, thực hiện `worker.recognize(buffer)` để trích xuất văn bản từ ảnh scan.
  * Xây dựng hàm làm sạch dữ liệu văn bản (Text Sanitization): Loại bỏ khoảng trắng thừa, xóa ký tự đặc biệt không đọc được và chuẩn hóa mã Unicode tiếng Việt.

* **Xây dựng AI Provider Service (`ai-provider.service.js`) & API Indexing:**
  * Viết module đóng gói gọi OpenAI API:
    ```javascript
    import OpenAI from "openai";

    const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

    export const invokeOpenAI = async ({ systemPrompt, userPrompt, jsonMode = false }) => {
      const response = await openai.chat.completions.create({
        model: "gpt-4o-mini",
        messages: [
          { role: "system", content: systemPrompt },
          { role: "user", content: userPrompt }
        ],
        response_format: jsonMode -> { type: "json_object" } : undefined,
        temperature: 0.3,
      });
      return response.choices[0].message.content;
    };
    ```
  * Viết API `POST /api/lessons/:id/index-ai`: Giảng viên tải file bài giảng lên (PDF/Word/Ảnh), hệ thống tự động chạy Pipeline trích xuất văn bản và lưu chuỗi text vào trường `ai_indexed_content` trong `Lesson.model.js` trên MongoDB Atlas.

* **Cập nhật Dockerfile Backend cho Tesseract.js:**
  * Cập nhật file `Dockerfile` của Backend để cài đặt các gói phụ thuộc hệ thống Linux cần thiết cho Tesseract OCR (`pango`, `cairo`, `libpng`) để đảm bảo quá trình build Docker trên Amazon ECR chạy không bị lỗi.

---

### 4. Kết quả đạt được (Deliverables)
* Module `file-parser.service.js` đọc và trích xuất chính xác 100% nội dung văn bản từ các file PDF bài giảng, file Word `.docx` và ảnh tài liệu scan tiếng Việt.
* Module `ai-provider.service.js` kết nối thành công tới OpenAI API với model `gpt-4o-mini`, xử lý phản hồi nhanh chóng và quản lý lỗi timeout an toàn.
* Khai phá thành công tính năng AI Indexing: Giảng viên chỉ cần upload tài liệu bài học, hệ thống tự động bóc tách và lưu trữ dữ liệu văn bản vào MongoDB Atlas làm nguyên liệu đầu vào cho Trợ lý AI Tutor và Công cụ Tạo Quiz tự động ở Tuần 6.
