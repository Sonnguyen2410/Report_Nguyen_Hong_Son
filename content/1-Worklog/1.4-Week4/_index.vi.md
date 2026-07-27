---
title: "Worklog Tuần 4"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

## Chủ đề: Xây dựng Giao diện Frontend React, State Management & Tích hợp APIs

### 1. Mục tiêu tuần 4
* Khởi tạo dự án Frontend `LearnSphere_FE` với Vite, React.js, TypeScript và TailwindCSS.
* Cấu hình hệ thống điều hướng SPA với React Router v6 và Axios Interceptors quản lý JWT Token.
* Phát triển toàn bộ Giao diện người dùng cho Giáo viên (Quản lý khóa học, upload media) và Học viên (Học tập, thi Quiz, chat AI).
* Tích hợp thành công dữ liệu phản hồi từ Backend và hoàn thiện ứng dụng Single Page Application mượt mà.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (06/07/2026)** | • Khởi tạo dự án `LearnSphere_FE` bằng `vite` với template TypeScript.<br>• Cài đặt các thư viện: `react-router-dom`, `axios`, `@tanstack/react-query`, `lucide-react`, `tailwindcss`.<br>• Cấu hình Axios Client (`api.js`) tự động đính kèm Token `Authorization: Bearer <token>` và xử lý tự động logout khi Token hết hạn (401). | • Khung ứng dụng React Vite TypeScript.<br>• Axios Interceptors quản lý JWT. |
| **Thứ 3 (07/07/2026)** | • Xây dựng bộ UI Components dùng chung: `Navbar`, `Sidebar`, `Modal`, `Button`, `LoadingSpinner`, `ToastNotification`.<br>• Phát triển Giao diện Đăng ký / Đăng nhập (`LoginPage.tsx` & `RegisterPage.tsx`) với validation form mãnh liệt. | • Hệ thống UI Components chuẩn hóa.<br>• Trang Auth Login/Register hoàn thiện. |
| **Thứ 4 (08/07/2026)** | • Xây dựng Trang Dashboard Giáo viên (`TutorDashboard.tsx`): Danh sách khóa học, form Tạo khóa học mới.<br>• Tích hợp luồng upload video/PDF: Frontend nhận Presigned PUT URL từ Backend và thực hiện HTTP PUT trực tiếp lên S3 Media Bucket kèm thanh tiến trình (Upload Progress Bar). | • Giao diện Quản lý Khóa học Giáo viên.<br>• Trình tải tệp S3 Presigned URL trực tiếp. |
| **Thứ 5 (09/07/2026)** | • Xây dựng Giao diện Học tập cho Học viên (`LessonViewer.tsx`): Trình phát Video HTML5 kết hợp đường dẫn Presigned GET URL từ S3.<br>• Xây dựng Giao diện Thi Quiz (`QuizRunner.tsx`) đếm ngược thời gian và Giao diện Cửa sổ Chat Trợ lý AI (`AITutorChat.tsx`). | • Trang Xem Bài học & Stream Video S3.<br>• Giao diện Thi Quiz & Cửa sổ Chat AI. |
| **Thứ 6 (10/07/2026)** | • Tích hợp React Query quản lý Caching dữ liệu khóa học và tự động Re-fetch khi có thay đổi.<br>• Kiểm thử toàn bộ luồng trải nghiệm người dùng End-to-End từ local.<br>• Tham gia họp Demo sản phẩm Frontend tuần 4 với Mentor. | • Ứng dụng SPA Frontend mượt mà.<br>• Báo cáo Demo tuần 4 thành công. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **S3 CORS (Cross-Origin Resource Sharing):**
  * Hiểu rõ tại sao trình duyệt bị chặn CORS khi thực hiện `PUT` hoặc `GET` tới Amazon S3.
  * Cấu hình XML/JSON CORS Rules trên S3 Bucket cho phép các HTTP Methods (`GET`, `PUT`, `HEAD`) và Headers (`*`, `ETag`).

#### B. Phát triển Frontend React & TypeScript
* **Vite & React Ecosystem:**
  * Xây dựng Single Page Application với tốc độ Hot Module Replacement (HMR) cực nhanh của Vite.
  * Quản lý trạng thái bất đồng bộ nâng cao với **React Query (TanStack Query)**.
* **Direct Browser-to-S3 Upload Progress:**
  * Sử dụng Axios `onUploadProgress` callback để theo dõi chính xác phần trăm tệp video/PDF được tải trực tiếp từ trình duyệt lên S3.

---

### 4. Kết quả đạt được (Deliverables)
* Ứng dụng Frontend React Single Page Application hoàn chỉnh trong thư mục `LearnSphere_FE`.
* Luồng tải lên tệp truyền thông qua Presigned PUT URL trực tiếp tới S3 với thanh tiến trình mượt mà.
* Trình phát video bài giảng bảo mật qua Presigned GET URL.
* Giao diện Thi Quiz đếm ngược và cửa sổ nhắn tin với Trợ lý AI Tutor.
