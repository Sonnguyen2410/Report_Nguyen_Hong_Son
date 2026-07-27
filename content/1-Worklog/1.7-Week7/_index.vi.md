---
title: "Worklog Tuần 7"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---

## Chủ đề: Xây dựng giao diện làm bài Quiz trực tuyến, chấm điểm tự động và theo dõi tiến độ bài học

### 1. Mục tiêu tuần 7
* Xây dựng giao diện làm bài kiểm tra trực tuyến Quiz Runner UI hoàn chỉnh cho học viên, hỗ trợ đồng hồ đếm ngược thời gian, lưu trạng thái bài làm tạm thời và tự động nộp bài khi hết giờ.
* Phát triển Engine chấm điểm tự động (Auto-Grading Engine) trên Backend xử lý chính xác các dạng câu hỏi khác nhau (Trắc nghiệm, Đúng/Sai, Điền từ, Tự luận dựa trên AI).
* Xây dựng hệ thống theo dõi tiến độ học tập (Learning Progress Tracking) ghi nhận phần trăm hoàn thành bài học, lưu lịch sử lượt thi (`QuizAttempt`) và cấp chứng chỉ/trạng thái hoàn thành khóa học.
* Hoàn thiện các trang quản lý học viên: Trang tổng quan tiến độ (`DashboardPage.tsx`) và trang xem chi tiết kết quả bài kiểm tra.

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Quản lý State bài thi & Đồng bộ thời gian trên React Frontend
* **Kỹ thuật Quản lý State Bài thi Phức tạp:**
  * Quản lý mảng câu trả lời của học viên `userAnswers: Record<string, any>` để đảm bảo học viên có thể chuyển đổi qua lại giữa các câu hỏi mà không bị mất dữ liệu đã chọn.
  * Lưu State tạm thời vào `sessionStorage` hoặc `localStorage` phòng trường hợp học viên vô tình F5 lỡ tay làm mới trang trình duyệt.
* **Đồng bộ Thời gian & Tự động Nộp bài (Quiz Timer):**
  * Sử dụng React `useEffect` kết hợp `setInterval` để xây dựng đồng hồ đếm ngược thời gian thực (ví dụ: 15 phút, 45 phút).
  * Xử lý sự kiện tự động nộp bài: Khi `timeLeft === 0`, khóa toàn bộ các ô nhập liệu, hiển thị thông báo "Hết giờ làm bài" và kích hoạt hàm nộp bài lên Server.

#### B. Thuật toán Chấm điểm & Ghi nhận Tiến độ Học tập
* **Engine Chấm điểm Đa dạng Loại câu hỏi (Auto-Grading Logic):**
  * **Câu hỏi Trắc nghiệm (`multiple_choice`) & Đúng/Sai (`true_false`):** So sánh chính xác chuỗi ID/Value của đáp án học viên chọn với `correct_answer`.
  * **Câu hỏi Điền từ (`fill_in_blank`):** Loại bỏ khoảng trắng thừa, chuyển về chữ thường (`trim().toLowerCase()`) để so sánh khớp chuỗi.
  * **Câu hỏi Tự luận (`essay`):** Tích hợp OpenAI API chấm điểm tự động bằng cách so sánh câu trả lời của học viên với đáp án mẫu/ý chính, trả về điểm số từ 0 - 10 kèm lời nhận xét góp ý.
* **Cấu trúc Lưu trữ Tiến độ Học tập (MongoDB Models):**
  * `QuizAttempt.model.js`: Lưu `user_id`, `quiz_id`, `score` (điểm số), `passed` (Đạt/Không đạt dựa trên passing score), `answers` (chi tiết câu trả lời) và `duration_seconds` (thời gian làm bài).
  * `LessonProgress.model.js`: Đánh dấu bài học là `completed: true` khi học viên xem xong video hoặc vượt qua bài Quiz bắt buộc.
  * Cập nhật phần trăm tiến độ tổng thể của khóa học trong `Enrollment.model.js` (`progress_percentage = (completed_lessons / total_lessons) * 100`).

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Phát triển Backend API Chấm điểm & Ghi nhận Kết quả (`quiz-execution.service.js`):**
  * Viết API `POST /api/quizzes/:id/submit`:
    * **BƯỚC 1:** Tiếp nhận mảng câu trả lời `answers` từ học viên và `start_time`.
    * **BƯỚC 2:** Truy vấn dữ liệu bài Quiz từ MongoDB Atlas (bao gồm danh sách đáp án đúng `correct_answer`).
    * **BƯỚC 3:** Thực hiện tính toán điểm số tổng: Cộng điểm trực tiếp cho các câu hỏi Trắc nghiệm/Đúng-Sai/Điền từ đúng; (Nếu có tự luận) Gọi `ai-provider.service.js` để OpenAI chấm điểm và đưa ra nhận xét.
    * **BƯỚC 4:** Tạo một bản ghi mới trong `QuizAttempt.model.js`.
    * **BƯỚC 5:** Nếu điểm số $\ge$ `passing_score` (ví dụ 70%), tự động gọi `enrollment.service.js` để cập nhật trạng thái bài học thành hoàn thành và tính lại % tiến độ khóa học.
  * Viết API `GET /api/quizzes/:id/attempts` cho phép xem lại lịch sử các lần thi trước đó kèm đáp án chi tiết và lời giải thích.

* **Dựng Giao diện Làm bài Quiz Tương tác (`QuizPage.tsx`):**
  * Thiết kế giao diện làm bài chuyên nghiệp:
    * **Thanh Header:** Hiển thị tên bài Quiz, nút Nộp bài và Đồng hồ đếm ngược thời gian (Timer Badge).
    * **Cột bên trái:** Danh sách các câu hỏi (Question Grid) cho phép bấm nhảy nhanh đến từng câu, đổi màu đánh dấu các câu đã trả lời / chưa trả lời.
    * **Khung trung tâm:** Hiển thị nội dung câu hỏi (hỗ trợ KaTeX render công thức toán), danh sách các lựa chọn Radio button, Checkbox hoặc ô nhập văn bản.
    * **Màn hình Kết quả Thi (`QuizResultModal`):** Hiển thị ngay tổng điểm số, trạng thái ĐẠT / KHÔNG ĐẠT, thời gian làm bài và bảng phân tích chi tiết đúng/sai từng câu kèm giải thích.

* **Phát triển Trang Tổng quan Tiến độ Học tập (`DashboardPage.tsx`):**
  * Dựng màn hình Dashboard dành cho Học viên:
    * Danh sách các khóa học đang tham gia kèm thanh tiến độ (Progress Bar %).
    * Thống kê tổng số bài học đã hoàn thành, số bài Quiz đã vượt qua và điểm số trung bình.
    * Danh sách thông báo mới (`Notification.model.js`) về bài tập mới hoặc phản hồi thảo luận.

---

### 4. Kết quả đạt được (Deliverables)
* Giao diện `QuizPage.tsx` vận hành mượt mà, đồng hồ đếm ngược chạy chính xác, tự động khóa bài nộp khi hết giờ và lưu State chống mất dữ liệu khi F5.
* Engine chấm điểm trên Backend xử lý chính xác 100% logic chấm điểm tự động cho các loại câu hỏi, lưu vết đầy đủ trong `QuizAttempt`.
* Hệ thống theo dõi tiến độ bài học (`LessonProgress` & `Enrollment`) tự động cập nhật ngay lập tức khi học viên nộp bài thi thành công.
* Trang `DashboardPage.tsx` giúp học viên dễ dàng theo dõi lộ trình học tập cá nhân và xem lại kết quả các bài thi chi tiết.
