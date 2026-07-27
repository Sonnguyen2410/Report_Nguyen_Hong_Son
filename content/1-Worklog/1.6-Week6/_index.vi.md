---
title: "Worklog Tuần 6"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---

## Chủ đề: Phát triển Trợ lý học tập AI Tutor 24/7 và công cụ tạo bài kiểm tra Quiz tự động

### 1. Mục tiêu tuần 6
* Xây dựng tính năng Trợ lý học tập cá nhân hóa AI Tutor (Chatbot 24/7) cho phép học viên đặt câu hỏi và nhận câu trả lời chính xác dựa trên ngữ cảnh tài liệu bài học đã được trích xuất ở Tuần 5.
* Phát triển công cụ Tự động sinh bài kiểm tra Quiz bằng AI (AI Quiz Generator) hỗ trợ tạo đa dạng loại câu hỏi (Trắc nghiệm, Đúng/Sai, Điền từ, Tự luận) kèm đáp án và giải thích chi tiết.
* Xây dựng giao diện công cụ thiết kế câu hỏi Question Builder trên React Frontend cho phép Giảng viên xem trước, chỉnh sửa hoặc bổ sung câu hỏi do AI tạo ra.
* Đảm bảo chuẩn hóa phản hồi dữ liệu dạng JSON từ OpenAI API và lưu vết lịch sử trò chuyện AI trong MongoDB Atlas.

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến trúc Trợ lý AI Tutor theo Ngữ cảnh (Context-Aware AI Assistant)
* **Kỹ thuật RAG đơn giản hóa (Simple Retrieval-Augmented Generation):**
  * Hiểu cách hoạt động của AI Tutor: Khi học viên gửi thắc mắc trong một bài học, Backend sẽ lấy chuỗi văn bản đã trích xuất (`ai_indexed_content`) của bài học đó trong MongoDB Atlas để ghép vào System Prompt làm ngữ cảnh (Context).
  * Thiết lập Prompt kiểm soát hành vi (System Prompt Engineering): Ép buộc AI chỉ trả lời các câu hỏi nằm trong phạm vi kiến thức của bài học, trả lời lịch sự bằng tiếng Việt, giải thích dễ hiểu và từ chối các câu hỏi không liên quan đến bài học.
* **Quản lý Lịch sử Trò chuyện (Chat History Management):**
  * Cấu trúc lưu trữ hội thoại trong `AIMessage.model.js` (gồm `user_id`, `lesson_id`, `role`, `content`, `tokens_used`, `timestamp`).
  * Kỹ thuật gửi kèm N câu thoại gần nhất (Context Window History) để AI hiểu được ngữ cảnh các câu hỏi nối tiếp của học viên.

#### B. Công cụ Sinh Bài kiểm tra Quiz Tự động (AI Quiz Generator Engine)
* **Bắt buộc định dạng JSON chuẩn (Structured JSON Outputs):**
  * Sử dụng tính năng `response_format: { type: "json_object" }` của OpenAI API để yêu cầu Model trả về đúng cấu trúc JSON mong muốn mà không bị dính văn bản giải thích thừa.
  * Cấu hình cấu trúc JSON Schema cho bài kiểm tra:
    * `title`: Tiêu đề bài Quiz.
    * `questions`: Mảng chứa các câu hỏi.
    * `question_type`: Loại câu hỏi (`multiple_choice`, `true_false`, `fill_in_blank`, `essay`).
    * `question_text`: Nội dung câu hỏi.
    * `options`: Các lựa chọn (đối với trắc nghiệm).
    * `correct_answer`: Đáp án đúng.
    * `explanation`: Giải thích chi tiết tại sao đáp án đó đúng.
* **Kỹ thuật KaTeX Render Công thức Toán/Khoa học:**
  * Tìm hiểu thư viện `katex` và `@types/katex` trên React Frontend.
  * Hướng dẫn Prompt ép OpenAI trả về các công thức toán học dưới dạng chuẩn LaTeX (ví dụ `\( E = mc^2 \)` hoặc `\[ \int_0^\infty x^2 dx \]`) để Frontend hiển thị đẹp mắt.

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Phát triển Module Trợ lý AI Tutor (`ai-assistant.service.js`):**
  * Viết API `POST /api/ai/chat`:
    * **BƯỚC 1:** Tiếp nhận `lesson_id` và câu hỏi `message` từ học viên.
    * **BƯỚC 2:** Truy vấn MongoDB lấy dữ liệu `ai_indexed_content` của bài học và 5 tin nhắn gần nhất từ `AIMessage` collection.
    * **BƯỚC 3:** Xây dựng System Prompt ép ngữ cảnh bài học và gửi request tới OpenAI API (`gpt-4o-mini`).
    * **BƯỚC 4:** Lưu tin nhắn của User và phản hồi của AI vào MongoDB, trả kết quả về cho Frontend.
  * Dựng giao diện `AIAssistantPage.tsx` / Chat Drawer trên Frontend với hiệu ứng gõ phím, hiển thị công thức KaTeX và khung chat thời gian thực.

* **Phát triển Engine Sinh Quiz Tự động (`quiz-generator.service.js`):**
  * Viết API `POST /api/quizzes/generate-ai`:
    * Tiếp nhận các thông số từ Giảng viên: `lesson_id`, `num_questions` (số lượng câu hỏi), `difficulty` (Dễ/Trung bình/Khó), `question_types` (các dạng câu hỏi muốn tạo).
    * Đưa văn bản bài học và thông số vào System Prompt thiết kế sẵn cho OpenAI API.
    * Tiếp nhận chuỗi JSON từ OpenAI, kiểm tra tính hợp lệ của dữ liệu (JSON Validation) và lưu bài Quiz vào `Quiz.model.js` với trạng thái nháp (Draft).

* **Phát triển Giao diện Question Builder (`QuestionBuilderPage.tsx`):**
  * Dựng màn hình thiết kế bài kiểm tra dành cho Giảng viên:
    * Nút bấm "Tạo câu hỏi tự động bằng AI" kích hoạt modal chọn số lượng và độ khó.
    * Danh sách câu hỏi hiển thị dạng Cards tương tác cho phép Giảng viên trực tiếp chỉnh sửa nội dung câu hỏi, thêm/bớt đáp án, thay đổi đáp án đúng hoặc bổ sung thêm câu hỏi thủ công.
    * Nút "Xuất bản bài Quiz" (Publish) để chính thức cho phép sinh viên vào làm bài.

---

### 4. Kết quả đạt được (Deliverables)
* Tính năng Trợ lý AI Tutor chạy ổn định, trả lời câu hỏi chính xác theo đúng ngữ cảnh tài liệu bài học, hiển thị mượt mà trên giao diện Chat của Frontend.
* Engine sinh Quiz bằng OpenAI API tạo ra các bài kiểm tra chất lượng cao, đúng cấu trúc JSON, hỗ trợ hiển thị công thức toán học LaTeX bằng KaTeX.
* Trang `QuestionBuilderPage.tsx` hoàn thiện giúp Giảng viên tiết kiệm 80% thời gian biên soạn bài tập, kết hợp linh hoạt giữa trí tuệ nhân tạo và sự tùy chỉnh của con người.
